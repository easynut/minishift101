# Lab 1. Creating OpenShift projects

Learn how to login to an OpenShift cluster and create a new project in Minishift.

## 1. Login to the cluster

Login to the cluster with the output from the command after running `minishift start` as described in [setup overview](../README.md).

```
$ oc login -u system:admin
```

If you get an error about the `oc` command not being found, you can source it with the following command:

```
$ eval $(minishift oc-env)
```

As you will be able to see, there are several projects available to be able to switch between different workloads.

```
Logged into "https://192.168.99.100:8443" as "system:admin" using existing credentials.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-dns
    kube-proxy
    kube-public
    kube-system
  * myproject
    openshift
    openshift-apiserver
    openshift-controller-manager
    openshift-core-operators
    openshift-infra
    openshift-node
    openshift-service-cert-signer
    openshift-web-console
    test

Using project "myproject".
```

## 2. Create a project

You should have a default project setup already but we will create a new project for our new application. 

```
$ oc new-project nodejs-echo --display-name="nodejs" --description="Sample Node.js app"
```

Now you should have a new project with the label `nodejs` and your active project will now point to it. If you want to switch between projects, run:

```
$ oc project <display-name>
```

Congratulations, you have logged into your cluster and have created your first OpenShift project! To learn how to create your first application move on to the [next lab (Lab 2)](../Lab2/README.md).
