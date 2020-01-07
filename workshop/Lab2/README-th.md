# Lab 2: Creating OpenShift applications

หลังจากที่เราได้ทำการสร้าง project จาก [lab ที่แล้ว (Lab 1)](../Lab1/README-th.md) ขั้นต่อไป เรามาทำการสร้าง Openshift application บน cluster.

## 1. Creating an app 

การสร้าง App ใน Openshift cluster นั้นมีหลายวิธี 
- จาก Souce code
- จาก DockerHub images
- จาก Openshift templates
- จาก Openshift UI

### 1.1 สร้าง app จาก souce code

เราใช้ command `oc new-app` ใช้สำหรับสร้าง app ใน Openshift โดยใช้ source code ที่มีอยู่แล้ว ไม่ว่าจาก local หรือ จาก repository ไม่ว่า github/gitlab. ถ้ามีการระบุ repository, `new-app` จะทำการตรวจสอบว่าควรใช้ build straetegy แบบใด (**Docker** หรือ **Source**) และ `new-app` จะทำการตรวจสอบว่า ภาษาอะไร ที่ใช้ในการสร้าง app และใช้ builder ที่เหมาะสมกับ app ของเรา

การ build จาก local DockerFile:
```
$ oc new-app /path/to/local/ or /remote/Dockerfile
```

การ build จาก source code:
```
$ oc new-app path/to/local/ or /remote/repository.git
```

### 1.2 การสร้าง app ด้วย images จาก DockerHub

Openshift มี feature ที่รองรับ DockerHub โดยเราสามารถระบุ image ใน DockerHub ที่เราต้องการใช้ deploy app บน cluster, command `new-app` จะทำการสร้าง runnable image จาก image ที่เราระบุ

ยกตัวอย่าง, การสร้าง app จาก nginx image เราใช้ command ตามด้านล่าง:

```
$ oc new-app nginx
```

และ Openshift ไม่ได้จำกัดให้เราใช้งาน DockerHub registry อย่างเดียว, OpenShift สามารถรองรับการเข้าถึง Private registries ได้อีกด้วย:

```
$ oc new-app myregistry:8000/example/image
```

### 1.3 สร้าง app จาก Openshift template

Openshift templates คือ starter kit ที่ทาง Openshift ได้ปูทางเอาไว้ให้ developer สร้างหยิบไปใช้งานได้ทันที โดย templates มักจะครอบคลุม applications ที่เป็นที่นิยม และ ถูกนำไปใช้งานบ่อย เช่น Ruby, Node และ MongoDB

ตัวอย่าง [nodejs template](https://github.com/sclorg/nodejs-ex#openshift-origin-v3-setup) จะมีโครงสร้างตามด้านล่าง:

```
nodejs-ex
├── openshift
│   └── templates
│       ├── nodejs.json
│       ├── nodejs-mongodb.json
│       └── nodejs-mongodb-persistent.json
├── package.json
├── README.md
├── server.js
├── tests
│   └── app_test.js
└── views
    └── index.html
```

สำหรับการ Deploy เราเพียงแค่ run command ตามด้านล่าง:

```
$ oc new-app -f /path/to/nodejs.json
```

ถ้า template นี้ได้ถูกเก็บไว้ที่ repo อยู่แล้ว, เราสามารถสร้าง app จาก source code ตาม [section 1.1](./##11-%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-app-%E0%B8%88%E0%B8%B2%E0%B8%81-souce-code)


### "ตัวอย่าง deploy ด้วย source code จาก github"

หลังจากที่เรา `oc login` ผ่าน cmd/terminal มาแล้ว เราสามารถ deploy sample application จาก github ได้

```
$ oc login
Authentication required for https://192.168.99.100:8443 (openshift)
Username: developer
Password:
Login successful.

You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>
```

ต้องอย่าลืมว่า RBAC (Role Base Access Control) จะมากับ Openshift เป็น default นะครับ ฉะนั้น User ที่เราใช้ผ่าน cli oc ต้องมี permission ทำการสร้าง project, application และอื่นๆ ด้วยนะครับ

```
$ oc create clusterrolebinding <any_valid_name> --clusterrole=sudoer --user=<username>
```

และทำการสร้าง project ก่อนที่จะสร้าง app นะครับ

```
oc new-project nodejs-ex --display-name="nodejs" --description="Sample Node.js ex app"
```

ต่อไป สร้าง application ด้วย source code จาก github

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

สามารถตรวจสอบสถานะ, deployment, services, การ expose และ route. ทดลองย้อนกลับไปที่ UI console ดูนะครับ ว่า pod, build, deployment และ service รวมไปถึง route มีสถานะอย่างไร

```
# get deployments, this command similar to kubectl get deployments
$ oc get dc

# get services
$ oc get svc

# expose the service and create the route
$ oc expose svc nodejs-ex

# get route for the service
$ oc get route
```

### 1.4 Creating an app from the OpenShift UI สร้าง app จาก Openshift UI

ถ้าเราไม่ต้องการสร้าง app จาก cli ผ่าน cmd/terminal, อีกทางเลือกนึงที่เรียกได้ว่า user-friendly กว่าคือการใช้ Openshift UI ซึ่งได้ถูกออกมาแบบมาให้ใช้งานง่าย โดยไม่ขึ้นกับ cli tool เรียกได้ว่าค่อนข้างสะดวก และย่นระยะเวลาในการเรียนรู้เพิ่มเติมเกี่ยวกับ cli tool (ยกตัวอย่างการใช้ kubernetes ซึ่งจะไม่ได้มี UI ให้เราใช้สร้าง projects, applications). OpenShift console จะมากับทุกๆ cluster ซึ่งสำหรับ local cluster ที่เรารันขึ้นมา URL/address จะแสดงไว้หลังจากที่เราสั่ง `minishift start` ตามที่เราได้ปฏิบัติไปแล้วใน [setup](https://github.com/easynut/minishift101/blob/master/workshop/README-th.md#start-the-openshift-server):

```console
$ minishift start
...

The server is accessible via web console at:
    https://192.168.64.11:8443/console

You are logged in as:
    User:     developer
    Password: <any value>
```

Login ผ่าน UI:

![OpenShift login](../images/openshift_login.png)

As mentioned in the `minishift start` output, you can use the user _developer_ and password as any string of characters (at least one) and you will be able to access the UI.

ตามที่เราได้เห็นผ่านทาง cmd/terminal หลังจาก `minishift start` เราสามารถใช้ user _developer_ ส่วน password จะเป็นอะไรก็ได้

หน้า Catalog:

![OpenShift catalog](../images/openshift_console.png)

หลังจากที่ logic เข้ามาแล้ว เราจะเห็นหน้า Catalog ซึ่งก็มีตัวอย่าง applications ให้เราลองหยิบไป deploy บน local cluster ของเราได้ สามารถย้อนหลับไปดู command  ที่ [section 1.2](./#12-%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-app-%E0%B8%94%E0%B9%89%E0%B8%A7%E0%B8%A2-images-%E0%B8%88%E0%B8%B2%E0%B8%81-dockerhub)

สามารถอ่านเพิ่มเติมได้ที่ [reference](https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html) เกี่ยวกับการใช้ `new-app` command เพื่อสร้าง OpenShift applications.

Good job, Keep it up !! ณ ตอนนี้ ทุกท่านได้ทราบวิธีการสร้าง applications ใน Openshift. ขั้นตอนต่อไป เรามาดูกันว่า เราะจัดการ applications ใน Openshift ย่างไร ที่ [Lab ถัดไป (Lab 3)](../Lab3/README-th.md)
