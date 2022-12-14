<h1> Monitoring Server </h1>

Untuk memonitoring server kita akan menggunakan grafana, prometheus, node exporter.

apa itu grafana ? merupakan aplikasi open-source untuk memvisualisasikan metrics yg di ambil dari prometheus.
lalu apa itu prometheus? merupakan aplikasi untuk mengambil data metrics dari node exporter. 
sedangakan node exporter, merupakan aplikasi untuk mengexpose data metrics ke luar. 

install terlebih dahulu grafana,prometheus, dan node expoterter menggunakan docker compose. 


```shell
version: '3.8'
   
services:
  node-exporter:
    image: bitnami/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    stdin_open: true
    ports:
      - 9100:9100

  prometheus:
    image: bitnami/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    stdin_open: true
    ports:
      - 9090:9090
    volumes:
      - /home/kel2/grafana/prometheus.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana
    container_name: grafana
    stdin_open: true
    ports:
      - 3000:3000

```

pada docker compose di atas, volumes prometheus langsung di ambil dari yml prometheus yg sudah di buat dengan isi berikut. 



```shell
global:
  scrape_interval: 15s #mengambil data tiap 15s

scrape_configs:
  - job_name: "prometheus" #nama job yg akan muncul pada grafana
    scrape_interval: 15s #mengambil data tiap 15s
    static_configs:
    - targets: ["185.135.137.176:9090"] # target yg akan di ambil datanya

  - job_name: "node"
    scrape_interval: 15s #mengambil data tiap 15s
    static_configs:
    - targets: ["185.135.137.176:9100"] # target yg akan di ambil datanya

```

jadi ketika docker compose di install, maka prometheus akan secara otomatis menggunakan prometheus yg berada di folder grafana


![image](https://user-images.githubusercontent.com/56806850/207483361-4466d6f2-3af0-4d79-a2da-e56b01acfaf9.png)

lalu setelah membuat docker-compose.yml dan prometheus.yml jalankan docker compose. 


![image](https://user-images.githubusercontent.com/56806850/207483484-90d43a06-77f1-41a3-963b-93635b3560f6.png)

container sudah berjalan dan bisa langsung di buka grafana menggunakan port 3000.

![image](https://user-images.githubusercontent.com/56806850/207483592-cdff3bfc-6dfd-4daf-8184-7ebdc31f11cf.png)


secara default username dan password grafana adalah `admin/admin` 

![image](https://user-images.githubusercontent.com/56806850/207483852-dcbbe606-f51c-4a1c-b994-9a48f62c0c1d.png)

lalu ke configurasi dan tambahkan data source terlebih dahulu

![image](https://user-images.githubusercontent.com/56806850/207484068-1c48ded2-ce45-4f30-8bb2-6da399e86c20.png)

kemudian pilih prometheus 

![image](https://user-images.githubusercontent.com/56806850/207484198-7c4d1b29-8692-4910-962b-e16556717929.png)

setelah menambahkan data source kemudian kita buat dashboard baru 

![image](https://user-images.githubusercontent.com/56806850/207484468-bad521bc-4092-4380-813b-73f0c2715ea5.png)

dan tambahkan panel baru 

![image](https://user-images.githubusercontent.com/56806850/207484678-e72a3913-e728-471a-bd7d-300b2b266349.png)

lalu kita masuk ke edit panel tampilannya akan seperti ini 

![image](https://user-images.githubusercontent.com/56806850/207484727-3179b5c5-faac-4888-bf55-570d19056b81.png)


setelah itu kita buat metrics berdasarkan data dari prometheus.
![image](https://user-images.githubusercontent.com/56806850/207484787-ed5b29c8-c71c-4c4b-bf8b-a2aaf7e7f448.png)

kemudian pilih metrics yg akan di gunakan.


<h2> Membuat CPU Usage </h2>

gunakan metrics `node_cpu_seconds_total`, disini kita akan memonitoring

![image](https://user-images.githubusercontent.com/56806850/207484907-c88d1885-22cc-4749-87be-7eb574daef2e.png)


kemudian gunakan `mode!=idle`  tidak sama dengan idle, idlenya tidak di ambil datanya, lalu menggunakan kalkulasi irate dengan interval 1m, dan avg cpu


![image](https://user-images.githubusercontent.com/56806850/207486469-c2b2f990-3e3f-441b-826a-754df61cc910.png)

maka tampilannya akan seperti ini. 

![image](https://user-images.githubusercontent.com/56806850/207486553-2d81e6b2-b665-4980-be73-40eebea0f047.png)

dan untuk legend kita rubah menjadi mode, karena pada legend sekarang masih menggunakan automatis dan legend masih sulit untuk di baca.

![image](https://user-images.githubusercontent.com/56806850/207486657-67c3ea28-f858-42f9-9598-2113f6300339.png)

setelah dirubah maka legend juga akan berubah menjadi seperti berikut. 


![image](https://user-images.githubusercontent.com/56806850/207486690-0b89d392-6bc8-4792-8685-f2620219cb46.png)

rubah tooltip menjadi all

![image](https://user-images.githubusercontent.com/56806850/207486741-499ad2a0-fd4f-4cc8-b46b-db2ca0ccba8e.png)

dan unit menjadi percent 

![image](https://user-images.githubusercontent.com/56806850/207486805-b1418d63-d28e-4899-8860-27e7218cc2ed.png)

maka hasilnya akan seperti berikut. 

![image](https://user-images.githubusercontent.com/56806850/207486856-06aebb07-7657-4664-926d-0e24ca63c4d6.png)


setelah selesai mengatur kita save panel.

<h2> Panel RAM Usage </h2> 




