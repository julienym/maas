# Installation de MAAS sur Proxmox

Note: Tutoriel de l'installation et configuration de MAAS sur Proxmox.



## Installation de MAAS

Connectez vous à XXXXXXXXXXXXXXXXXXX

Username: XXXXXXXXXXXXXXXXXXX

Password: XXXXXXXXXXXXXXXXXXX  

- Créer une VM dans votre "resource pool" ou en appelant votre VM (ex. denis-maas)
  ![image](uploads/07a7b773f04310240cac800a4f93f122/image.png)
  ![image](uploads/549c87637a45c6a8be88892577bc7870/image.png)  
- Choisissez VirtIO et Write Back pour les meilleurs performances
  ![image](uploads/e96572142558a46774c2fe41d1bc342c/image.png)
  ![image](uploads/55bea3d40c493ed97ef0620d11210159/image.png)
  ![image](uploads/a6e48ca62b4956c8c6d6c372c66e9fcb/image.png)  
- Choisissez VMBR 8 - VLAN-MAAS et comme tag VLAN utilisez cette solution (votre âge + la jour de votre naissance x 10, ex. Gustavio 27 ans + 3 janvier * 7 = 48)  
  ![image](uploads/c7baf0985ba5d6a18968d8f6d0f0b46b/image.png)  
- Finissez la création de la VM et démarrez celle-ci. La VM démarrera avec l'ISO d'installation.  
  Choisissez votre langue, et votre clavier. Installez ensuite MAAS en mode region  
  ![image](uploads/1efaad92960c4bb9c27b6bbab9c13940/image.png)  
- Choisissez un compte admin/mot de passe pour MAAS.  Configurez ensuite une IP statique avec le sous-réseau du même VLAN tag. Exemple: 192.168.48.0/24 (Gardons .1 pour le routeur/dns)  
  ![image](uploads/39c4bb996d15cbbf09809073ec1398ec/image.png)
- Continuez avec les valeurs par défaut. Pour le profile, entrez ce que vous désirez. Installez ensuite SSH et importez votre clef SSH si vous l'avez déjà dans GitHub.
  ![image](uploads/dc978f98e8f040a925a8dd39b52311d9/image.png)
- Ceci ne fonctionnera pas car votre réseau n'as pas encore de routeur... votre VM n'as donc pas accès à internet.



## Configuration de pfSense

Connectez vous à XXXXXXXXXXXXXXXXXXX 

Username: XXXXXXXXXXXXXXXXXXX 

Password: XXXXXXXXXXXXXXXXXXX  



- Dans la section VLANs, rajouter une interface VLAN avec l'interface parente vnet8 et votre VLAN tag. Entrez votre nom dans la description.  
  ![image](uploads/45dba3482f1d60ca0205b3ec7d55a66f/image.png)
- Dans la section Interface Assignment, rajouter une interface avec l'interface VLAN crée  
  ![image](uploads/300ecf37cc06726f62279b335d2e2fbf/image.png)  
- Cliquez sur l'interface pour la configurer  
  ![image](uploads/2c784ba44028bf6cd067663f6e974fd4/image.png)  
- Activer l'interface, donnez lui un nom, et choisissez la configuration IPv4 Static  
  ![image](uploads/76820cd4a709b653a83654c4068a8528/image.png)  
- Plus bas sur la page...  
  Entrez l'ip de votre routeur choisit pendant l'installation de MAAS (normalement .1) et votre sous-réseau /24, puis sauvegarder
  ![image](uploads/f02bdcd598d8ca76488469efe5f544ba/image.png)
- Allez dans les règles Firewall, dans votre interface, rajouter une règle:
  ![image](uploads/0815a15428a2e8d71253b8a53f58ab27/image.png)
- Changez le protocol pour Any et sauvegarder
  ![image](uploads/df2911719386051c8b3cb21a32369a4f/image.png)
- N'oubliez pas d'activer vos changements
  ![image](uploads/638ee2b543260c4d6aef69343bc38156/image.png)

- Dans Firewall/NAT, rajouter une règles de port-forwarding NAT.
  Nous ne rajouterons qu'une règle SSH, car nous pourrons tout faire par des tunnels SSH ensuite.
  ![image](uploads/01dd2fd8247ce4e4e3ab2e97c27959f2/image.png)



*Après les modifications dans pfSense, vous pourrez importez votre clef SSH de GitHub et terminez l'installation.*



## Configuration de base de MAAS

- Premièrement, connectons nous a notre VM Maas par SSH:
  ![image](uploads/1cf9fb3a57bbfcfad2a896ff8cf454d2/image.png)

- Si tout marche bien, configurons ensuite notre ssh pour être plus rapide:

```
~/.ssh/config
Host mymaas
        Port 4822
        User julien
        HostName ocean.gologic.ca
```
- Maintenant pour accèder à l'interface web de notre Maas qui écoute sur le port 5240 par un tunnel SSH local:

  ```
  ssh -N mymaas -L 5240:127.0.0.1:5240
  ```

  ![image](uploads/7da7f5e34c234d536ac246aff8e4e044/image.png)

- Première configuration de MAAS:
  ![image](uploads/cf35ac63e4cd300a9208bcdd097617f7/image.png)
- Cliquez sur continue. On peut ensuite importez nos clefs de compte GitHub pour que celles-ci soit disponible dans les VMs/Servers que nous allons gérer/déployer avec MAAS.

![image](uploads/8a1a86d1f16c0053f369f0a954379b7d/image.png)

- MAAS est prêt, mais il reste du travail.
  ![image](uploads/27207da7d93673c43c1cd1cef7b04834/image.png)

