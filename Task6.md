<h1> Using Ansible to manage Server </h1>

<h2> Install ansible </h2> 

tambahkan repositori ansible  dan update 

```shell
sudo apt-add-repository ppa:ansible/ansible && sudo apt update
```

kemudian install ansible 


```shell
sudo apt install ansible
```

setelah install buatlah file ansible.cfg dan Inventoro


isi dari ansible.cfg

```shell
[defaults]
inventory = Inventory
private_key_file = sshkey.pem
```


isi Inventory merupakan IP yg ingin di manage menggunakan ansible 

```shell
[appserver]
103.187.146.241 ansible_user=aziz

[gateway]
103.187.146.54 ansible_user=aziz

```


pertama membuat playbookk base, berisi untuk menginstall docker pada semua server

`base-playbook.yml`
```shell
---
  - hosts: all
    become: true
    vars:
      created_username: aziz
    
    tasks:
      - name: Install aptitude
        apt:
          name: aptitude
          state: latest
          update_cache: true

      - name: Install docker requirement
        apt:
          pkg: 
            - apt-transport-https
            - ca-certificates
            - curl
            - software-properties-common

      - name: docker GPG apt key
        apt_key:
          url: https://download.docker.com/linux/ubuntu/gpg
          state: present

      - name: Add Docker Repository
        apt_repository:
          repo: "deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ansible_distribution_release}} stable"
          state: present

      - name: install docker ce
        apt:
          name: docker.io
          state: latest
          update_cache: true
      
      - name: add docker to group without sudo
        user:
          name: aziz
          groups: docker
          append: yes
```

check terlebih dahulu apakah terdapat error pada yml

```shell
ansible-playbook base-playbook.yml --check
```
jika tidak ada error maka bisa langusng jalankan yml nya tanpa menggunakan options `--check`
```shell
ansible-playbook base-playbook.yml
```

![image](https://user-images.githubusercontent.com/56806850/207884577-7a556250-e58c-47a5-880e-51afaa230212.png)


installasi selesai. check kedua server menggunakan ansible

![image](https://user-images.githubusercontent.com/56806850/207884997-fa4167a8-a1f7-42bc-a5ae-a3f865424dc5.png)


```shell
ansible all -a "docker --version" -u aziz
```
