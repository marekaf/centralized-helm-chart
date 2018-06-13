# centralized-helm-chart
Centralized helm chart microservice template deployed to GCS w/ gitlab-ci

When deploying a lot of similar workload to kubernetes, it is convenient to use helm for templating and release management.

This repo implements a kubernetes-builder image with its dependencies (google-cloud-sdk with kubectl, helm, ...) and 
automatically packages a helm chart and uploads it to Google Cloud Storage bucket.  

Each microservice has its own gitlab repo and `values.yaml`. In its own `.gitlab-ci.yml` you can do something like this:
```
deploy-devel:
    stage: deploy-dev
...
    script:
    - cd build
    - helm init --upgrade
    - helm repo add my-helm-chart https://my-helm-charts.storage.googleapis.com/
    - helm repo list | grep my-helm-chart
    - |
      helm upgrade --install --namespace=$NAMESPACE --debug "$APP_NAME"-devel -f helm/values.yaml \
        --set image.repository=$IMAGE_REPO \
        --set image.tag=$IMAGE_TAG \
        --set namespace=$NAMESPACE \
        --set env.sqlUser=$SQL_USER \
        --set env.sqlPassword=$SQL_PASSWORD \
        --set env.sqlName=$SQL_NAME \
        --set env.host=$URL_HOST \
        my-helm-chart/my-service

... and other stages with their namespaces and so on...
```

check `.gitlab-ci.yml` to see how the helm chart is packaged and deployed to GCS

don't forget to set the proper GITLAB_VARIABLES
