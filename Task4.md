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

<h3> Menghubungkan Jenkins dengan Server </h3>

sebelum bisa menggunakan jenkins dengan vps server kita hubungkan terlebih dahulu dengan menambahkan ssh-keygen. ssh yg dibutuhkan

- ssh-keygen public key > ditaruh di dalam authorized keys ssh
- ssh-kegen private key > di letakan pada system account jenkins

buat keygen dengan command

```ssh
ssh-keygen
```
kemudian di enter saja hingga selesai. setelah ssh di buat copy isi dari public key ke dalam file `authorized_keys`

![image](https://user-images.githubusercontent.com/56806850/206850589-e43b6b83-8dc8-466a-bea7-8ec73a02b004.png)

lalu kita tambahkan ssh key ke jenkinns

buka user>credentials >

![image](https://user-images.githubusercontent.com/56806850/206850635-1a50396e-4aa5-457f-8043-d8916c0eb45c.png)

scroll kebawah dan tambah credentials

![image](https://user-images.githubusercontent.com/56806850/206850718-d4e57d78-2c35-4763-8f5d-2c8f3f318670.png)

pilih credentials baru menggunakan `SSH username with private key`

![image](https://user-images.githubusercontent.com/56806850/206850734-56ad4e01-da0f-4bce-a80f-c0ccbcbeeeba.png)

isi ID sesuai dengan user login pada VPS yang akan di hubungkan 

![image](https://user-images.githubusercontent.com/56806850/206850848-7f18b37d-2781-4df0-a2d7-6cd4ed5bd533.png)

isi private key dengan ssh private key yang sudah dibuat pada vps

![image](https://user-images.githubusercontent.com/56806850/206850894-c618e363-7979-4a05-8f78-c855bc6d1606.png)

isi private key terdapat pada direktori ssh secara default dan tanpa file type `.pub`

![image](https://user-images.githubusercontent.com/56806850/206850912-44bbc212-0614-4706-97f6-2f74eee961d4.png)

copy isi private key dan masukkan kedalam jenkins. setelah ktia menambahkan user credentials, download pluginn ssh pada jenkins agar bisa menggunakan koneksi ssh.

kembali ke dashboard>manage jenkins > Plugin manager pilih bagian available plugins untuk mendonwload plugin  cari plugin bernama `ssh agent plugin`

![image](https://user-images.githubusercontent.com/56806850/206851212-dc70a768-a68a-499d-93d5-61af3bb85aa3.png)

![image](https://user-images.githubusercontent.com/56806850/206851176-b8f30e46-3027-4e18-bf9c-119f61d81b9a.png)





<h3> Membuat Jenkinsfile </h3>

untuk membuat pipeline di butuhkan file bernama `Jenkinsfile` gunakan nano untuk membuat  file

```shell
def secret = 'app'
def server = 'app@103.189.235.91'
def directory = 'literature-backend2'
def branch = 'main'



pipeline{
    agent any

    stages{
        stage ('delete docker & git pull'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker-compose down
                    docker system prune -f
                    git pull origin ${branch}
                    exit
                    EOF"""
                }
            }
        }
        stage ('docker build'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker-compose build
                    exit
                    EOF"""
                }
            }
        }
        stage ('docker up'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker-compose up -d
                    exit
                    EOF"""
                }
            }
        }
    }
}
```

setelah membuat jenkinsfile, file ini harus di upload ke dalam repository yg ingin di hubungkan ke jenkins.
<h3> Membuat Pipeline </h3>

untuk membuat pipelie kembali ke dashboard kemudian pilih new item dan pilih pipeline, berinama pipeline 

![image](https://user-images.githubusercontent.com/56806850/206851394-0a8adb5e-4d93-486c-8ddf-2bc6a484c4fc.png)

scroll kebagian bawah nyalakan GITScm

![image](https://user-images.githubusercontent.com/56806850/206851438-314e5557-6e15-44d5-9b73-085d68d5fa26.png)


rubah definition menjadi `pipeline script SCM` dan SCM menjadi  `git` dan tambahkan link repository yang akan di hubungkan dengan jenkins

![image](https://user-images.githubusercontent.com/56806850/206851525-e2f25392-6b98-4712-8f37-e018967bd9c6.png)


pilih branch yg akan digunakan, pada kali ini kita gunakan branch` main`

![image](https://user-images.githubusercontent.com/56806850/206851514-0c6fbd16-237a-4a98-ac29-f0959cb1f0a5.png)

dan kemudian save. Jika Jenkinsfile sudah di tambahkan ke dalam repository. bisa langsung menekan tombol build now. ketika di build kita bisa melihat error di bagian logs sesuai dengan stage yg di jalankan.


<h2> Automatic Trigger Build using webhook github </h2>

untuk membuat jenkins automatic build ketika kita push ke repository github, gunakan webhook pada repository dengan cara berikut.

masuk ke settings repository

![image](https://user-images.githubusercontent.com/56806850/206851700-602613bb-db33-4d72-b534-1508d0426f88.png)
 
 kemudian pilih webhook
 
 ![image](https://user-images.githubusercontent.com/56806850/206851714-dc02d92a-5b94-45f7-be9b-e73bb9890a70.png)

tambahkan webhook

![image](https://user-images.githubusercontent.com/56806850/206851727-2fbf16fc-c031-4ed9-b4ef-cd348e2296ca.png)

isi dengan url jenkinns dengan menambahkan `/github-webhook/` pada url jenkins

![image](https://user-images.githubusercontent.com/56806850/206851763-a0734837-5750-4f3a-a4ce-c2c26bd60c96.png)


kemudian save. Jenkins sudah automatis Build ketika push repository.


<h2> Membuat Notifikasi dengan discord </h2>


untuk membuat notifikasi jenkins ke discord, install terlebih dahulu plugin dari jenkins.

![image](https://user-images.githubusercontent.com/56806850/206851834-fe194499-f8ba-4859-9a85-262327762492.png)


setelah terintall hubungkan copy terlebih dahulu alamat webhook dari discord untuk mendapatkannya harus memiliki server discord kemudian masuk kedalam pengaturan server lalu ke integration dan tambahkan webhook

![image](https://user-images.githubusercontent.com/56806850/206851944-8d4e3a02-84e8-4cf0-ad96-54479609aaf3.png)


![image](https://user-images.githubusercontent.com/56806850/206851955-e5d9c73a-d35a-4a49-8910-e3de713a724b.png)


Beri nama bot webhook dan pilih channel yg ingin digunakan unntuk memberi notifikasi,dan copt alamat webhooknya.

![image](https://user-images.githubusercontent.com/56806850/206851991-5d88c85c-3051-4360-a89a-561a7c36e71f.png)

alamat webhook tersebut di letakan ke dalam Jenkinsfile

```shell
  post {
    always {
   discordSend description: 'Jenkins Build', enableArtifactsList: true, footer: '', image: '', link: '', result: '', scmWebUrl: '', showChangeset: true, thumbnail: '', title: 'Jenkins Literature Backend', webhookURL: 'https://discord.com/api/webhooks/1050591484052766740/fX357kf9wiKUhoSlW2aVwa0WnQK0wmMQb4gP7Tulwi6EULxbmwpFzXazPdPRc4L49e4D'
    }
  }
```


syntax tersebut bisa di generate menggunakan pipeline syntax

pilih discord send webhook

![image](https://user-images.githubusercontent.com/56806850/206852093-2825341e-e5fd-4781-b656-44ff60c19856.png)

dan masukan alamat webhook 

![image](https://user-images.githubusercontent.com/56806850/206852095-b6bb73ce-7725-4d3c-aaed-f0a5e9e57c11.png)


nyalakan ke 2 cheklist tersebut dan generate script

![image](https://user-images.githubusercontent.com/56806850/206852110-1f7038e6-ae88-4710-80c4-7db00591d09b.png)

lalu setelah menambahkan scriot tersebut ke jenkinsfile, secara otomatis setelah pipeline di buuild maka akan muncul notifikasi ke discord. 

![image](https://user-images.githubusercontent.com/56806850/206852134-1bc51549-807b-4951-8cbf-781dcde3703e.png)


