# Lab 3: Managing OpenShift applications

หลังจากที่เราได้สร้าง application บน Openshift ตาม [lab ที่แล้ว (Lab 2)](../Lab2/README-th.md), ขั้นตอนต่อไป คือ เรามั่นใจได้อย่างไรว่า applications ของเรา มันทำงานได้ปกติ และ เราจะ access เข้าไปที่ applications ที่เรา deploy ขึ้นไป Openshift ได้อย่างไร

## 0. Preparation

ต้องอย่าลืมว่า RBAC (Role Base Access Control) จะมากับ Openshift เป็น default นะครับ ฉะนั้น User ที่เราใช้ผ่าน cli oc ต้องมี permission ทำการสร้าง project, application และอื่นๆ ด้วยนะครับ จาก lab นี้ เราจะใช้ user = developer เป็นหลักนะครับ ดังนั้น เพื่อให้เราสามารถเห็นทุก projects และมีสิทธิ๋ในการสั่ง command ผ่าน CLI took ได้เปรียบเสมือน system:admin ได้นั้น เราต้องมีการปรับค่าให้ developer account ตามด้านล่าง

```
$ minishift addons install --defaults
$ minishift addons enable cluster-admin
$ oc adm policy  --as system:admin add-cluster-role-to-user cluster-admin developer
```

oc login ผ่าน cmd/terminal มาแล้ว เราสามารถ deploy sample application จาก github ได้

```
$ oc login
Authentication required for https://192.168.99.100:8443 (openshift)
Username: developer
Password:
Login successful.

You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>
```

ทำการสร้าง project ก่อนที่จะสร้าง app นะครับ

```
oc new-project nodejs-ex --display-name="nodejs" --description="Sample Node.js ex app"
```

## 1. Monitoring builds

เมื่อเราสั่ง `oc new-app` build ก็ถูกทำงาน (คล้ายๆ กับ docker build), แต่แตกต่างกันตรงที่ build จะรัน background และเราก็จะค่อยเห็นว่า cmd/terminal ไมค่อยพิมพ์อะไรผ่าน console ออกมา

ดังนั้น เพื่อทำการ check output ให้ใช้คำสั่งด้านล่าง

```console
$ oc status
```

อันดับต่อไป เรามาสร้าง app โดยการดึง source code จาก github มา deploy บน Openshift:

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

และทำการตรวจสอบสถานะ:

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

จาก output เราจะเห็นว่า service endpoint ที่เราเอาไว้ใช้เข้าถึง app คือ `172.30.6.48:8080` และเรายังเห็น build type ที่ใช้สร้าง app อีกด้วย นั่นก็คือ  `openshift/nodejs:10` ท้ายที่สุด เราเห็นอีกว่า app ของเรามันยังไม่พร้อมใช้งาน ยังรอ image upload อยู่ ดังนั้น ถ้าเราต้องการดู build logs เพิ่มเติม เราสามารถใช้ command ด้านล่าง

```
# get all builds
$ oc get build
NAME          TYPE      FROM          STATUS     STARTED          DURATION
nodejs-ex-1   Source    Git@e59fe75   Complete   32 minutes ago   32s

# check logs from the specific build
$ oc logs build/<app-name>-<build-no>
```

จากตัวอย่าง เราจะเห็นว่าตอนนี้ เรามีเพียงแค่ 1 build เท่านั้น เราลองดู logs จาก build นี้โดย
```
$ oc logs build/nodejs-ex-1
```

## 2. Monitoring deployments

ใน Kubernetes, เราตรวจสอบ running applications ได้ และ Openshift ก็ได้รับคุณสมบัติเหล่านั้นมาด้วยเหมือนกัน แต่ใน Openshift เราจะใช้ `oc` command:

```console
$ oc get pod
NAME                READY     STATUS      RESTARTS   AGE
nodejs-ex-1-build   0/1       Completed   0          13m
nodejs-ex-1-hx6v9   1/1       Running     0          12m
```

เราจะเห็นว่า pod `nodejs-ex-1-build` ที่ถูกสร้างขึ้นมาเพื่อทำการ build โดยเฉพาะได้ถูกทำลาย หลังจากที่มันทำงานเสร็จสิ้นแล้ว. ส่วน pod `nodejs-ex-1-hx6v9` คือ pod ที่มี application ที่เรา deploy ขึ้นไปทำงานอยู่

ถ้าเราต้องการดู _deployments_ ทั้งหมด เราสามารถใช้คำสั่งด้านล่าง

```console
$ oc get dc
NAME        REVISION   DESIRED   CURRENT   TRIGGERED BY
nodejs-ex   1          1         1         config,image(nodejs-ex:latest)
```

สำหรับการตรวจสอบ services นั้น ถ้าเป้น Kubernetes เราก็จะใช้ `kubectl get svc` หรือ `kubectl get service` ส่วนสำหรับใน Openshift เราก็ใช้  `oc` 

```console
$ oc get svc
NAME        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
nodejs-ex   ClusterIP   172.30.6.48   <none>        8080/TCP   14m
```

เราจะเห็นว่า service ที่เรามีอยู ณ ตอนนี้คือ `nodejs-ex` มันได้ถูกมอบหมาย IP Address แต่ว่าเป็น Internal เท่านั้น

สำหรับ lab นี้ เราจะเห็นว่าการตรวจสอบสถานะของ applications, builds, pods, deployments และ services ผ่านทาง CLI tool (cmd/terminal) ก็ทำได้ง่าย ไม่ยาก ซึ่ง Openshift UI ก็มี features ทั้งหมดนี้มาให้อยู่แล้ว สำหรับใครที่มองว่าการใช้งาน CLI tool ค่อนข้างวุ่นวาย สามารถทดลองใช้ UI Console ได้นะครับ จะง่ายกว่ามาก 

ส่วนอันดับถัดไป เราจะเห็นว่า ณ ตอนนี้ Application ของเรามันทำงานแล้ว แล้วเราจะเข้าถึง application ของเราอย่างไน ในเมื่อ service มันมีมาแต่ internal IP address ดังนั้นเราเริ่ม Lab ต่อไปนะครับ สำหรับการ expose service ไปยังภายนอก Openshift cluster [final Lab (Lab 4)](../Lab4/README-th.md)
