Playbook Ansible – Setup complet de la machine de backup

Description

Ce playbook Ansible configure automatiquement une machine dédiée aux sauvegardes (nommée backup) pour récupérer régulièrement des dumps de bases de données MySQL depuis une machine distante appelée replicat.

L’objectif est de :
	•	Créer un utilisateur dédié aux sauvegardes.
	•	Mettre en place une authentification SSH sans mot de passe.
	•	Déployer un script de récupération de dump MySQL.
	•	Planifier une tâche cron toutes les heures pour effectuer la sauvegarde.

⸻

Structure du Playbook

Hôte ciblé :
	•	backup (doit être défini dans votre fichier d’inventaire Ansible)

Variables utilisées :

Variable	Description
backup_user	Nom de l’utilisateur dédié à la sauvegarde
backup_pass	Mot de passe hashé pour l’utilisateur
replicat_host	IP ou nom DNS de la machine source (replicat)
remote_db_user	Utilisateur MySQL distant
remote_db_pass	Mot de passe MySQL distant
backup_dir	Répertoire local pour stocker les backups



⸻

Étapes réalisées par le playbook
	1.	Création de l’utilisateur backupesiee avec un mot de passe sécurisé.
	2.	Génération d’une clé SSH RSA (4096 bits) pour une connexion sans mot de passe.
	3.	Affichage de la clé publique à copier manuellement sur la machine replicat.
	4.	Création du dossier /var/backups, propriétaire backupesiee.
	5.	Déploiement d’un script fetch_replicat_backup.sh qui :
	•	Se connecte en SSH à replicat
	•	Lance un mysqldump de toutes les bases de données
	•	Compresse et récupère le dump localement
	6.	Création d’une tâche cron pour exécuter ce script toutes les heures.

⸻

Étape manuelle requise

Une fois le playbook exécuté, vous devez copier la clé publique SSH affichée par Ansible sur la machine replicat, dans le fichier ~/.ssh/authorized_keys de l’utilisateur root :

Sur la machine replicat (192.168.203.154)
echo "clé_publique_affichée_par_le_playbook" >> ~/.ssh/authorized_keys



⸻

Prérequis
	•	Accès root à la machine backup
	•	Machine source (replicat) accessible via SSH
	•	Ansible installé sur votre poste d’administration
	•	Entrée dans l’inventaire Ansible :

[backup]
backup-hostname ansible_host=192.168.x.x ansible_user=your_user



⸻

Exemple d’exécution

ansible-playbook setup_backup.yml -i inventory.ini



⸻

Vérifier la tâche cron

Pour vérifier que la tâche est bien en place :

crontab -u backupesiee -l



⸻

Notes
	•	Le script mysqldump utilise le mot de passe en ligne de commande, ce qui peut être visible par d’autres utilisateurs sur la machine. Pour des environnements plus sécurisés, pensez à utiliser un fichier .my.cnf ou un autre mécanisme.
	•	Il est possible de configurer la copie de la clé publique automatiquement via Ansible si l’accès root SSH est autorisé avec mot de passe (non recommandé sans précaution).
