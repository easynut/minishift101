# Lab 1. Creating OpenShift projects

มาดูกันว่า login เข้า Openshift cluster และ สร้าง project ใหม่ ใน minishift ทำอย่างไร

## 1. Login to the cluster

Login เข้า cluster ผ่าน cmd/terminal หลังจาก คำสั่ง `minishift start` เสร็จสิ้น ตามที่อธิบายไว้ใน [การติดตั้ง](../README-th.md).

```
$ oc login -u system:admin
```

ถ้าพบปัญหา `oc` command not being found ให้ทำการ set path ให้กับ laptop ให้ถูกต้องนะครับ ส่วนถ้าเป็น macOS ให้ใช้คำสั่งลัดด้านล่าง

```
$ eval $(minishift oc-env)
```

เมื่อทำการ login แล้ว เราจะเห็นว่า projects ก็ถูกแสดงออกมาทั้งหมด ซึ่งก็ขึ้นว่าเราจะใช้ project ไหน

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

ณ ตอนนี้ default project ก็จะถูกสร้างขึ้นมาให้เราเรียบร้อยแล้ว แต่ว่าเราจะทำการสร้าง project ใหม่สำหรับ application ของเราเอง

```
$ oc new-project nodejs-echo --display-name="nodejs" --description="Sample Node.js app"
```

และเราก็จะเห็นว่า project ใหม่ ที่ชื่อว่า nodejs-echo ถูกสร้างขึ้นมา โดยมี label เพิ่มเติมก็คือ nodejs (ตามที่เรา set display name เอาไว้) โดยที่เราจะสามารถเปลี่ยน project ได้ โดยใช้ command ด้านล่างนี้

```
$ oc project <display-name>
```

เสร็จสิ้นขึ้นตอนแรก และต่อไป เราจะทำการสร้าง application ใน [ lab ถัดไป (Lab 2)](../Lab2/README-th.md).
