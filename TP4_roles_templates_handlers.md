# TP4 — Rôles, Templates et Handlers dans Ansible

## Objectifs

* Structurer un projet Ansible avec des **rôles**.
* Créer et utiliser des **templates Jinja2** pour générer des fichiers de configuration dynamiques.
* Implémenter des **handlers** pour relancer les services automatiquement après modification.

---

## 1. Introduction aux rôles

Les **rôles** permettent d’organiser les playbooks en modules réutilisables. Un rôle contient ses propres tâches, fichiers, templates et variables.

### Structure type d’un rôle

```
roles/
└── nginx/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   └── nginx.conf.j2
    ├── vars/
    │   └── main.yml
    └── defaults/
        └── main.yml
```

Créer la structure automatiquement :

```bash
ansible-galaxy init roles/nginx
```

---

## 2. Création du rôle NGINX

### `roles/nginx/defaults/main.yml`

```yaml
nginx_port: 8080
web_root: /var/www/html
```

### `roles/nginx/tasks/main.yml`

```yaml
- name: Installer NGINX
  apt:
    name: nginx
    state: present
    update_cache: yes

- name: Déployer la configuration NGINX
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/default
  notify: Reload nginx

- name: S'assurer que NGINX est démarré
  service:
    name: nginx
    state: started
    enabled: yes
```

### `roles/nginx/templates/nginx.conf.j2`

```jinja
server {
    listen {{ nginx_port }};
    root {{ web_root }};

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### `roles/nginx/handlers/main.yml`

```yaml
- name: Reload nginx
  service:
    name: nginx
    state: reloaded
```

---

## 3. Utilisation du rôle dans un Playbook

### `playbooks/site_roles.yml`

```yaml
- name: Déploiement web avec rôle NGINX
  hosts: web
  become: yes
  roles:
    - nginx
```

Exécution :

```bash
ansible-playbook -i inventaires/lab.yml playbooks/site_roles.yml
```

---

## 4. Personnalisation via variables

Les valeurs par défaut du rôle peuvent être **surchargées** dans `group_vars` ou `host_vars` :

### `group_vars/web.yml`

```yaml
nginx_port: 8081
web_root: /srv/site1
```

Puis relancer :

```bash
ansible-playbook playbooks/site_roles.yml
```

Vérifier :

```bash
ansible web -a "ss -ltnp | grep nginx"
```

---

## 5. Ajout d’un second rôle : Base de données

Créer un rôle `roles/db/` avec :

* Installation de `mariadb-server`
* Handler pour redémarrage du service

### `roles/db/tasks/main.yml`

```yaml
- name: Installer MariaDB
  apt:
    name: mariadb-server
    state: present
    update_cache: yes
  notify: Restart MariaDB

- name: S'assurer que MariaDB est actif
  service:
    name: mariadb
    state: started
    enabled: yes
```

### `roles/db/handlers/main.yml`

```yaml
- name: Restart MariaDB
  service:
    name: mariadb
    state: restarted
```

### `playbooks/site_full.yml`

```yaml
- name: Déploiement complet web + DB
  hosts: all
  become: yes
  roles:
    - { role: nginx, when: "'web' in group_names" }
    - { role: db, when: "'db' in group_names" }
```

---

## 6. Exercices

1. Modifier le template NGINX pour afficher le nom de l’hôte (`{{ ansible_hostname }}`).
2. Ajouter un rôle `firewall` qui ouvre le port HTTP via `ufw` ou `firewalld`.
3. Créer un handler `Restart firewall`.
4. Définir des variables par environnement (ex. `group_vars/prod.yml`, `group_vars/dev.yml`).

---

## 7. Bonnes pratiques

* Toujours inclure un `README.md` dans chaque rôle (but, variables, dépendances).
* Préférer les **handlers** plutôt que les redémarrages directs.
* Factoriser les valeurs communes dans `defaults/` plutôt que dans `vars/`.
* Utiliser `tags:` pour exécuter partiellement un rôle :

  ```bash
  ansible-playbook playbooks/site_roles.yml --tags "nginx"
  ```

---

## 8. Pour aller plus loin

* Créez un rôle `users` pour gérer des comptes Unix.
* Combinez vos rôles dans un playbook unique (`site.yml`) pour un déploiement complet.
* Explorez la commande `ansible-galaxy` pour publier vos rôles.

---

*Auteur : Matthieu Minet — IAxLab Inside*
