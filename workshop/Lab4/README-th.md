# 4. Exposing OpenShift applications

เมื่อเราได้ทำการตรวจสอบแล้วว่า application ของเรา build, deploy และ up running จาก [lab ที่แล้ว (Lab 3)](../Lab3/README-th.md), 
ขั้นตอนต่อไป เราจะทำการ configure access เปิดให้ application ของเรากับ users นอก cluster ซึ่งมันก็มีหลายวิธี

- Node-port services
- Port-forwarding
- Routes

## 4.1 Node port services

นี่คือวิธีที่เรียกได้ว่า clean ที่สุดทั้ง locally และ publicly ซึ่งจะเป็นการใช้ cluster node's IPs และ port ระหว่าง 30000-32767 โดยใช้ proxy ใน Openshift ควบคู่ด้วย ข้อได้เปรียบสำหรับวิธีนี้ ถ้าเทียบกับอีก 2 วิธีหลัง คือ เราไม่ต้องกังวลเรื่อง port clashes, มันสามารถทำงานได้ดีกับ http และ ไมต้องการ public hostname

สำหรับการ expose deployments ของเราผ่าน NodePort นั้นง่ายมาก เราเพียงแค่ expose ด้วย _load balancer_ และ label มันด้วย _nodejs-ex-ingress_:

```console
$ oc expose dc nodejs-ex --type=LoadBalancer --name=nodejs-ex-ingress
service/nodejs-ex-ingress exposed
```

To see the NodePort created, we can run:
```console
$ oc get --export svc nodejs-ex-ingress
NAME                TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
nodejs-ex-ingress   LoadBalancer   <none>       172.29.51.89   8080:31692/TCP   <unknown>
```

We can use the NodePort in conjuction with the cluster's internal or external IP which we can find in the following command:
```console
$ oc get node -o wide
NAME        STATUS    ROLES     AGE       VERSION           INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION              CONTAINER-RUNTIME
localhost   Ready     <none>    13h       v1.11.0+d4cacc0   192.168.64.11   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://1.13.1
```

We should then be able to access the application in the browser. In this example, we can access the Node application at `192.168.64.11:31692`:

![OpenShift node app](../images/openshift_node_app.png)

## 4.2 Port-forwarding

Alternatively, if you want to quickly access a port of a specific pod of your cluster, you can also use the oc `port-forward` command:

```
$ oc port-forward POD [LOCAL_PORT:]REMOTE_PORT
```

## 4.3 Routes

For web applications, the most common way to expose it is by a route. A route exposes the service as a host name. You can do this by running the command providing you have a host name available:

```
$ oc expose svc/nodejs-ex --hostname=www.example.com
```

Congratulations! You have completed all labs in this workshop! You have learnt how to:
- Create an OpenShift project
- Create an OpenShift application in various ways
- How to monitor the status of an application
- How to access and expose your application

For more information on how to navigate Minishift, check the [Minishift docs](https://docs.okd.io/latest/minishift/index.html)
