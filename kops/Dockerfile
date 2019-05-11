FROM alpine:latest

WORKDIR /k8s

RUN apk add --no-cache --update python python-dev py-pip \
        curl libffi-dev openssl-dev build-base ca-certificates

COPY requirements.txt /k8s
RUN pip install --upgrade pip && pip install -r /k8s/requirements.txt

RUN curl -Lo /usr/local/bin/kops https://github.com/kubernetes/kops/releases/download/1.10.0/kops-linux-amd64
RUN chmod +x /usr/local/bin/kops

RUN curl -Lo /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/v1.10.6/bin/linux/amd64/kubectl
RUN chmod +x /usr/local/bin/kubectl

RUN curl -Lo /tmp/helm.tar.gz https://storage.googleapis.com/kubernetes-helm/helm-v2.10.0-linux-amd64.tar.gz
RUN tar -zxf /tmp/helm.tar.gz
RUN mv linux-amd64/helm /usr/local/bin/helm

ENTRYPOINT ["ansible-playbook"]
