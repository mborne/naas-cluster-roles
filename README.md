# naas-cluster-roles

> ARCHIVE (porté et complété sous forme de script terraform)

## Description

Définition **expérimentale**  de [ClusterRole Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) pour faciliter la mise à disposition d'une instance Kubernetes en mode "Namespace as a Service".

## Liste des rôles

| Nom                                      | Description                                                                                            | Cible RBAC |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------ | ---------- |
| [naas-user](manifest/naas-user.yaml)     | Rôle visant à permet la découverte des ressources globales du pour tous les utilisateurs authentifiés. | Cluster    |
| [naas-appops](manifest/naas-appops.yaml) | Rôle "AppOps" visant à donner les accès en lecture/écriture sur un namespace pour le déploiement d'une application.                          | Namespace  |
| [naas-appdev](manifest/naas-appdev.yaml) | Rôle "AppDev" visant à donner les accès en lecture sur un namespace pour le debug d'une application                                   | Namespace  |

## Utilisation

### Chargement des rôles

```bash
kubectl apply -k https://github.com/mborne/cluster-roles/manifest/
```

### Affectation des rôles

Quelques exemples à paufiner en supposant une authentification OIDC (par exemple basée sur Keycloak) pour l'accès au cluster :

* Affectation du rôle [naas-user](manifest/naas-user.yaml) à tous les utilisateurs authentifiés :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
    name: rbac-naas-user
roleRef:
    kind: ClusterRole
    name: naas-user
    apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  name: system:authenticated
  apiGroup: rbac.authorization.k8s.io
```


* Affectation du rôle [naas-appops](manifest/naas-appops.yaml) sur un namespace **hello** pour les membres du groupe `hello_admins` avec authentification OIDC :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
    name: rbac-appops
    namespace: hello
roleRef:
    kind: ClusterRole
    name: naas-appops
    apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  name: oidc:hello_admins
  apiGroup: rbac.authorization.k8s.io
```

* Affectation du rôle [naas-appdev](manifest/naas-appdev.yaml) sur un namespace **hello** pour les membres du groupe `hello_devs` avec authentification OIDC :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
    name: rbac-appdev
    namespace: hello
roleRef:
    kind: ClusterRole
    name: naas-appdev
    apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  name: oidc:hello_devs
  apiGroup: rbac.authorization.k8s.io
```


## Licence

[MIT](LICENSE)




