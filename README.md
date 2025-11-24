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
6. Mettre en place la partie load-balancing / HA
7. Finaliser la documentation pour la présentation

---

### DNS (Bind9)

- Déployé via Ansible sur 192.168.1.20.
- Zone locale `proxmox.lan` opérationnelle.
- Tous les enregistrements A fonctionnent (`web1`, `web2`, `bdd`, `haproxy`, etc.).
- Rôle Ansible complet : templates + tasks + handler.
- Validé avec `named-checkzone`, `named-checkconf` et `dig`.


---

Suivi hebdomadaire – Projet Linux (Proxmox + Ansible)

---
Semaine du 14 — 17 novembre
Fait :
•	Réception officielle de l’énoncé du projet Linux (14 novembre).
•	Installation de Proxmox sur la machine physique du collège.
•	Mise en place du routeur VyOS (WAN DHCP / LAN 192.168.1.0/24).
•	Installation de la GNS3 VM dans Proxmox.
•	Tests initiaux de connectivité (VyOS ↔ GNS3 ↔ Internet).
•	Création du dépôt GitHub du projet + ajout du professeur.
•	Mise en place du tableau « Projects » (Kanban) sur GitHub.
À faire :
•	Déployer les premières VM Linux (DHCP, DNS, Web, DB, Backup).
•	Préparer la structure initiale du projet Ansible (roles/, inventory, ansible.cfg).

Semaine du 18 — 24 novembre
Fait :
•	Mise en place complète de la structure Ansible.
•	Déploiement du serveur Ansible (192.168.1.5).
•	Développement du rôle DNS (Bind9) avec templates, tasks, handlers et variables.
•	Correction des erreurs DNS (zone invalide proxmox_oaa.lan).
•	Renommage complet de la zone en proxmox.lan.
•	Validation technique complète : named-checkzone OK, named-checkconf OK, dig OK.
•	Mise à jour du README.md et sauvegarde dans GitHub.
À faire :
•	Créer les rôles suivants : DHCP, Web (Apache), DB (MariaDB), HAProxy, Sauvegarde.
•	Tester la communication interne entre toutes les VM.
•	Préparer la démonstration du 5 décembre.
---
# Rôle Ansible : bdd (Base de données MySQL/MariaDB)

## 🎯 Objectif
Ce rôle Ansible installe, configure et valide un serveur de base de données MySQL/MariaDB. Il gère les utilisateurs, les permissions, et vérifie l'accès aux données via des requêtes SQL.

## 📦 Fonctionnalités
- Installation du paquet `mariadb-server`
- Configuration du service et du fichier `my.cnf`
- Création de bases de données et d’utilisateurs
- Attribution de privilèges
- Vérification de l’accès et manipulation des données (CRUD)
- 
Utilisation
bash
ansible-playbook -i inventory.yml site.yml
-
 Variables

Définies dans group_vars/databases.yml et host_vars/bdd.yml.
# Journal des activités

## [2025-11-24] Initialisation du rôle `bdd`
- Création de l’arborescence `roles/bdd`
- Ajout des fichiers `main.yml` dans `defaults`, `handlers`, `tasks`
- Création du template `my.cnf.j2`
- Ajout des variables dans `group_vars` et `host_vars`
- Mise en place du playbook `site.yml`
- Tests de connexion SSH et sudo
- Résolution des erreurs YAML et sudo-rs
- Validation des accès et manipulation des données via requêtes SQL

