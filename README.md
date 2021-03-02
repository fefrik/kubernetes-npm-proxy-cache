# Base image is used mainly for electron builds
``` shellsession
Image: electronuserland/builder:wine
npm 6.14.6
node 12.18.3
```

# kubernetes-npm-proxy-cache

Make `npm install` much faster on your kubernetes deployment, by
deploying a small internal proxy cache for your deployment.

This has been hacked together to make our Jenkins/Gitlab/Gocd (CI) build faster.



## How to use it

First, deploy the proxy cache on your kubernetes cluster:

``` shellsession
$> kubectl apply -f https://github.com/fefrik/kubernetes-npm-proxy-cache/raw/master/npm-proxy-cache.yml
deployment "npm-proxy-cache-deployment" created
service "npm-proxy-cache-service" created
```

Then, anywhere in your Kubernetes cluster:

``` shellsession
$> npm config set proxy http://npm-proxy-cache-service
$> npm config set https-proxy http://npm-proxy-cache-service
$> npm config set strict-ssl false
$> npm install
```

## In case you need to install local registry in your own already running docker


## Local Registry
Set the local registry first using this command
``` shellsession
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```
## Image Tag
Given a Dockerfile, the image could be built and tagged this easy way:
``` shellsession
docker build -t localhost:5000/my-image
```
## Image Pull Policy
the field imagePullPolicy should then be changed to get the right image from the right repo.

given this sample pod template

``` shellsession
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
    - name: app
      image: localhost:5000/my-image
      imagePullPolicy: Never
```

## Deploy Pod
The pod can be deployed using:

kubectl create -f pod.yml

Hope this comes in handy :)
