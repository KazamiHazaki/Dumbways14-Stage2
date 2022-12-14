<h1> Deploy Web-app With Docker </h1>

<h2> Install Docker </h2>

pertama update list package pada ubuntu karena fresh install

```shell
sudo apt update
```

![image](https://user-images.githubusercontent.com/56806850/205819899-16dd96a1-9aaf-43fa-8f7c-92b52a1cd8e8.png)


kemudian install  package yg di butuhkan sebelum install Docker 

```shell
sudo apt install apt-transport-https ca-certificates curl software-properties-common

```

![image](https://user-images.githubusercontent.com/56806850/205821533-2f7d2680-2976-4584-bfc4-93422987c9d1.png)


lalu menambahkan Official GPG Key ke repository Server 

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

![image](https://user-images.githubusercontent.com/56806850/205821756-77dcfeeb-d4d9-4642-a366-22bc1b89c13e.png)

Menambahkan repository docker ke APT 


```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
![image](https://user-images.githubusercontent.com/56806850/205821901-9636dd35-07ac-40ca-ac21-527fce76bbf5.png)

Lalu install Docker 

```shell
sudo apt install docker-ce
```
![image](https://user-images.githubusercontent.com/56806850/205822136-2feb81bf-81d8-41a4-87d4-bc37df0322b8.png)

Kemudian check apakah docker sudah berjalan atau belum 

```shell
sudo systemctl status docker
```

![image](https://user-images.githubusercontent.com/56806850/205822493-0ca21df8-77e1-44b4-88dd-ace0da69a34d.png)

maka docker siap di gunakan, test dengan docker hello world

```shell
sudo docker run hello-world
```

![image](https://user-images.githubusercontent.com/56806850/205822650-abd26026-9837-4d47-9a69-9e95041ee76c.png)


pada penggunaan docker di haruskan menggunakan sudo atau root user, untuk menghilakan penggunaan sudo/root maka rubah menggunakan usermod

```shell 
sudo usermod -aG docker ${USER}
```
![image](https://user-images.githubusercontent.com/56806850/205822965-f4c71c6c-7500-4691-b51a-5bd497a4923a.png)

pada penggunaan command tersebut akan menambahkan docker ke dalam group user. 

kemudian tambahkan user app ke dalam super user , dan masukkan password root

```shell
su - ${USER}
```
![image](https://user-images.githubusercontent.com/56806850/205823217-4c9cfe90-ed54-4bde-9e2d-19a4e2b280b4.png)

lalu pastikan user app sudah masuk kedalam group

```shell
groups
```
![image](https://user-images.githubusercontent.com/56806850/205823306-54c3ac83-f27e-4d4b-a80c-f2f18254265d.png)


<h2> Deploy Literature Backend </h2>

clone terlebih dahulu repositorinya 

```shell
git clone https://github.com/dumbwaysdev/literature-backend.git
```
![image](https://user-images.githubusercontent.com/56806850/205825389-fe6d7cbb-0038-43b8-a466-efeab0d17d14.png)


lalu kita buat terlebih dahulu images backend deengan mempersiapkan `Dockerfile`


![image](https://user-images.githubusercontent.com/56806850/206176778-0e470a13-3f8e-4cc3-92c0-01ebd7a1b5eb.png)

```shell
FROM node:19.2-alpine
WORKDIR /app
COPY . .
RUN npm install
RUN npm install -g sequelize-cli
RUN npx sequelize db:migrate
EXPOSE 5000
CMD ["npm","start"]
```

kemudian kita buat imagenya menggunakan ` docker build `

```shell
docker build --tag literature-be:1.2 .
```
![image](https://user-images.githubusercontent.com/56806850/205839275-88307225-ef9e-44b9-a571-95e1a6fcc66a.png)



setelah membuat dockerfile kemudian buat `docker-compose.yml`

![image](https://user-images.githubusercontent.com/56806850/206177283-d75cf601-18ee-44d7-84a6-4d69b9290163.png)

```shell
version: "3.8"
services:
   mysql_db:
    image: mysql
    container_name: literature_mysql_db
    restart: always
    stdin_open: true
    ports:
     - "3306:3306"
    environment:
     - MYSQL_ROOT_PASSWORD=root
     - MYSQL_USER=kel2
     - MYSQL_PASSWORD=Literature1
     - MYSQL_DATABASE=literature

    volumes:
     - "/home/app/database:/var/lib/mysql"

   literature_be:
    build: .
    container_name: literature_be
    ports:
     - 5000:5000
    depends_on:
     - mysql_db
```


lalu kita rubah configurasi pada folder config backend dengan IP public dan user password database sesuai `docker-compose.yml`


![image](https://user-images.githubusercontent.com/56806850/206177592-054e3636-3b0e-4104-9b48-73288d38d663.png)

setelah semuanya di persiapkan jalankan dengan menggunakan `docker-compose up -d`

![image](https://user-images.githubusercontent.com/56806850/206177779-c94f904e-b01e-472e-a72b-dc3a4d84135e.png)

jika tidak ada error maka container akan langsung berjalan  check menggunakan

```shell
docker container ps


<h2> Upload images to docker-hub </h2>


sebelum mengupload ke docker hub login terlebih dahulu akun docker hub pada server

```ssh
docker login
```
kemudian isi username dan password akun, lalu
build dengan menggunakan tag username akun docker hub.

```shell
docker build -t kazamisei98/literature-be:1.0 .
```

![image](https://user-images.githubusercontent.com/56806850/206129626-c8d685ca-fadd-4b26-a719-9fbb90f71057.png)

![image](https://user-images.githubusercontent.com/56806850/206177057-bee6d27b-02eb-42a6-9f34-d39373011541.png)

```

kemudian update pada docker-compose literature-be, menggunakan images user `kazamisei98`

```shell
literature_be:
    image: kazamisei98/literature-be:1.3
    container_name: literature_be
    ports:
     - 5000:5000
    depends_on:
     - mysql_db

```


![image](https://user-images.githubusercontent.com/56806850/206169756-ded2c7ef-fa20-4a24-aa73-72c76fe4a08b.png)




<h2> Reverse Proxy NGINX </h2>

setelah install nginx pada server tambahkan folder baru untuk file configuration nya disini menambahkan folder `reverse`

![image](https://user-images.githubusercontent.com/56806850/206171587-a832dee3-6f81-4d7f-8719-b724b947dbc8.png)


kemudian buat 2 file untuk reverse proxy.


![image](https://user-images.githubusercontent.com/56806850/206174228-0d14d150-3dfc-43f0-b67c-b017c966a0c7.png)


untuk informasi installasi nginx bisa melihat pada link berikut.

https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04


<h2> mengubah DNS record dengan cloudflare </h2>

untuk mengubah DNS cukup mudah menggunakan cloudflare. syaratnya adalah memiliki domain sendiri. pada tugas ini menggunakan domain `studentdumbways.my.id`

lalu masuk ke dns record dan tambahkan domain baru dengan menghubungkan ke IP public VPS

![image](https://user-images.githubusercontent.com/56806850/206173435-ee0b3ff3-b2d6-4db2-8276-0ebd87b3a83c.png)


setelah melakukan reverse proxy menggunakan nginx maka domain dengan `kelompok2.studentdumbways.my.id` dapat di akses, domain tersebut mengarah ke IP `http://103.189.235.91:3000`

![image](https://user-images.githubusercontent.com/56806850/206178472-a9d9858d-edd9-41ab-80fb-56d9f42810eb.png)

dan untuk API jg sudah bisa di akses dengan domain `api.studentdumbways.my.id`

![image](https://user-images.githubusercontent.com/56806850/206178593-2cec4662-18eb-4d13-beb9-b0a0612310f7.png)

<h2> install SSL Certificate </h2>
