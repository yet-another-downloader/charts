# Helm charts for yad

## Requirements

 - Helm 3
 - Kubernetes cluster
 - Deployed MongoDB
 - Generated api key for youtube
 - (Optional) Samba NAS

## Deploy

### Backend

1. Create namespace
2. Deploy

```bash
export NAMESPACE=yad-prod
export RELEASE_BACKEND_NAME=gateway
export YAD_BACKEND_BUILD=66

kubectl create ns $NAMESPACE

helm upgrade -i --namespace ${NAMESPACE} \
 --atomic \
 --set "image.build=$YAD_BACKEND_BUILD" \
 --set "yad.youtube.apiKey=a123456" \
 --set "yad.database.uri=mongodb://root:somePassword@mongo.example.com:27017/admin" \
 --set 'yad.cifs.params=username=someUsername\,password=somePassword\,iocharset=utf8 //nas.example.com/path-location' \
 --debug \
 --wait \
 $RELEASE_BACKEND_NAME \
 ./yad-backend
```

### Deploy frontend

```bash
export YAD_FRONTEND_BUILD=28
export RELEASE_FRONTEND_NAME=frontend

helm upgrade -i --namespace ${NAMESPACE} \
 --atomic \
 --set "image.build=$YAD_FRONTEND_BUILD" \
 --set "yad.gateway.url=$RELEASE_BACKEND_NAME-yad-backend.$NAMESPACE" \
 --debug \
 --wait \
 $RELEASE_FRONTEND_NAME \
 ./yad-frontend
```

### Check status

```bash
helm ls -A
```

Output should be like that:

```bash
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
frontend        yad-prod        4               2020-07-23 22:12:25.3658057 +0300 +0300 deployed        yad-frontend-0.1.0      1.16.0
gateway         yad-prod        3               2020-07-23 22:13:00.0156845 +0300 +0300 deployed        yad-backend-0.1.0       1.16.0
```

## Delete

If you want to delete

```bash
helm delete $RELEASE_BACKEND_NAME  --namespace $NAMESPACE
helm delete $RELEASE_FRONTEND_NAME --namespace $NAMESPACE
```