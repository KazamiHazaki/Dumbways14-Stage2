<h2> Install Jenkins </h2> 

untuk menginstall jenkins kita tambahkan terlebih dahulu  repository jenkins ke dalam sistem kita

```shell
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key |sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
```

setelah download repository kemudian tambahkan kedalam repository

```shell
sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins.gpg] http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```
![image](https://user-images.githubusercontent.com/56806850/206358514-b1a9503d-8ed1-42d4-9ffb-768acfe509b8.png)

lalu update,  setelah di update maka kita bisa install java terlebih dahulu lalu install jenkins.

```shell
sudo apt install default-jre
```
![image](https://user-images.githubusercontent.com/56806850/206361118-36af2cf2-f2b7-43a7-a887-5238a464b485.png)


lalu install jenkins 


```shell
sudo apt install jenkins
```

setelah selesai check service jenkins 

![image](https://user-images.githubusercontent.com/56806850/206361349-1e395e22-febb-4542-9418-efed979293a0.png)

setelah proses installasi selesai dan jenkins sudah berjalan. buka port `8080` terlebih dahulu agar bisa mengakses jenkins.

```shell
sudo ufw allow 8080
```

lalu setelah itu kita bisa mengakses melalui port 8080

![image](https://user-images.githubusercontent.com/56806850/206361771-f0334ca5-b220-4c45-a10e-3ea1f83fb2e7.png)


<h2> Install jenkins on docker </h2>

pertama buat docker-compose terlebih dahulu 

```shell
version: '3.8'
services:
  jenkins:
   image: jenkins/jenkins:lts
   privileged: true
   ports:
   - "8080:8080"
   - "50000:50000"

   volumes:
    - "/home/kel2/jenkins:/var/jenkins_home"


```

![image](https://user-images.githubusercontent.com/56806850/206369300-983ac7b5-891d-449b-af26-997e479b4dd2.png)

jalankan dengan docker compose 

![image](https://user-images.githubusercontent.com/56806850/206369240-5fb19ce9-4d45-42db-8b90-9a85471851cd.png)









pada saat pertamakali akan diminta untuk memasukan password yang sudah di generate secara random oleh jennkins yg letaknya ada di 

```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
masukkan password tersebut untuk bisa mengakses jenkins.


pada installasi plugin pilih reccomennded plungin.setelah installasi plugin selesai maka akan diarahkan untuk membuat akun admin

![image](https://user-images.githubusercontent.com/56806850/206362042-ffaba03d-8531-4cd4-b004-7ba94d4690ab.png)

instance config biarkan seperti ini 

![image](https://user-images.githubusercontent.com/56806850/206362098-ab8d705d-1d9d-44af-818e-16cd40103ff6.png)

dan jenkins sudah siap untuk di  gunakan 

![image](https://user-images.githubusercontent.com/56806850/206362135-8eb27679-ab41-43d7-965f-4c80f0a75765.png)

setelah installasi jenkins selesai hubungkan akun github dengan jenkins, pertama kita buat API token terlebih dahulu dari github. kemudian sambungkan ke jenkins
buka github.com kemudian ke settings > Developer Settings > Personal Access Tokens > tokens (classic) > generate new token

![image](https://user-images.githubusercontent.com/56806850/206362651-c00f1d5a-2063-4471-9846-96e702f2c0a3.png)


kemudian pilih scopes repo:status,public_repo,admin:org_hook.dan generate token

![image](https://user-images.githubusercontent.com/56806850/206362962-9cb79c7a-1334-49f8-a1a5-cc88662c036d.png)


copy token tersebut dan pasang pada jenkins

![image](https://user-images.githubusercontent.com/56806850/206363038-7f12d2a1-0085-428b-b276-a966b1f0e57b.png)

`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`

kembali ke jenkins, klik profile dan tambahkan credentials

![image](https://user-images.githubusercontent.com/56806850/206363233-28ca930a-2dde-4abd-8653-51d99426081f.png)

tambahkan credentials pada global

![image](https://user-images.githubusercontent.com/56806850/206363277-41607cd0-6e0a-4829-86db-5dca1720d677.png)

kembali ke halaman dashboard jenkins lalu `manage jenkins`> `Configure System` kemudian scroll kebawah cari bagian github.

![image](https://user-images.githubusercontent.com/56806850/206363864-77aa0d90-fc56-40cf-82a4-58c5bd2c3b8d.png)
![image](https://user-images.githubusercontent.com/56806850/206364090-2eb8fd8e-9a6b-444a-98b4-4003ac9ede7d.png)

lalu tambahkan github server

![image](https://user-images.githubusercontent.com/56806850/206364132-d7d6172f-7a24-4387-80f7-a42071ac3baf.png)

kemudian pilih credentials 

![image](https://user-images.githubusercontent.com/56806850/206364174-ea845269-0a28-4903-8fb1-88097cd14108.png)

pilih kunci yg sudah di buat sebelumnya,kemudian test connection, jika berhasil akan muncul username github. kemudian di save


![image](https://user-images.githubusercontent.com/56806850/206364267-cb8e0444-f5a8-4678-a0be-2a57c11f571b.png)

kembali ke dashboard dan new item, pilih pipeline

![image](https://user-images.githubusercontent.com/56806850/206364458-39a0df14-d5f3-4635-8e58-896aef72c5c1.png)

pilih github project dan masukan link github

![image](https://user-images.githubusercontent.com/56806850/206364761-2867f78a-7e13-495d-ac2c-531c4a2414b8.png)

kemudian centang `Github hook trigger for GITScm polling`

![image](https://user-images.githubusercontent.com/56806850/206364822-0c4d059e-a4de-4484-8240-faf191b4faf8.png)

kemudian pada bagian pipeline pilih `pipeline script from SCM` dan SCM menggunakan `git`

![image](https://user-images.githubusercontent.com/56806850/206365011-37769bce-ada4-4650-b618-21f0ea1820db.png)










