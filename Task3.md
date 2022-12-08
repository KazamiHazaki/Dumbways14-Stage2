<h1> Docker Swarm </h1>

setelah install docker pada VPS lalu kita inisiasi vps sebagai docker swarm manager

```shell
docker  swarm init
```
setelah muncul docker swarm join maka hubungkan docker worker ke node manager

![image](https://user-images.githubusercontent.com/56806850/206350662-f86f605a-8048-444e-9067-66cf066c1d1c.png)

sebelum menghubungkan worker ke manager buka terlebih dahulu port 2377 pada manager. lalu hubungkan worker dengan node manager

![image](https://user-images.githubusercontent.com/56806850/206351078-efabf807-b1a3-4177-9355-ec84c80065f2.png)

lalu cek pada node manager menggunakan `docker node ls`

![image](https://user-images.githubusercontent.com/56806850/206351166-0df36962-9965-469e-b13b-7c8cef9e6ae0.png)

