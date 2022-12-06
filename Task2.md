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



