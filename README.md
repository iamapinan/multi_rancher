# Multi Rancher Server
## Command for create rancher multiple master.
เหตุผลเพื่อต้องการให้มีการป้องกัน rancher server ล้ม ซึ่งหากมี node เดียวก็จะล้มได้ทั้งระบบหากไม่มีระบบสำรองทำงานแทน

## สร้างฐานข้อมูลให้กับ Rancher บน Node1 
Donwload rancher.sql ไปไว้ที่ node1 ใน /data/rancher/dump/

```Bash
sudo docker run --name mariadb 
-v /data/mysql_data:/var/lib/mysql 
-v /data/rancher/dump:/docker-entrypoint-initdb.d 
-e MYSQL_DATABASE=rancher_server 
-e MYSQL_ROOT_PASSWORD=<Password> -d mariadb
```

## สร้าง Rancher Server บน Node1
```Bash
sudo docker run -d --restart=always -p 8080:8080 --name rancher-server  --link mariadb \
-e CATTLE_API_ALLOW_CLIENT_OVERRIDE=true \
-e CATTLE_DB_CATTLE_MYSQL_HOST=mariadb \
-e CATTLE_DB_CATTLE_MYSQL_PORT=3306 \
-e CATTLE_DB_CATTLE_MYSQL_NAME=rancher_server \
-e CATTLE_DB_CATTLE_USERNAME=root \
-e CATTLE_DB_CATTLE_PASSWORD=<Password> \
rancher/server
```

## สร้าง Rancher Server ตัวที่ 2 บน Node2
```Bash
sudo docker run -d --restart=always -p 8080:8080 --name rancher-server  \
-e CATTLE_API_ALLOW_CLIENT_OVERRIDE=true \
-e CATTLE_DB_CATTLE_MYSQL_HOST=<IP or URL of mariadb> \
-e CATTLE_DB_CATTLE_MYSQL_PORT=3306 \
-e CATTLE_DB_CATTLE_MYSQL_NAME=rancher_server \
-e CATTLE_DB_CATTLE_USERNAME=root \
-e CATTLE_DB_CATTLE_PASSWORD=<Password> \
rancher/server
```

จากนั้นก็สามารถรันไปที่ port 8080 ของทั้งเครื่อง node1 และ node2 ได้แล้ว ซึ่งเมื่อตัว node1 มีปัญหา node2 ก็จะทำงานแทนทันที
