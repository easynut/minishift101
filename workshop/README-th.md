[See this lab in English](./README.md)

# Setup overview

ขั้นตอนด้านล่างจะอธิบาย การติดตั้ง minishift และ depenencies ต่างๆ ซึ่ง Basic stps สำหรับการติดตั้งบน personal system ประกอบไปด้วย

1. [Configure the virtualization environment](#configure-the-virtualization-environment)
2. [Download and install Minishift](#download-and-install-minishift)
3. [Start the OpenShift Server](#start-the-openshift-server)

สำคัญ เราควรจะต้องมีสิทธิ๋ในการติดตั้ง software บน laptop/pc ที่เราจะใช้งานด้วยนะครับ

# Configure the virtualization environment

Minishift สามารถถูกนำไปติดตั้งบน Windows, Linux และ macOS ได้ แต่เพราะว่าแต่ละ OS ถูกออกแบบมาแตกต่างกัน ดังนั้น Hypbervisor ที่เหมาะสมจะต้องถูกหยิบมาใช้ ซึ่ง Minishift จะทำงานควบคู่กับ Hypervisor นั่นเอง

ขั้นตอนการติดตั้ง สามารถดูได้ตามหมวดหมู่ OS ด้านล่างนี้

- For Linux, [set up the KVM driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-kvm-driver)
- For macOS, ~~[set up the xhyve driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-xhyve-driver)or~~ [set up the hyperkit driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-hyperkit-driver)
- For Windows, [set up the Hyper-V driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-hyperkit-driver)
- For VirtualBox (all platforms), [set up Minishift to use VirtualBox](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-virtualbox-driver)

*NOTE: สำหรับ macOS users, ถ้าได้ทำการติดตั้ง Docker Desktop แล้ว ในตัวของมันจะมี hyperkit มาด้วยอยู่แล้วนะครับ และสามารถดู video การติดตั้ง minishift บน Windows, macOS จาก link ด้านล่าง*

## วิธีการติดตั้ง Minishift บน Windows 10

[![การติดตั้ง minishift บน Windows](https://img.youtube.com/vi/mDAEGGaRYqw/hqdefault.jpg)](https://medium.com/@nutta/%E0%B8%A7%E0%B8%B4%E0%B8%98%E0%B8%B5%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-minishift-%E0%B8%9A%E0%B8%99-windows-10-d4bee756c49d)

## ขั้นตอนการติดตั้ง Minishift บน Macbook - macOS

 [![การติดตั้ง minishift บน Windows](https://img.youtube.com/vi/fS-xhhxz8dY/hqdefault.jpg)](https://medium.com/@nutta/%E0%B8%82%E0%B8%B1%E0%B9%89%E0%B8%99%E0%B8%95%E0%B8%AD%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-minishift-%E0%B8%9A%E0%B8%99-macbook-macos-f1e138a32cf4)

# Download and install Minishift

[download Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html) สำหรับผู้ใช้งาน macOS แนะนำให้ติดตั้งผ่าน Homebrew นะครับ

# Start the OpenShift Server

เมื่อทำการติดตั้งเรียบร้อยแล้ว อับดับต่อไปคือการสั่งเปิดใช้งาน Minishift โดยระบุุการใช้ vm driver เช่น hyperkit หรือ virtualbox การรันครั้งแรกอาจจะใช้เวลามากเพราะว่ารวมไปถึงการติดตั้ง สร้าง minishift cluster. 

```
$ minishift start --vm-driver <driver>
```

เมื่อ minishift cluster ได้ถูกติดตั้งเรียบร้อยแล้ว เราจะเห็นข้อมูลผ่าน cmd/terminal ตามด้านล่าง

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

*NOTE: URL สำหรับ Console และ PID จะไม่เหมือนกันกับที่แสดงข้างบนนะครับ*

# Further cluster configurations

Minishift tool มีไว้ให้เราจัดการ single-node Openshift cluster รวมไปถึง environment variables, persistent storage และ proxy สามารถอ่านเพิ่มเติมการปรับแต่ง configurations ได้จาก ที่นี่ [link](https://docs.okd.io/latest/minishift/using/basic-usage.html#runtime-options).

เมื่อ Minishift สามารถ run ได้ปกติแล้ว สามารถไปที่ [Lab 1](./Lab1/README-th.md)
