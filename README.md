Base Image with ksh and iconv (with TIS-620 support)

# Prepare gconv-modules.cache

เตรียม gconv-modules.cache
NOTE: อันนีต้องทำในเครื่อง Linux หรือ Docker ของตระกูล CentOS มารัน เพื่อ Build

1. เอา TIS-620.so โยนไปใน /usr/lib64/gconv
2. vi แก้ไฟล์ /usr/lib64/gconv/gconv-modules
3. เติมตาราง Endcoding

```
#       from                    to                      module          cost
alias   TIS620//                TIS-620//
alias   TIS620-0//              TIS-620//
alias   TIS620.2529-1//         TIS-620//
alias   TIS620.2533-0//         TIS-620//
alias   ISO-IR-166//            TIS-620//
module  TIS-620//               INTERNAL                TIS-620         1
module  INTERNAL                TIS-620//               TIS-620         1
```

4. save และใช้ iconvconfig build cache ขึ้นมา gconv-modules.cache ที่ path /usr/lib64/gconv/gconv-modules
5. iconv -l | grep TIS

# Build Image By Jenkins

1. create Jenkins Job from Jenkinsfile

# Build Image By Command

1. Build 1.0.0 คือ Tag ถ้ามีแก้ Base Image ค้องขยับ Tag ด้วย

```
docker build -t dev.ds.local:5000/alma8ksh:1.0.0 -f Dockerfile.alma8ksh .
```

2. ตรวจสอบ Size ของ Image ด้วยคำสั่ง

```
docker images
```

ขนาดที่ได้ควรจะประมาณ 600MB++

```
REPOSITORY                      TAG       IMAGE ID       CREATED          SIZE
dev.ds.local:5000/alma8ksh    1.0.0     db49ae4935d7   10 seconds ago   662MB
```

3. เอาขึ้น docker registry ของ ds-dc

```
docker push dev.ds.local:5000/alma8ksh:1.0.0

docker push dev-dr.ds.local:5000/alma8ksh:1.0.0
```

4. Tag เป็น dr

```
docker tag  dev.ds.local:5000/alma8ksh:1.0.0  dev-dr.ds.local:5000/alma8ksh:1.0.0
```

5. เอาขึ้น docker registry ของ ds-dr

```
docker push dev-dr.ds.local:5000/alma8ksh:1.0.0
```
