---
title: 'TP1 Open-Source :'

---

# TP Compilattion de drivers : 

### Installation de Vagrant : 

> Téléchargement de vragrant sur windows depuis : https://developer.hashicorp.com/vagrant/install?product_intent=vagrant
> 
> - Configuration  du Vagrantfile pour utiliser une machine ubuntu 
> - Exécuter la commande `vagrant up` dans un terminal en tant qu'admin.
> - Exécuter la commande `vagrant ssh`.
> 
> On à maintenant accès ç la VM.

### Installation des drivers : 

> Sur la VM : 
```
sudo apt install linux-headers-$(uname -r)
```
![image](https://hackmd.io/_uploads/Sy0SOUBp1g.png)

> Téléchargement du driver sur la machine hôte depuis https://www.marvell.com:
> ![image](https://hackmd.io/_uploads/ByV4tUHTkx.png)

> Transfert du driver sur la VM  : `scp .\Linux_Source_41xxx_8.74.6.0.zip vagrant@172.24.1.141:22` : 
![image](https://hackmd.io/_uploads/ryiJ5Lrayg.png)

> Extraction des fichiers : `unzip Linux_Source_41xxx_8.74.6.0.zip`
![image](https://hackmd.io/_uploads/rytLjLraye.png)

> Extraction de "fastlinq-8.74.6.0.tar.bz2" : `tar -xvjf fastlinq-8.74.6.0.tar.bz2`
![image](https://hackmd.io/_uploads/HJS6iLHTyg.png)

### Compilation du driver : 
> ```
> cd fastlinq-8.74.6.0
> make
> ```
### test du driver : 
> ```
> sudo modprobe qed
> lsmod | grep qed
> sudo rmmod qed
> lsmod | grep qed
> ```

![image](https://hackmd.io/_uploads/B1e2hUSpke.png)
