[See this lab in English](./README.md)

<img src="https://avatars1.githubusercontent.com/u/38341030?s=150&v=4">

# OpenShift

Red Hat® OpenShift® รองรับ Kubernetes สำหรับ cloud-native Applications ที่มาพร้อมกับตวามปลอดภัยระดับ enterprise. ถึงแม้ว่า kubernetes ช่วยให้เราไม่ต้องกังวลเรื่อง scalability, การกระจาย workload ไปที่ต่างๆ แม้กระทั่งเรื่อง abstract ที่ทำให้เราแยกเรื่อง business และ development ได้อย่างชัดเจน ทั้งนี้ทั้งนั้น การใช้ k8s มากับหลายๆ เรื่องที่เพิ่มเติม เช่นกาติดตั้ง การ deploy และการจัดการ cluster. ซึ่ง Openshfit เป็น tool ที่ทำให้การใช้งาน containerization + orchestraction tool นี้ง่ายขึ้น

Repository นี้ เป็นที่รวม content สำหรับ minishift workshop เริ่มตันจากศูนย์ ไปจนกระทั้งการสร้าง local cluster รวมไปถึงการ deploy application บน local openshift cluster. 

[![OpenShift Overview](https://www.openshift.com/hubfs/video_Red-Hat-OpenShift-overview.jpg)](https://www.youtube.com/watch?v=5dwMrFxq8sU)

## Minishift

workshops จะประกอบไปด้วยหลายๆ labs/ขั้นตอน แม้กระทั้งการสร้าง การ deploy และการจัดการ microservices ใน ด้วย single-node openshift cluster โดยใช้ minishift. ดังนั้นก่อนเริ่มต้น lab, เราควรจะติดตั้ง minishift ให้ทำงานได้เรียบร้อยเสียก่อน รวมไปทั้งขึ้นตอนการเครียมความพร้อมอื่นๆ ด้วย.

- [ติดตั้ง](./workshop/README-th.md)
- [Lab 1 OpenShift สร้าง projects](./workshop/Lab1/README-th.md)
- [Lab 2 OpenShift สร้าง applications](./workshop/Lab2/README-th.md)
- [Lab 3 OpenShift จัดการ applications](./workshop/Lab3/README-th.md)
- [Lab 4 OpenShift เปิดเผย applications](./workshop/Lab4/README-th.md)
