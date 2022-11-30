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

kemudian kita buat subneting A dan Subnetting B

![image](https://user-images.githubusercontent.com/56806850/204795799-0bd1afe5-6c41-4840-9587-ec9a24e40ea4.png)

setelah itu pilih dumbflix-A

![image](https://user-images.githubusercontent.com/56806850/204795883-20830bd2-6af6-49ef-88b2-a3ae11afa1dd.png)

kemudian isi subnet pertama dengan format seperti berikut

![image](https://user-images.githubusercontent.com/56806850/204796092-394c7c81-0deb-49e2-a4d2-58579feb6168.png)


dan subnet ke 2 seperti berikut setelah itu create subnet

![image](https://user-images.githubusercontent.com/56806850/204796338-795e9964-67a7-4aa0-ab94-cd23104f22bb.png)


setelah itu kita buat Route tablesnya dengan memilih VPC dumbflix-A

![image](https://user-images.githubusercontent.com/56806850/204796440-f51be3c8-081c-4c16-b4cc-7b4ce1262282.png)
<br/>

![image](https://user-images.githubusercontent.com/56806850/204796535-88709435-dda3-4b2b-9cc8-e836ca33724d.png)


setelah di buat route tablenya kita edit terlebih dahulu.

![image](https://user-images.githubusercontent.com/56806850/204796738-03055d05-b383-4236-b17d-e7e2066ae389.png)

dan centang subnet-A

![image](https://user-images.githubusercontent.com/56806850/204796877-17a0fcf7-76db-4351-b613-c499932c0ac2.png)

kemudian kembali lagi ke route table dan buat route table b

![image](https://user-images.githubusercontent.com/56806850/204797029-15da21cb-9439-43a1-a300-457c0f119d10.png)

dan centang route table b pada assosiasi route table 

![image](https://user-images.githubusercontent.com/56806850/204797171-5fc2375a-ed5b-4c66-8957-742d61d01492.png)

kemudian buat route table antara gateway dengan subnet 

![image](https://user-images.githubusercontent.com/56806850/204797571-682d3a06-5cb1-4ac6-b712-ffea070f0633.png)

tambahkan 0.0.0.0/0 dengan maksud keseluruhan IP yg ada di gateway in maka akan terhubung

piih target internet gateway yg sudah di buat dan pilih dumbflix-a-internetgateway dan create 

![image](https://user-images.githubusercontent.com/56806850/204797917-c3286c95-5436-441f-85f8-1cb06b47b7d7.png)


jika sudah di tambahkan maka route table pada subnet a akan seperti berikut, artinya subnet-A sudah terhubung dengan gateway internat dan menjadi ip public.

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




