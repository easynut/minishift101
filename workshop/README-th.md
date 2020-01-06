[See this lab in English](./README.md)

# Setup overview

ขั้นตอนด้านล่างจะอธิบาย การติดตั้ง minishift และ depenencies ต่างๆ ซึ่ง Basic stps สำหรับการติดตั้งบน personal system ประกอบไปด้วย

1. [Configure the virtualization environment](./#configure-the-virtualization-environment)
2. [Download and install Minishift](./#download-and-install-minishift)
3. [Start the OpenShift Server](./#start-the-openshift-server)

สำคัญ เราควรจะต้องมีสิทธิ๋ในการติดตั้ง software บน laptop/pc ที่เราจะใช้งานด้วยนะครับ

# Configure the virtualization environment

Minishift สามารถถูกนำไปติดตั้งบน Windows, Linux และ macOS ได้ แต่เพราะว่าแต่ละ OS ถูกออกแบบมาแตกต่างกัน ดังนั้น Hypbervisor ที่เหมาะสมจะต้องถูกหยิบมาใช้ ซึ่ง Minishift จะทำงานควบคู่กับ Hypervisor นั่นเอง

ขั้นตอนการติดตั้ง สามารถดูได้ตามหมวดหมู่ OS ด้านล่างนี้

- For Linux, [set up the KVM driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-kvm-driver)
- For macOS, ~~[set up the xhyve driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-xhyve-driver)or~~ [set up the hyperkit driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-hyperkit-driver)
- For Windows, [set up the Hyper-V driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-hyperkit-driver)
- For VirtualBox (all platforms), [set up Minishift to use VirtualBox](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-virtualbox-driver)

*NOTE: If you have installed docker-ce for Mac on your machine, the hyperkit driver is already installed*

[![การติดตั้ง minishift บน Windows](https://img.youtube.com/vi/mDAEGGaRYqw/hqdefault.jpg)](https://www.youtube.com/watch?v=mDAEGGaRYqw)

[![การติดตั้ง minishift บน Windows](https://img.youtube.com/vi/fS-xhhxz8dY/hqdefault.jpg)](https://www.youtube.com/watch?v=fS-xhhxz8dY)

# Download and install Minishift

[download Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html) สำหรับผู้ใช้งาน macOS แนะนำให้ติดตั้งผ่าน Homebrew นะครับ

# Start the OpenShift Server

เมื่อทำการติดตั้งเรียบร้อยแล้ว อับดับต่อไปคือการสั่งเปิดใช้งาน Minishift

```
$ minishift start --vm-driver <driver>
```

Make sure to replace `<driver>` with the driver that you have used e.g. 'hyperkit' or 'virtualbox'. Once successfully started, you should be given the credentials to login to the cluster and the UI address as shown below:

```
OpenShift server started.

The server is accessible via web console at:
    https://192.168.64.11:8443/console

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


-- Exporting of OpenShift images is occuring in background process with pid 5703.
```

*NOTE: Both your console address and your pid will be different.*

# Further cluster configurations

After setting up your cluster, you may have specific requirements that you want to enforce in your cluster. The minishift tool allows you to manage the lifecycle of the single-node OpenShift cluster as well as set environment variables, persistent storage and proxy options if your machine is behind a proxy. For more information on these configurations, see the following [link](https://docs.okd.io/latest/minishift/using/basic-usage.html#runtime-options).

Minishift tool มีไว้ให้เราจัดการ single-node Openshift cluster รวมไปถึง environment variables, persistent storage และ proxy สามารถอ่านเพิ่มเติมการปรับแต่ง configurations ได้จาก ที่นี่ [link](https://docs.okd.io/latest/minishift/using/basic-usage.html#runtime-options).

เมื่อ Minishift สามารถ run ได้ปกติแล้ว สามารถไปที่ [Lab 1](./Lab1/README-th.md)
