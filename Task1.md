Tugas kali ini adalah membuat 2 server, dengan fungsi server 1 sebagai app server dan server ke 2 sebagai gateway menggunakan nginx digunakan sebagai reverse proxy.
pada pembuatan server ini platform yg akan saya gunakan adalah AWS EC2 yg merupakan bagian dari  Aws cloud computing.

sebelum membuat server mari kita terlebih dahulu VPC(Virtual Private Cloud) yaitu merupakan jaringan virtual yg berada di cloud platform  AWS.

cari VPC pada kolom pencarian dan klik vpc tersebut.

![image](https://user-images.githubusercontent.com/56806850/204792931-8e77a314-1e20-4880-b0e5-965d7e2d00bb.png)

lalu create VPC kemudian buat dengan 

format berikut 


Resource to create : VPC-Only <br/>
Name tag :  dumbflix-A <br/>
Ipv4 CIDR Block : Ipv4 CIDR Manual input br/>
IPv4 CIDR : 10.0.0.0/16 <br/>
IPv6 CIDR Block : NO IPv6 CIDR Block <br/>
Tenancy : Default <br/>

![image](https://user-images.githubusercontent.com/56806850/204794374-206a43ce-1764-4fb9-9e36-87885aaf5c50.png)

Kemudian buat gateway yang akan menghubungkan internet ke  dumbflix-A

![image](https://user-images.githubusercontent.com/56806850/204794642-72b60d13-701f-4e6f-950f-aefd9261d149.png)

isi tag dengan `dumbflix-A-internetgateway`

![image](https://user-images.githubusercontent.com/56806850/204794841-94a8d0af-dd42-4d61-8db5-b44343c187b3.png)

kemudian pasang pada vpc dumbflix-A 

![image](https://user-images.githubusercontent.com/56806850/204794996-54c3c3ce-e2d3-4b1d-94da-c219c2b3212f.png)<br/>
![image](https://user-images.githubusercontent.com/56806850/204795071-74fee5e6-9049-4741-8761-e98e069c2340.png)

maka internet gateway sudah terhubung dengan VPC dumbflix-A dengan IP range 10.0.0.0/16

kemudian kita buat subneting A

![image](https://user-images.githubusercontent.com/56806850/204795799-0bd1afe5-6c41-4840-9587-ec9a24e40ea4.png)

setelah itu pilih dumbflix-A

![image](https://user-images.githubusercontent.com/56806850/204795883-20830bd2-6af6-49ef-88b2-a3ae11afa1dd.png)

kemudian isi subnet pertama dengan format seperti berikut

![image](https://user-images.githubusercontent.com/56806850/204796092-394c7c81-0deb-49e2-a4d2-58579feb6168.png)




setelah itu kita buat Route tablesnya dengan memilih VPC dumbflix-A

![image](https://user-images.githubusercontent.com/56806850/204796440-f51be3c8-081c-4c16-b4cc-7b4ce1262282.png)
<br/>

![image](https://user-images.githubusercontent.com/56806850/204796535-88709435-dda3-4b2b-9cc8-e836ca33724d.png)


setelah di buat route tablenya kita edit terlebih dahulu.

![image](https://user-images.githubusercontent.com/56806850/204796738-03055d05-b383-4236-b17d-e7e2066ae389.png)

dan centang subnet-A

![image](https://user-images.githubusercontent.com/56806850/204822872-3a4ea4cf-bd2d-43a9-8f1e-205c762c3f9c.png)


kemudian buat route table antara gateway dengan subnet 

![image](https://user-images.githubusercontent.com/56806850/204797571-682d3a06-5cb1-4ac6-b712-ffea070f0633.png)

tambahkan 0.0.0.0/0 dengan maksud keseluruhan IP yg ada di gateway in maka akan terhubung

piih target internet gateway yg sudah di buat dan pilih dumbflix-a-internetgateway dan create 

![image](https://user-images.githubusercontent.com/56806850/204797917-c3286c95-5436-441f-85f8-1cb06b47b7d7.png)


jika sudah di tambahkan maka route table pada subnet a akan seperti berikut, artinya subnet-A sudah terhubung dengan gateway internet dan menjadi ip public.

![image](https://user-images.githubusercontent.com/56806850/204798533-fb50614c-268a-42da-8562-bfc8055ea7d1.png)



<br/>

setelah itu kita buat VPS nya 




![image](https://user-images.githubusercontent.com/56806850/204789807-f3648b02-193d-44c0-aa49-5425585fd299.png)
 
 cari EC2 pada kolom pencarian. lalu buat instance baru, tekan tombol launch instance.
 
 ![image](https://user-images.githubusercontent.com/56806850/204789973-c1b0244a-5356-4a1a-a7b4-5fc424b56464.png)


kemudian isi nama VPS dan pilih sistem operasi ubuntu dengan versi 20.04 

![image](https://user-images.githubusercontent.com/56806850/204790314-362476bc-e834-4e04-b024-6d66517afdca.png)


 pilih instance type t2.micro
 
 ![image](https://user-images.githubusercontent.com/56806850/204790409-718ec909-4239-4416-ad1d-e77d6fa22c61.png)

dan untuk bisa mengakses VPS menggunakan SSH tanpa menggunakan password isi key pair (login) dengan ssh yg sudah di buat atau yg sudah ada.
bisa membuat dari CLI atau dari aws. jika membuat dari aws klik `create new key pair` 

![image](https://user-images.githubusercontent.com/56806850/204790604-bf98a758-b00d-4449-9dce-d8248b015855.png)


pilih enkripsi RSA atau ED25519, dan format private key .pem agar bisa di gunakan openSSH. kemudian `create key pair`

![image](https://user-images.githubusercontent.com/56806850/204790792-c0d26990-5d8c-4209-9813-984f7a13946b.png)

file secara otomatis akan di buatkan oleh aws dan tersimpan dalam sistem aws, lalu kita bisa langsung di download satt menekan tombol create key pair

![image](https://user-images.githubusercontent.com/56806850/204790980-4b3308f6-d211-42c8-b05f-9c85239441c7.png)


key pair baru akan di tambahkan pada dropdown key pair 

![image](https://user-images.githubusercontent.com/56806850/204791063-bc9e5d20-9893-4321-82cd-47934d4b01c1.png)

lalu pada network settings pilih edit dan masukan VPC yg sudah di buat sebelumnya.

![image](https://user-images.githubusercontent.com/56806850/204802432-9af56f04-735e-4585-a59d-d02b6f0ff31a.png)


dan pilih subnet-A

![image](https://user-images.githubusercontent.com/56806850/204802561-338aa1d4-f820-4e2a-bab6-c671527e50d2.png)

dan auto-assign public IP di enable.

berikutnya adalah mengatur firewall VPS,untuk mengontrol lalulintas data yg keluar masuk pada jaringan VPS. secara default AWS akan memberikan firewall dengan menginjinkan aplikasi SSH


![image](https://user-images.githubusercontent.com/56806850/204806453-a07aa1cd-d200-488f-b18a-f24db7185237.png)

lalu pada pengaturan storage cukup gunakan 25 GB 

![image](https://user-images.githubusercontent.com/56806850/204806715-63e9de79-420a-418a-b16d-800302b63643.png)


setelah  pengaturan selesai di isi semua maka VPS bisa di buat dengan menekan tombol launch instance. 

![image](https://user-images.githubusercontent.com/56806850/204806844-407e0914-0d88-446c-9b2b-bf9e57968f25.png)


selanjutnya buat VPS untuk gateway Nginx sama dengan cara pembuatan server web app 

samakan jaringan server dengan server web app

![image](https://user-images.githubusercontent.com/56806850/204825445-f412701c-e6bf-45df-bdc1-8ac5642ae703.png)


dan 2 server sudah selesai di setup dengan menggunakan custom VPC dan subnet jaringan yg sama 

![image](https://user-images.githubusercontent.com/56806850/204826317-363b9f17-5bda-4126-ab01-c8d5193ab8bc.png)

![image](https://user-images.githubusercontent.com/56806850/204826384-95a83443-d020-4d32-b22f-c7cc03c530b1.png)


setelah installasi VPS selesai maka untuk menghubungkan vps dengan SSH yang sudah di buat saat pertama kali tadi. menggunakan SSH dumbways-key.pem

dengan menggunakan 

```shell
sudo ssh -i "dumbways-key.pem" ubuntu@<IP SERVER>
```
Ip server disesuaikan dengan IP public yg sudah di buat pada VPS tersebut.


Install web apps dumbflix

```shell
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install -y nodejs
```

![image](https://user-images.githubusercontent.com/56806850/204851182-acc8a1a4-fef1-49b1-b5e3-2fd0a529f756.png)

![image](https://user-images.githubusercontent.com/56806850/204851324-b44210e4-3434-43f6-93a3-6edb924896ae.png)



lalu pastikan dengan cek versi NodeJS dan NPM
lalu clone repository git 

```shell
git clone https://github.com/dumbwaysdev/dumbflix-frontend.git
```

setelah clone selesai masuk ke direktori tersebut dan gunakan `npm install`

![image](https://user-images.githubusercontent.com/56806850/204851424-a815b83c-ebd8-4a01-992b-21534f1d1129.png)

lalu kita test dengan `npm run start`

jika sudah muncul seperti ini maka web sudah bisa di akses melalui IP tersebut.tetapi sebelum  membuka web tersebut. karena port 3000 belum di buka maka ubah pengaturan pada AWS dengan menambahkan port 3000 pada firewalll vps

![image](https://user-images.githubusercontent.com/56806850/204859170-cbf6fcad-3f7e-4a0c-8c60-277b560fa888.png)


Membuka port pada AWS  klik pada sercurity groups lalu edit inbounds rules pada bawah kanan
![image](https://user-images.githubusercontent.com/56806850/204860043-fde2b59c-6433-4990-a9b5-b423a4183b3c.png)

![image](https://user-images.githubusercontent.com/56806850/204860272-ff2bdb72-e744-424d-a9b2-22c5be09edd3.png)

tambahkan custom TCP dan port 3000 lalu gunakan IP 0.0.0.0/0

![image](https://user-images.githubusercontent.com/56806850/204860469-db5e4420-1b73-4432-bb7d-bd546dfc797c.png)

kemudian sesudah web app bisa di jalankan install pm2  

```shell
npm install pm2 -g
```

![image](https://user-images.githubusercontent.com/56806850/204942521-b5b0cff0-2fd1-4f3a-8aaa-46bdcfdfd5b1.png)

gunakan command berikut

```shell
sudo npm2 start "npm run start" --name dumbflix
```

![image](https://user-images.githubusercontent.com/56806850/204943195-690f23b1-d2dd-4906-b496-a273ae0f0bdb.png)


dan karena sudah berjalan di background web-app dumbflix makaa website sudah bisa langsung di akses menggunakan IP public server 

![image](https://user-images.githubusercontent.com/56806850/204944225-b0512f32-1c25-423f-b3da-d3bc3c7875f7.png)

![image](https://user-images.githubusercontent.com/56806850/204944147-bfbf88e2-8316-43c3-a241-5b2fd6e2d84b.png)


kemudian install database mysql 

```shell
sudo apt install mysql-server
```

![image](https://user-images.githubusercontent.com/56806850/204944425-142a2397-6492-456c-8af9-2984e5f49e74.png)


kemudian jalankan service mysql

```shell
sudo systemctl status mysql.service
```


![image](https://user-images.githubusercontent.com/56806850/204946257-efc466ac-c641-445a-9e74-0fcb29c4b88b.png)


setelah installasi jalankan `mysql_secure_installation` untuk mengamankan mysql agar ketika mengakases mysql menggunakan password, pilih sesuai dengan kombinasi password minimal medium

![image](https://user-images.githubusercontent.com/56806850/204948358-3cccf4ff-ed13-4c84-a4bc-549462a021be.png)


ketika masuk ke secure installation maka akan di tanyakan  unntuk menghapus user anonymous dan database test
ketik `Y` saja kecuali untu root login remotly


untuk memudahkan migrasi database nodeJS ke mysql kita gunakan squelize, install terlebih dahuli sequelize

```shell
sudo npm install --save sequelize-cli
```
kemudian install manual driver database mysql

```shell
sudo npm install --save mysql2
```

lalu setelah itu 


inisialisasi folder dengan command sequalize 

```shell
npx sequalize-cli init
```

kemudian atur pada config.json terletak pada folder config isi username dan password sesuai user yg akan di gunakan

![image](https://user-images.githubusercontent.com/56806850/204979574-936b93f9-4d93-4442-a22b-93cc4b3a427f.png)


lalu buat database terlebih dahulu 

![image](https://user-images.githubusercontent.com/56806850/204979615-19d72fd7-3367-4472-9f61-080efb8733ed.png)

setelah database sudah di buat database yg ada pada web-app bisa di migrasi ke database server menggunakan db:migrate sequalize

```shell
npx sequalize-cli db:migrate
```
![image](https://user-images.githubusercontent.com/56806850/204979793-13d1dc57-439d-4e60-9415-3e1ccd4ae083.png)




Install Nginx


pertama jalan kan 

```shell
sudo apt update &&& sudo apt upgrade
```
setelah itu instann nginx server


```shell
sudo apt install nginx
```
![image](https://user-images.githubusercontent.com/56806850/205067831-30486210-a8b8-4c31-8130-2ff77c9279ff.png)

cek kembali nginx sudah terinstall atau belum 

![image](https://user-images.githubusercontent.com/56806850/205068009-b4226b4d-3293-49df-869a-9d78f3da45f7.png)

lalu tambahkan reverse proxy pada conf nginx

dengan caara membuat direktori baru pada /etc/nginx

lalu buat file type .conf

dan isi dengan format berikut 

![image](https://user-images.githubusercontent.com/56806850/205072243-8d99a525-c024-49c5-86ed-e239c345610a.png)

kemudian tambahkan pada ngnix conf
tambahkan 

```shell
include /etc/nginx/dumbflix/*;
```

ketika di test akan muncul seperti ini 

![image](https://user-images.githubusercontent.com/56806850/205072349-c5341aa2-a5d5-494f-a214-67180925bc9b.png)


edit pada conf nginx untuk menambahkan name hash. hilangkan tanda pagar pade server_names_hash_bucket_size

![image](https://user-images.githubusercontent.com/56806850/205072524-663ca1ad-0400-4f93-8c56-bfcda1fc54d3.png)
![image](https://user-images.githubusercontent.com/56806850/205072588-9407bd6b-de26-4d4f-a0cb-3246867f77d0.png)

lalu test kembali dan ngnix konfigurasi berhasil di settings

![image](https://user-images.githubusercontent.com/56806850/205072680-8efea832-919a-41b3-a913-9d7dff415357.png)


setelah configurasi nginx selesai, buka firewall HTTP dan HTTPS pada server nginx

![image](https://user-images.githubusercontent.com/56806850/205079877-57e8dd50-f254-4179-8961-8b9bbbc7f18d.png)

lalu tambahkan IP nginx ke dns record dengan type record A, tambahkan 2 untuk topdomain.  `aziz` dan `api.aziz`

![image](https://user-images.githubusercontent.com/56806850/205080267-903910a3-8c56-4d5e-b998-518579c6473d.png)






