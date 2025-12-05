
# Rôle db-backup
Déploie scripts de sauvegarde MariaDB (full + incr si binlogs) et vérification de restauration en base de test.

## Variables clés
- `mariadb_databases`: liste des bases à sauvegarder
- `mariadb_dump_user` / `mariadb_dump_pass`: compte de dump (utiliser ansible-vault pour le mot de passe)
- `enable_binlog`: ne modifie pas MariaDB si `false`
- `backup_mountpoint`, `backup_dir_db`, `backup_status_dir`: chemins NFS

## Dépendances
- Le partage NFS doit être monté (via rôle `common`).
- Le serveur BACKUP doit exporter `/srv/backups` (rôle `backup-server`).

## Sécurité
- Les scripts lisent la BDD, n’altèrent pas la prod.
- La restauration se fait dans `*_staging`.

