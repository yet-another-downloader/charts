# Helm charts for yad

Use Helm 3

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

## Delete

If you want to delete

```bash
helm delete $RELEASE_BACKEND_NAME  --namespace $NAMESPACE
helm delete $RELEASE_FRONTEND_NAME --namespace $NAMESPACE
```