# Lab 2: Creating OpenShift applications

หลังจากที่เราได้ทำการสร้าง project จาก [lab ที่แล้ว (Lab 1)](../Lab1/README-th.md) ขั้นต่อไป เรามาทำการสร้าง Openshift application บน cluster.

## 1. Creating an app 

การสร้าง App ใน Openshift cluster นั้นมีหลายวิธี 
- จาก souce code
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

As this template lives in a repo, we could have also run this from source as described in [section 1.1](./#11-creating-an-app-from-source)

ถ้า template นี้ได้ถูกเก็บไว้ที่ repo อยู่แล้ว, เราสามารถสร้าง app จาก source code ตาม [section 1.1](./#11-creating-an-app-from-source)

### 1.4 Creating an app from the OpenShift UI

If you're not a fan of the cli and wanted a more visual way of deploying applications in your cluster, you also have the option of using the OpenShift console. This is available locally at the address given after running `minishift start` as we did in the [setup](https://github.com/mofesal/minishift-101/blob/master/workshop/README.md#start-the-openshift-server):

```console
$ minishift start
...

The server is accessible via web console at:
    https://192.168.64.11:8443/console

You are logged in as:
    User:     developer
    Password: <any value>
```

Login to the UI:

![OpenShift login](../images/openshift_login.png)

As mentioned in the `minishift start` output, you can use the user _developer_ and password as any string of characters (at least one) and you will be able to access the UI.

Access the catalog:

![OpenShift catalog](../images/openshift_console.png)

Once you login, you will be redirected to the browser catalog where there will be a number of sample applications available for you to chose to deploy. This mirrors the OpenShift template steps we saw in [section 1.2](./#12-creating-an-app-from-a-dockerhub-image). We can also create and switch between projects but note, you are limited to the provided sample applications available in the console catalog.

See this [reference](https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html) for a more comprehensive overview of how to use the `new-app` command to create OpenShift applications.

Congratulations! You have learnt several ways to create applications in OpenShift! To see how we can manage our applications in OpenShift, let's continue on to the [next Lab (Lab 3)](../Lab3/README.md)




