
## all in one
while ! kustomize build example | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done

## 1. cert-manager

kustomize build common/cert-manager/cert-manager/base | kubectl apply -f -


### 삭제
kustomize build common/cert-manager/cert-manager/base | kubectl delete -f -

### 확인

kubectl get pod -n cert-manager

NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-7dd5854bb4-7nmpd              1/1     Running   0          2m10s
cert-manager-cainjector-64c949654c-2scxr   1/1     Running   0          2m10s
cert-manager-webhook-6b57b9b886-7q6g2      1/1     Running   0          2m10s


kustomize build common/cert-manager/kubeflow-issuer/base | kubectl apply -f -

여러번 

## 2. Istio

kustomize build common/istio-1-17/istio-crds/base | kubectl apply -f -
kustomize build common/istio-1-17/istio-namespace/base | kubectl apply -f -
kustomize build common/istio-1-17/istio-install/base | kubectl apply -f -

### 삭제
kustomize build common/istio-1-17/istio-crds/base | kubectl delete -f -
kustomize build common/istio-1-17/istio-namespace/base | kubectl delete -f -
kustomize build common/istio-1-17/istio-install/base | kubectl delete -f -

### 확인

kubectl get po -n istio-system

NAME                                   READY   STATUS    RESTARTS   AGE
istio-ingressgateway-79b665c95-xm22l   1/1     Running   0          16s
istiod-86457659bb-5h58w                1/1     Running   0          16s



## 제외-- 3. Dex
kustomize build common/dex/overlays/istio | kubectl apply -f -

### 삭제 
kustomize build common/dex/overlays/istio | kubectl delete -f -
## keycloak


## 4. OIDC AuthService

kustomize build common/oidc-client/oidc-authservice/base | kubectl apply -f -

### 삭제
kustomize build common/oidc-client/oidc-authservice/base | kubectl delete -f -

### 확인
kubectl get po -n istio-system -w

NAME                                   READY   STATUS    RESTARTS   AGE
authservice-0                          1/1     Running   0          14s
istio-ingressgateway-79b665c95-xm22l   1/1     Running   0          2m37s
istiod-86457659bb-5h58w                1/1     Running   0          2m37s


## 5. kubeflow namespace

kustomize build common/kubeflow-namespace/base | kubectl apply -f -

### 삭제
kustomize build common/kubeflow-namespace/base | kubectl delete -f -


## 6. kubeflow Roles

kustomize build common/kubeflow-roles/base | kubectl apply -f -

### 삭제
kustomize build common/kubeflow-roles/base | kubectl delete -f -

### 확인
kubectl get clusterrole | grep kubeflow


## 7. Kubeflow Istio Resources

kustomize build common/istio-1-17/kubeflow-istio-resources/base | kubectl apply -f -

### 삭제
kustomize build common/istio-1-17/kubeflow-istio-resources/base | kubectl delete -f -

### 확인
kubectl get clusterrole | grep kubeflow-istio

kubeflow-istio-admin                                                   2021-12-03T08:53:17Z
kubeflow-istio-edit                                                    2021-12-03T08:53:17Z
kubeflow-istio-view                                                    2021-12-03T08:53:17Z

kubectl get gateway -n kubeflow

NAME               AGE
kubeflow-gateway   31s


## 8. kubeflow Pipelines

kustomize build apps/pipeline/upstream/env/platform-agnostic-multi-user | kubectl apply -f -

여러번 반복

### 삭제
kustomize build apps/pipeline/upstream/env/platform-agnostic-multi-user | kubectl delete -f -

### 확인

kubectl get po -n kubeflow

NAME                                                     READY   STATUS    RESTARTS   AGE
cache-deployer-deployment-79fdf9c5c9-bjnbg               2/2     Running   1          5m3s
cache-server-5bdf4f4457-48gbp                            2/2     Running   0          5m3s
kubeflow-pipelines-profile-controller-7b947f4748-8d26b   1/1     Running   0          5m3s
metacontroller-0                                         1/1     Running   0          5m3s
metadata-envoy-deployment-5b4856dd5-xtlkd                1/1     Running   0          5m3s
metadata-grpc-deployment-6b5685488-kwvv7                 2/2     Running   3          5m3s
metadata-writer-548bd879bb-zjkcn                         2/2     Running   1          5m3s
minio-5b65df66c9-k5gzg                                   2/2     Running   0          5m3s
ml-pipeline-8c4b99589-85jw6                              2/2     Running   1          5m3s
ml-pipeline-persistenceagent-d6bdc77bd-ssxrv             2/2     Running   0          5m3s
ml-pipeline-scheduledworkflow-5db54d75c5-zk2cw           2/2     Running   0          5m2s
ml-pipeline-ui-5bd8d6dc84-j7wqr                          2/2     Running   0          5m2s
ml-pipeline-viewer-crd-68fb5f4d58-mbcbg                  2/2     Running   1          5m2s
ml-pipeline-visualizationserver-8476b5c645-wljfm         2/2     Running   0          5m2s
mysql-f7b9b7dd4-xfnw4                                    2/2     Running   0          5m2s
workflow-controller-5cbbb49bd8-5zrwx                     2/2     Running   1          5m2s

