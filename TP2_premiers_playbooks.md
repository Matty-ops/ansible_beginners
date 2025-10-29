# TP2 — Premiers Playbooks avec Ansible

## Objectifs

* Comprendre les “facts” collectés automatiquement par Ansible.
* Rédiger et exécuter un premier playbook.
* Installer et configurer un serveur web et une base de données.

---

## 1. Observation des facts

Lancer la collecte d’informations :

```bash
ansible all -m setup
```

Filtrer une variable précise :

```bash
ansible all -m setup -a "filter=ansible_distribution"
```

---

## 2. Création d’un premier playbook

### `playbooks/site.yml`

```yaml
- name: Installation du serveur web
  hosts: web
  become: yes
  tasks:
    - name: Installer nginx
      apt:
        name: nginx
        state: present
        update_cache: yes
    - name: Démarrer nginx
      service:
        name: nginx
        state: started
        enabled: yes

- name: Installation de la base de données
  hosts: db
  become: yes
  tasks:
    - name: Installer MariaDB
      apt:
        name: mariadb-server
        state: present
        update_cache: yes
    - name: Démarrer MariaDB
      service:
        name: mariadb
        state: started
        enabled: yes
```

Exécution :

```bash
ansible-playbook -i inventaires/lab.yml playbooks/site.yml
```

---

## 3. Vérifications

* Serveur web :

  ```bash
  ansible web -a "curl -I localhost"
  ```
* Base de données :

  ```bash
  ansible db -a "systemctl status mariadb"
  ```

---

## 4. Exercices supplémentaires

1. Modifier le playbook pour installer `apache2` au lieu de `nginx`.
2. Ajouter une variable `db_engine` dans `group_vars/db.yml`.
3. Utiliser `when:` pour choisir le paquet selon `db_engine`.

---

## 5. Bonus : Découverte des facts dans un playbook

Ajouter une tâche d’affichage :

```yaml
- name: Afficher le nom du système
  debug:
    var: ansible_hostname
```

Vous pouvez aussi enregistrer les facts dans un fichier :

```bash
ansible web -m setup --tree facts/
```

