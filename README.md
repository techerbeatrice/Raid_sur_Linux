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

Avec la commande **lsblk** on peut voir les 3 disques qui vont servir au RAID :   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/508fe496-d8da-4c4e-952a-0a79ecfee91f)

Partitionner le disque sdb avec la commande **fdisk /dev/sdb**. Cela ouvre l'utilitaire de partitionnement.   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/7721db04-4c26-4613-90a5-5c07ab432d39)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/5632241c-f393-4d95-a91a-fdfee1827a20)

_____

**B- Création du RAID**  

Exécute la commande **sudo mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sdb1 /dev/sdc1**   

Explications de cette commande :   

**/dev/md0** : nom du périphérique RAID à créer  
**--level 1** : niveau de RAID, soit ici du RAID 1 (on peut aussi écrire --level=1)   
**--raid-devices 2** : le nombre de disques, soit ici 2 (on peut aussi écrire **--raid-devices=2**)   
**/dev/sdb1 /dev/sdc1** : les partitions des disques concernés  

Pour vérifier l'état du RAID, il faut aller voir le contenu du fichier **mdstat** :   
Tu peux aussi voir l'état du RAID avec la commande **mdadm** :  

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/01c595d1-7047-43f3-9578-b5828a3b0e31)

On peut aussi voir les disques inclus dans le RAID md0 avec la commande **lsblk -f**.   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/4c8f33da-3df1-4ff5-96ef-2393b04fba72)

___

**C- Formatage du RAID**

Formate le volume RAID **md0** avec un file system en ext4 et avec le nom **PersonalData**.   
Si tu relances la commande **lsblk -f**, tu vois que md0 est formaté en **ext4** et que le nom est **PersonalData** :   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/5d4efb96-dc99-4caf-be18-9948124bcd84)

____

**D- Montage du RAID**  

Ajoute un dossier **Data-Raid1** sous **/home/beatrice**, pour monter la partition **md0** :   
Monte ensuite la partition **md0** dans ce dossier :  
Pour que le RAID soit monté automatiquement à chaque démarrage, il faut ajouter la ligne suivante au fichier **/etc/fstab** :   

**/dev/md0 /home/wilder/Data-RAID1 ext4 nofail 0 0**  

Qu'indique cette ligne :   

**/dev/md0** : Le RAID  
**/home/beatrice/Data-RAID1** : Le point de montage de la partition RAID   
**ext4 : Le FS choisi pour le RAID**   
**nofail** : Avec cette option, il n'y aura pas de blocage au boot s'il y a un problème avec **md0**.    

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/3526f705-ddd0-4692-a909-e82b0fe86eb5)

___

**E- Verrouillage du nom md0 de la partition RAID**  

La commande **sudo mdadm --detail --scan** donne le résultat suivant :

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/9e02fee1-a653-462d-adf0-3080bf0be7b4)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/c3e9d651-1382-43e1-91a8-7b56aef43dd1)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/cb14c1c7-9148-4f9b-beb4-d4f75b27d2e0)

___

🔧 **Test du bon fonctionnement du RAID 1**  

___

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/9238c08e-70a5-4e32-aa80-94887966ba88)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/a265f2e2-f660-4901-bae4-a171131cf1bc)

___

🔧 **Simulation d'une panne**  

__

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/8f344d7a-df70-4f57-913d-298f4cc49c83)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/d0e954f8-8768-4824-983c-70ac33823f90)


Pour enlever un disque, tu peux aussi utiliser la commande sudo umount /dev/sdX directement dans la VM Ubuntu.   

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/bbc2dc77-e5f3-4b8a-beda-68aee6ed428b)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/5664bc8a-efdc-40ed-8ca3-f1d94ddd245e)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/4c1e4902-4b86-470e-9dcf-6ff50c15bf95)

![image](https://github.com/techerbeatrice/Raid_sur_Linux/assets/138071140/3c1578ff-b291-4380-a609-0049778296ad)
