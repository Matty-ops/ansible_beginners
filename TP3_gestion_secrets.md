# TP3 — Gestion des Secrets avec Ansible Vault

## Objectifs

* Comprendre le rôle d’Ansible Vault.
* Créer et utiliser des fichiers de variables chiffrés.
* Gérer plusieurs coffres (`vault_id`).

---

## 1. Création d’un fichier Vault

Créer un fichier chiffré pour la base de données :

```bash
ansible-vault create group_vars/db/vault.yml
```

Contenu :

```yaml
db_admin_user: admin
db_admin_password: "SuperSecret123!"
```

---

## 2. Utilisation dans un playbook

### `playbooks/db_secure.yml`

```yaml
- name: Configuration sécurisée de la base de données
  hosts: db
  become: yes
  tasks:
    - name: Créer un utilisateur admin
      ansible.builtin.command: >
        mysql -e "CREATE USER '{{ db_admin_user }}'@'%' IDENTIFIED BY '{{ db_admin_password }}';"
```

Exécution :

```bash
ansible-playbook -i inventaires/lab.yml playbooks/db_secure.yml --ask-vault-pass
```

---

## 3. Gestion des mots de passe Vault

### Option 1 — Fichier local (non versionné)

Créer `.secrets/vault_pass.txt` :

```
motdepassevault
```

Lancer :

```bash
ansible-playbook playbooks/db_secure.yml --vault-password-file .secrets/vault_pass.txt
```

### Option 2 — Plusieurs Vaults (environnements)

```bash
ansible-vault create --vault-id dev@prompt group_vars/all/vault_dev.yml
ansible-vault create --vault-id prod@prompt group_vars/all/vault_prod.yml
```

Exécution :

```bash
ansible-playbook playbooks/site.yml --vault-id dev@prompt
```

---

## 4. Commandes utiles

```bash
ansible-vault edit group_vars/db/vault.yml
ansible-vault encrypt group_vars/db/vault.yml
ansible-vault decrypt group_vars/db/vault.yml
ansible-vault rekey group_vars/db/vault.yml
```

---

## 5. Exercice final

* Créez un Vault `vault.yml` contenant :

  * un mot de passe DB,
  * un token API,
  * et un mot de passe d’utilisateur système.
* Affichez ces variables avec :

  ```bash
  ansible db -m debug -a "var=db_admin_password" --ask-vault-pass
  ```
* Intégrez ces variables à votre playbook de déploiement.

---

## 6. Bonnes pratiques

* Ne chiffrer que les secrets, pas tout le fichier.
* Versionner les fichiers Vault, **pas** les mots de passe (`.gitignore` pour `.secrets/`).
* Donner des noms explicites (`vault_dev.yml`, `vault_prod.yml`).
* Tester la lecture des variables chiffrées avec `ansible -m debug` avant l’exécution d’un playbook.
