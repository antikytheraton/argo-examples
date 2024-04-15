

# Follow this video to be a ArgoCD Boss
https://youtu.be/JLrR9RV9AFA


# Installing latest/stable version of ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Forward Ports
```
k get services -n argocd
kubectl port-forward service/argocd-server -n argocd 8080:443
```

### Get Credentials
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

# Install ArgoCD CLI / Login via CLI
```
brew install argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443
argocd login 127.0.0.1:8080
```

# Creating an Application using ArgoCD CLI:
```
argocd app create webapp-kustom-prod \
--repo https://github.com/devopsjourney1/argo-examples.git \
--path kustom-webapp/overlays/prod --dest-server https://kubernetes.default.svc \
--dest-namespace prod
```

# Command Cheat sheet
```
argocd app create #Create a new Argo CD application.
argocd app list #List all applications in Argo CD.
argocd app logs <appname> #Get the application’s log output.
argocd app get <appname> #Get information about an Argo CD application.
argocd app diff <appname> #Compare the application’s configuration to its source repository.
argocd app sync <appname> #Synchronize the application with its source repository.
argocd app history <appname> #Get information about an Argo CD application.
argocd app rollback <appname> #Rollback to a previous version
argocd app set <appname> #Set the application’s configuration.
argocd app delete <appname> #Delete an Argo CD application.
```


----------------------------------------------------------------------------------------------


# ArgoCD tutorial

- Create cluster
```bash
colima kubernetes start --profile aarch64
```

- Create namespace
```bash
kubectl create namespace argocd
```

- Apply chart to cluster
```bash
curl -O https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -n argocd -f install.yaml
# for any error, recreate all resources
kubectl delete -n argocd -f install.yaml
```

- Get cluster info
```bash
kubectl get all -n argocd
```

- Login to argocd
```bash
# expose argocd server on localhost
kubectl port-forward service/argocd-server -n argocd 8080:443
# pull password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
# default user: admin
```

- Create + NEW APP - HELM
    - general
        - app_name: helm-webapp-dev
        - proj_name: default
        - sync: manual
        - sync opts: auto-create-namespace
    - source
        - source: https://github.com/antikytheraton/argo-examples
        - path: helm-webapp
    - destination:
        - cluster_url: https://kubernetes.default.svc
        - namespace: dev
- Sync

- Get cluster info
```bash
kubectl get all -n dev

#NAME                             READY   STATUS    RESTARTS   AGE
#pod/myhelmapp-5b58c48bb8-gdq6q   1/1     Running   0          3m46s
#pod/myhelmapp-5b58c48bb8-j74pb   1/1     Running   0          3m46s
#pod/myhelmapp-5b58c48bb8-zkfkk   1/1     Running   0          3m46s
#pod/myhelmapp-5b58c48bb8-lmkjz   1/1     Running   0          3m46s
#pod/myhelmapp-5b58c48bb8-n7p78   1/1     Running   0          3m46s
#
#NAME                TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
#service/myhelmapp   NodePort   10.43.156.216   <none>        80:30035/TCP   3m46s
#
#NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
#deployment.apps/myhelmapp   5/5     5            5           3m46s
#
#NAME                                   DESIRED   CURRENT   READY   AGE
#replicaset.apps/myhelmapp-5b58c48bb8   5         5         5       3m46s

kubectl port-forward service/myhelmapp -n dev 8888:80
```

- Create + NEW APP - Kustomize
    - general
        - app_name: kustom-webapp-dev
        - proj_name: default
        - sync: manual
        - sync opts: auto-create-namespace
    - source
        - source: https://github.com/antikytheraton/argo-examples
        - path: kustom-webapp/overlays/dev
    - destination:
        - cluster_url: https://kubernetes.default.svc
        - namespace: dev

- ArgoCD CLI

```bash
argocd app create helm-webapp-dev \
    --repo https://github.com/antikytheraton/argo-examples.git \
    --path helm-webapp --dest-server https://kubernetes.default.svc \
    --dest-namespace dev
```

```bash
argocd app create webapp-kustom-dev \
    --repo https://github.com/antikytheraton/argo-examples.git \
    --path kustom-webapp/overlays/dev --dest-server https://kubernetes.default.svc \
    --dest-namespace dev
```

```bash
argocd app create webapp-kustom-prod \
    --repo https://github.com/antikytheraton/argo-examples.git \
    --path kustom-webapp/overlays/prod --dest-server https://kubernetes.default.svc \
    --dest-namespace prod
```

```bash
argocd app list
argocd app sync argocd/webapp-kustom-prod
argocd app diff argocd/webapp-kustom-prod
argocd app history argocd/webapp-kustom-prod
argocd app history argocd/webapp-kustom-prod
```



