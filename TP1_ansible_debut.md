# TP1 — Découverte d’Ansible et premiers pas

## Objectifs

* Comprendre le rôle d’Ansible.
* Créer un inventaire YAML.
* Configurer les accès SSH aux hôtes gérés.
* Utiliser des variables `group_vars` et `host_vars`.
* Exécuter des commandes ad-hoc.

---

## 1. Installation d’Ansible

Sur la machine de contrôle :

```bash
sudo apt update && sudo apt install -y ansible
ansible --version
```

---

## 2. Création d’une clé SSH commune

1. **Générer une clé SSH** (si inexistante) :

   ```bash
   ssh-keygen -t ed25519 -C "ansible@control"
   ```

   -> Appuyez sur **Entrée** pour accepter les valeurs par défaut.

2. **Copier la clé sur chaque hôte** :

   ```bash
   ssh-copy-id user@web1
   ssh-copy-id user@db1
   ```

3. **Tester la connexion SSH** :

   ```bash
   ssh user@web1
   exit
   ```

---

## 3. Création d’un inventaire YAML

### `inventaires/lab.yml`

```yaml
all:
  vars:
    ansible_user: user
    ansible_ssh_common_args: "-o StrictHostKeyChecking=no"

  children:
    web:
      hosts:
        web1:
          ansible_host: 192.168.56.11
    db:
      hosts:
        db1:
          ansible_host: 192.168.56.21
```

Tester l’inventaire :

```bash
ansible-inventory -i inventaires/lab.yml --graph
ansible -i inventaires/lab.yml all -m ping
```

---

## 4. Utilisation de `group_vars` et `host_vars`

### Structure

```
group_vars/
├── all.yml
├── web.yml
└── db.yml
host_vars/
├── web1.yml
└── db1.yml
```

### Exemple de contenu

**`group_vars/all.yml`**

```yaml
timezone: Europe/Paris
```

**`group_vars/web.yml`**

```yaml
web_port: 8080
```

**`host_vars/web1.yml`**

```yaml
web_port: 8081
```

Afficher les variables :

```bash
ansible all -m debug -a "var=web_port"
```

---

## 5. Premières commandes ad-hoc

### Exemples utiles :

* Lister les hôtes :

  ```bash
  ansible all --list-hosts
  ```

* Exécuter une commande :

  ```bash
  ansible all -a "uname -a"
  ```

* Vérifier l’espace disque :

  ```bash
  ansible all -a "df -h"
  ```

* Installer un paquet :

  ```bash
  ansible web -b -m apt -a "name=nginx state=present update_cache=yes"
  ```

---

## 6. Résumé

* Vous savez créer un inventaire YAML.
* Vous savez utiliser des variables globales, de groupe et d’hôte.
* Vous avez testé vos connexions SSH et lancé vos premières commandes Ansible.

