# Lab 3: Managing OpenShift applications

After creating an OpenShift application as instructed in the [previous lab (Lab 2)](../Lab2/README.md), the next step is ensuring it is up and running and finding out how to access it. 

## 0. Preparation

RBAC (Role Base Access Control) comes by defauly with Openshift, the user that we use via oc cli tool , need to have the right permissions in order to create projects, applicaitons, etc.. For this lab, we're going to use user _developer_. First of all and to make it short and simple, we will grant system:admin rights to user _developer_ by using the following commmands. 

```
$ minishift addons install --defaults
$ minishift addons enable cluster-admin
$ oc adm policy  --as system:admin add-cluster-role-to-user cluster-admin developer
```

now, try to login via `oc login`  by using user _developer_

```
$ oc login
Authentication required for https://192.168.99.100:8443 (openshift)
Username: developer
Password:
Login successful.

You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>
```

next is to create a new project "nodejs-ex"

```
oc new-project nodejs-ex --display-name="nodejs" --description="Sample Node.js ex app"
```

## 1. Monitoring builds

When we run `oc new-app` a build is triggered similar to a `docker build`. Unlike `docker build` however, the build happens in the background and we don't get the output of what is happening. 

To see this output, we can run:

```console
$ oc status
```

And create a new applicaiton by specifying the source code that we have already have them on github repo:

```
$ oc new-app https://github.com/sclorg/nodejs-ex -l name=myapp
warning: Cannot check if git requires authentication.
--> Found image 93de123 (14 months old) in image stream "openshift/nodejs" under tag "10" for "nodejs"

    Node.js 10.12.0
    ---------------
    Node.js  available as docker container is a base platform for building and running various Node.js  applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

    Tags: builder, nodejs, nodejs-10.12.0

    * The source repository appears to match: nodejs
    * A source build using source code from https://github.com/sclorg/nodejs-ex will be created
      * The resulting image will be pushed to image stream tag "nodejs-ex:latest"
      * Use 'start-build' to trigger a new build
      * WARNING: this source repository may require credentials.
                 Create a secret with your git credentials and use 'set build-secret' to assign it to the build config.
    * This image will be deployed in deployment config "nodejs-ex"
    * Port 8080/tcp will be load balanced by service "nodejs-ex"
      * Other containers can access this service through the hostname "nodejs-ex"

--> Creating resources with label name=myapp ...
    imagestream.image.openshift.io "nodejs-ex" created
    buildconfig.build.openshift.io "nodejs-ex" created
    deploymentconfig.apps.openshift.io "nodejs-ex" created
    service "nodejs-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/nodejs-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/nodejs-ex'
    Run 'oc status' to view your app.
```

Then to check the status:

```console
$ oc status
In project nodejs (nodejs-echo) on server https://192.168.64.11:8443

svc/nodejs-ex - 172.30.6.48:8080
  dc/nodejs-ex deploys istag/nodejs-ex:latest <-
    bc/nodejs-ex source builds https://github.com/sclorg/nodejs-ex on openshift/nodejs:10
      build #1 running for 36 hours
    deployment #1 waiting on image or update


2 infos identified, use 'oc status --suggest' to see details.
```

Looking at the output, we can see that we have a service endpoint to access our node app at `172.30.6.48:8080`. We can also see the build type used to create our application: `openshift/nodejs:10`. Finally, we can see in the last line that our application is not actually ready for usage as it is waiting for thr image to be deployed. In the line above, we can use the build number to get a more comprehensive idea of what is happening in the build.

If you wanted to check the build progress of a application, it would follow this format:
```
$ oc logs build/<app-name>-<build-no>
```

In this example, we would use the application name `nodejs-ex` and the build number `1`:
```
$ oc logs build/nodejs-ex-1
```

## 2. Monitoring deployments

In Kubernetes, we are able to the status of all our running applications. OpenShift has the same capability using the `oc` command:

```console
$ oc get pod
NAME                READY     STATUS      RESTARTS   AGE
nodejs-ex-1-build   0/1       Completed   0          13m
nodejs-ex-1-hx6v9   1/1       Running     0          12m
```

Here we can see that a pod (`nodejs-ex-1-build`) was created with the sole purpose to run the build and then was cleaned up after completing successfully. We are then left with our running pod (`nodejs-ex-1-hx6v9`) containing our node application.

If we actually just wanted to see the _deployments_, the actual application and not the assoicated pods, we can run:

```console
$ oc get dc
NAME        REVISION   DESIRED   CURRENT   TRIGGERED BY
nodejs-ex   1          1         1         config,image(nodejs-ex:latest)
```

Another useful command in Kubernetes is the ability to check the services associated with our applications if any. We do this in Kubernetes by running `kubectl get svc` and can do a similar thing in OpenShift:

```console
$ oc get svc
NAME        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
nodejs-ex   ClusterIP   172.30.6.48   <none>        8080/TCP   14m
```

As with Kubernetes, each application can be given an internal IP in which we can access our services but unless stated otherwise, this will only be available within the cluster.

Congratulations! You have learnt how to monitor your application builds and deployments within your cluster! To see how we can expose our applications outside of the OpenShift cluster, let's continue on to the [final Lab (Lab 4)](../Lab4/README.md)
