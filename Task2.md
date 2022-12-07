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


![image](https://user-images.githubusercontent.com/56806850/205837907-a15ab03a-7015-4af1-b152-1f346adb3ecc.png)


```shell
FROM node:10.0-alpine  
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3001
CMD ["npm","run","start"]
```

kemudian kita buat imagenya menggunakan ` docker build `

```shell
docker build --tag literature-be:1.0 .
```

![image](https://user-images.githubusercontent.com/56806850/205839275-88307225-ef9e-44b9-a571-95e1a6fcc66a.png)



<h2> Upload images to docker-hub </h2>


sebelum mengupload ke docker hub login terlebih dahulu akun docker hub pada server

```ssh
docker login
```
kemudian isi username dan password akun, lalu
build dengan menggunakan tag username akun docker hub.

```shell
docker build -t kazamisei98/literature-be:1.3 .
```

![image](https://user-images.githubusercontent.com/56806850/206129626-c8d685ca-fadd-4b26-a719-9fbb90f71057.png)


