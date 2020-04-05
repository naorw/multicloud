FROM mesosphere/aws-cli
MAINTAINER @naorw
RUN apk --no-cache add openssl \ 
&& apk --no-cache add curl \
&& curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl \
&& mv kubectl /usr/local/bin/kubectl \
&& chmod +x /usr/local/bin/kubectl

WORKDIR /tmp
