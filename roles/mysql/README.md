# Rôle mysql
Ce rôle orchestre le laboratoire MySQL (sauvegarde, restauration, incrémentales) en appelant :
- backup : configuration NFS serveur
- bdd : import MGvpc, NFS client, binlogs, sauvegardes, script+cron
- web1/web2 : tests RW/RO
