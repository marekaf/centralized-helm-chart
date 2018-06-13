FROM docker:stable

# add dependencies
RUN apk add --update make ca-certificates openssl python curl bash tree && \
    update-ca-certificates
    
# Write our GCP service account private key into a file
ADD ./gcloud-service-key.json /gcloud-service-key.json
WORKDIR /
RUN cd / && wget https://dl.google.com/dl/cloudsdk/release/google-cloud-sdk.tar.gz && \
    tar zxvf google-cloud-sdk.tar.gz && \
    ./google-cloud-sdk/install.sh --usage-reporting=false --path-update=true

# add gcloud to PATH
ENV PATH="/google-cloud-sdk/bin/:${PATH}"

# update and auth
RUN gcloud --quiet components update && \
    gcloud components install kubectl && \
    gcloud auth activate-service-account --key-file /gcloud-service-key.json && \
    gcloud auth configure-docker

# install helm
RUN curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
