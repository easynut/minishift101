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

ทำการดูค่า NodePort:

```console
$ oc get --export svc nodejs-ex-ingress
NAME                TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
nodejs-ex-ingress   LoadBalancer   <none>       172.29.51.89   8080:31692/TCP   <unknown>
```

เราสามารถใช้ command ด้านล่างนี้ เพื่อดู Internal-IP และ External-IP ของ node

```console
$ oc get node -o wide
NAME        STATUS    ROLES     AGE       VERSION           INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION              CONTAINER-RUNTIME
localhost   Ready     <none>    13h       v1.11.0+d4cacc0   192.168.64.11   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://1.13.1
```

จาก Internal-IP จาก node และ NodePort ที่เราได้ค่ามาก่อนหน้านี้ ดังนั้น URL สำหรับเข้าถึง application ของเราก็คือ `192.168.64.11:31692`:

![OpenShift node app](../images/openshift_node_app.png)

ในบางกรณี เราจะเห็นว่า Internal-IP แสดงค่า ที่เราไม่สามารถเข้าถึงได้ ค่า External-IP ก็เป็น none

```console
oc get node -o wide
NAME        STATUS    ROLES     AGE       VERSION           INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION               CONTAINER-RUNTIME
localhost   Ready     <none>    5h        v1.11.0+d4cacc0   10.0.2.15     <none>        CentOS Linux 7 (Core)   3.10.0-1062.9.1.el7.x86_64   docker://1.13.1
```

ดังนั้นเราต้องใช้ `minishift ip` command เพื่อรับค่า IP address ของ cluster's node ของเรา 

```console
$ minishift ip
192.168.99.101
```

ซึ่ง URL สำหรับการเข้าถึง app ของเราก็คือ `http://192.168.99.101:31692`

## 4.2 Port-forwarding

ตามหัวข้อนะครับ port forwarding จาก local port ไป remote port เราใช้ command `oc port-forward`:

```
$ oc port-forward POD [LOCAL_PORT:]REMOTE_PORT
```

## 4.3 Routes

วิธีที่นิยมใช้ expose web application คือการ expose ด้วย route ซึ่งจะเป็น service ด้วย hostname โดยให้ทาง Openshift เป็นผู้จัดการ โดยที่เราสามารถระบุ hostname ได้ด้วย

```
$ oc expose svc/nodejs-ex --hostname=www.example.com
```

ในที่สุดเราก็ได้ทำ Lab เสร็จสิ้นแล้ว ทุกท่านได้เรียนรู้การใช้ Openshift ทั้งหมดตามที่ list มาด้านล่างนี้
- สร้าง Openshift project
- สร้าง Openshift application ด้วยวิธีหลายๆแบบ
- Monitor application อย่างไร
- เข้าถึง และ expose application อย่างไร

เรียนรู้เพิ่มเติมเกี่ยวกับ Minishift ได้ที่นี่ [Minishift docs](https://docs.okd.io/latest/minishift/index.html)