### 참고
kubectl port-forward svc/ml-pipeline-ui -n kubeflow 8888:80

로 ui 조회 8888포트



## 9. 제외--katib

kustomize build apps/katib/upstream/installs/katib-with-kubeflow | kubectl apply -f -


### 확인
kubectl get po -n kubeflow | grep katib

katib-controller-68c47fbf8b-b985z                        1/1     Running   0          82s
katib-db-manager-6c948b6b76-2d9gr                        1/1     Running   0          82s
katib-mysql-7894994f88-scs62                             1/1     Running   0          82s
katib-ui-64bb96d5bf-d89kp                                1/1     Running   0          82s

### 참고 ui
kubectl port-forward svc/katib-ui -n kubeflow 8081:80


## 10. Central Dashboard

kustomize build apps/centraldashboard/upstream/overlays/istio | kubectl apply -f -

### 삭제
kustomize build apps/centraldashboard/upstream/overlays/istio | kubectl delete -f -

### 확인
kubectl get po -n kubeflow | grep centraldashboard

centraldashboard-8fc7d8cc-xl7ts                          1/1     Running   0          52s

### 참고 ui
kubectl port-forward svc/centraldashboard -n kubeflow 8082:8


## 11. Admission webhook

kustomize build apps/admission-webhook/upstream/overlays/cert-manager | kubectl apply -f -

### 삭제
kustomize build apps/admission-webhook/upstream/overlays/cert-manager | kubectl delete -f -

### 확인
kubectl get po -n kubeflow | grep admission-webhook

admission-webhook-deployment-667bd68d94-2hhrx            1/1     Running   0          11s


## 12. Notebooks & jupyter web app

kustomize build apps/jupyter/notebook-controller/upstream/overlays/kubeflow | kubectl apply -f -
kustomize build apps/jupyter/jupyter-web-app/upstream/overlays/istio | kubectl apply -f -

### 삭제
kustomize build apps/jupyter/notebook-controller/upstream/overlays/kubeflow | kubectl delete -f -
kustomize build apps/jupyter/jupyter-web-app/upstream/overlays/istio | kubectl delete -f -

### 확인
kubectl get po -n kubeflow | grep notebook-controller

notebook-controller-deployment-75b4f7b578-w4d4l          1/1     Running   0          105s

kubectl get po -n kubeflow | grep jupyter-w

jupyter-web-app-deployment-6f744fbc54-p27ts              1/1     Running   0          2m


## 13. profile + KFAM

kustomize build apps/profiles/upstream/overlays/kubeflow | kubectl apply -f -

### 삭제
kustomize build apps/profiles/upstream/overlays/kubeflow | kubectl delete -f -

### 확인

kubectl get po -n kubeflow | grep profiles-deployment

profiles-deployment-89f7d88b-qsnrd                       2/2     Running   0          42s


##  PVC Viewer Controller
kustomize build apps/pvcviewer-controller/upstream/default | kubectl apply -f -

### 삭제
kustomize build apps/pvcviewer-controller/upstream/default | kubectl delete -f -

### 확인
pod/pvcviewer-controller-manager-85bc664bd6-qbd92   3/3     Running   1 (36d ago)   36d


## 14. volumes web app

kustomize build apps/volumes-web-app/upstream/overlays/istio | kubectl apply -f -

### 삭제
kustomize build apps/volumes-web-app/upstream/overlays/istio | kubectl delete -f -


### 확인
kubectl get po -n kubeflow | grep volumes-web-app

volumes-web-app-deployment-8589d664cc-62svl              1/1     Running   0          27


## 15. 제외--tensorboard & tensorboard web app

kustomize build apps/tensorboard/tensorboards-web-app/upstream/overlays/istio | kubectl apply -f -
kustomize build apps/tensorboard/tensorboard-controller/upstream/overlays/kubeflow | kubectl apply -f -

### 확인

kubectl get po -n kubeflow | grep tensorboards-web-app

tensorboards-web-app-deployment-6ff79b7f44-qbzmw            1/1     Running             0          22s

kubectl get po -n kubeflow | grep tensorboard-controller

tensorboard-controller-controller-manager-954b7c544-vjpzj   3/3     Running   1          73s


## 16. 제외--Training Operator

kustomize build apps/training-operator/upstream/overlays/kubeflow | kubectl apply -f -

### 확인

kubectl get po -n kubeflow | grep training-operator

training-operator-7d98f9dd88-6887f                          1/1     Running   0          28s


## 17. User Namespace

kustomize build common/user-namespace/base | kubectl apply -f -

### 삭제
kustomize build common/user-namespace/base | kubectl delete -f -