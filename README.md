# Gestion de RAID sur Linux

___

Prérequis :   

VM Linux 22.04 LTS  
disque 1 système   +    3 disques de 5 go branchables à chaud   

___

🔬 **Création d'un RAID 1**    

___

**A- Partitionnement des disques**  

Dans le terminal, installe l'outil **_mdadm_** avec **_sudo apt install mdadm_**  

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/d1b61b24-e52d-4e35-8922-fd020b6736eb)

Avec la commande lsblk on peut voir les 3 disques qui vont servir au RAID :   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/508fe496-d8da-4c4e-952a-0a79ecfee91f)

Partitionner le disque sdb avec la commande fdisk /dev/sdb. Cela ouvre l'utilitaire de partitionnement.   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/7721db04-4c26-4613-90a5-5c07ab432d39)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/5632241c-f393-4d95-a91a-fdfee1827a20)

_____

**B- Création du RAID**  

Exécute la commande sudo mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sdb1 /dev/sdc1   

Explications de cette commande :   

/dev/md0 : nom du périphérique RAID à créer  
--level 1 : niveau de RAID, soit ici du RAID 1 (on peut aussi écrire --level=1)   
--raid-devices 2 : le nombre de disques, soit ici 2 (on peut aussi écrire --raid-devices=2)   
/dev/sdb1 /dev/sdc1 : les partitions des disques concernés  

Pour vérifier l'état du RAID, il faut aller voir le contenu du fichier mdstat :   
Tu peux aussi voir l'état du RAID avec la commande mdadm :  

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/01c595d1-7047-43f3-9578-b5828a3b0e31)

On peut aussi voir les disques inclus dans le RAID md0 avec la commande lsblk -f.   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/4c8f33da-3df1-4ff5-96ef-2393b04fba72)

___

**C- Formatage du RAID**

Formate le volume RAID md0 avec un file system en ext4 et avec le nom PersonalData.   
Si tu relances la commande lsblk -f, tu vois que md0 est formaté en ext4 et que le nom est PersonalData :   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/5d4efb96-dc99-4caf-be18-9948124bcd84)

____

**D- Montage du RAID**  

Ajoute un dossier Data-Raid1 sous /home/beatrice, pour monter la partition md0 :   
Montes ensuite la partition md0 dans ce dossier :  
Pour que le RAID soit monté automatiquement à chaque démarrage, il faut ajouter la ligne suivante au fichier /etc/fstab :   

/dev/md0 /home/wilder/Data-RAID1 ext4 nofail 0 0  

Qu'indique cette ligne :   

/dev/md0 : Le RAID  
/home/wilder/Data-RAID1 : Le point de montage de la partition RAID   
ext4 : Le FS choisi pour le RAID   
nofail : Avec cette option, il n'y aura pas de blocage au boot s'il y a un problème avec md0.    

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/bce1cc05-655e-443b-85bf-0c6532a91ac8)

