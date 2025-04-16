# Rôle de la machine `backup` (192.168.203.94)

## Responsable : Yohan Halimi  
Cette branche contient le travail de configuration et de préparation de la machine de backup dans le cadre du projet Ansible pour le déploiement d'une infrastructure Wordpress.

---

## Objectif de la VM `backup`

Cette machine a pour but de :
- Recevoir les dumps SQL compressés (`.sql.gz`) générés sur la machine `replicat`
- Archiver ces fichiers dans un répertoire dédié (`/var/backups`)
- Assurer la disponibilité et l’intégrité des données sauvegardées

---

## Ce que fait le playbook `prepare_backup.yml`

- Crée un utilisateur Linux `esiee` avec le mot de passe `esiee`
- Crée le dossier `/var/backups`, appartenant à `esiee`
- Autorise la connexion SSH par mot de passe
- Redémarre le service SSH pour appliquer la config
- Active le port SSH dans le pare-feu (UFW)

---

## Tests effectués

- Connexion SSH fonctionnelle avec l’utilisateur `esiee`
- Répertoire `/var/backups` existant, accessible
- Fichiers `.sql.gz` bien transférés depuis la machine `replicat` via `scp`
- Lecture et accès confirmés côté `backup`

---

## Commande de déploiement Ansible utilisée

```bash
ansible-playbook -i inventory.ini playbooks/prepare_backup.yml
