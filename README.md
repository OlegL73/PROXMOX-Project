# Projet Linux – PROXMOX_Project

## État d’avancement
L’infrastructure de base est maintenant en place.  
Proxmox fonctionne correctement sur la machine dédiée au collège, la GNS3 VM est opérationnelle, et nous avons validé la connectivité réseau (VyOS + NAT + accès Internet).  
Le dépôt GitHub est prêt et partagé avec toute l’équipe ainsi qu’avec l’enseignant.  
La prochaine étape importante sera le déploiement automatisé des serveurs avec Ansible.

---

## Infrastructure
Notre environnement actuel repose sur :

- Une machine physique au collège dédiée au projet  
- Proxmox installé comme hyperviseur principal  
- Une GNS3 VM hébergée dans Proxmox  
- VyOS utilisé comme routeur/NAT vers le réseau du collège  
- Réseau interne (LAN) : **192.168.1.0/24**  
- Réseau WAN (DHCP du collège) : **172.16.152.0/24**  
- Accès GNS3 WebUI : **http://172.16.152.227**

Toutes les futures machines Linux seront déployées en IP statique sur le LAN.

---

## Rôles imposés
Les services suivants doivent être déployés et configurés via Ansible :

- Client Ubuntu Desktop en DHCP  
- Serveur DHCP  
- Serveur DNS avec zone locale  
- Serveur Web  
- Serveur Base de données  
- Répartition de charge ou tolérance de panne  
- Sauvegarde (fichiers ou BDD)

---

##Les rôles Ansible terminés :
-  DNS (Bind9) → voir section *DNS (Bind9)* ci-dessous

---

## À faire
Prochaines tâches :

- Déployer les VM Linux dans Proxmox  
- Préparer la structure du projet Ansible (`roles/`, `inventory.ini`, `site.yml`)  
- Écrire et tester chaque rôle obligatoire  
- Vérifier la communication interne entre toutes les VM  
- Finaliser la topologie GNS3  
- Préparer la démonstration du 5 décembre

---

## Architecture réseau

                           Internet
                              |
                     Réseau du collège (DHCP)
                              |
                      172.16.152.0/24 (WAN)
                              |
                   +-----------------------+
                   |     VyOS              |
                   | eth0: DHCP            |
                   | eth1: 192.168.1.254/24|
                   +-----------------------+
                              |
               Réseau interne 192.168.1.0/24
                             |
       ----------------------------------------------
       |        |           |           |            |
     DHCP     DNS        Web server   DB server   Backup
    .10       .20            .30         .40        .50

---

## Accès Proxmox :
- Interface Web Proxmox : https://172.16.152.13:8006 (au collège)
- Accès console des VM : via Proxmox (noVNC)

---

## Accès GNS3 :
- GNS3 WebUI : http://172.16.152.227
- SSH GNS3 VM : ssh gns3@172.16.152.227
  (mot de passe : gns3)
  (IP attribuée par DHCP, peut changer — vérifier sur la console Proxmox)

---

## Ansible – Plan global d’automatisation :

1. Définir l’inventaire (groupes : dhcp, dns, web, db, backup)  
2. Créer la structure (`roles/`, `site.yml`, `inventory.ini`)  
3. Développer un rôle commun (mises à jour, paquets essentiels, SSH)  
4. Implémenter tous les rôles imposés  
5. Tester chaque rôle sur des VM propres
   
### DNS (Bind9)

- Déployé via Ansible sur 192.168.1.20.
- Zone locale `proxmox.lan` opérationnelle.
- Tous les enregistrements A fonctionnent (`web1`, `web2`, `bdd`, `haproxy`, etc.).
- Rôle Ansible complet : templates + tasks + handler.
- Validé avec `named-checkzone`, `named-checkconf` et `dig`.

7. Mettre en place la partie load-balancing / HA  
8. Finaliser la documentation pour la présentation
