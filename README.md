# Série de Travaux Pratiques — Gestion de Configuration avec Ansible

Ce parcours de travaux pratiques vise à accompagner progressivement les étudiants dans la découverte et la maîtrise d’**Ansible** pour la gestion de configuration.

---

## 🧱 Organisation des TPs

| TP      | Thématique principale                | Objectifs                                                                                                          | Lien                                                     |
| ------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------- |
| **TP1** | Découverte d’Ansible et premiers pas | Installation, inventaires YAML, SSH, variables (`group_vars`/`host_vars`), commandes ad-hoc                        | [TP1_ansible_debut.md](./TP1_ansible_debut.md)           |
| **TP2** | Premiers Playbooks                   | Utilisation de *gather facts*, création de playbooks simples pour configurer un serveur web et une base de données | [TP2_premiers_playbooks.md](./TP2_premiers_playbooks.md) |
| **TP3** | Gestion des Secrets                  | Utilisation d’*Ansible Vault*, chiffrement de variables sensibles, gestion multi-environnements                    | [TP3_gestion_secrets.md](./TP3_gestion_secrets.md)       |
| **TP4** | Rôles, templates et Handlers | Création de rôles, Utilisation de templates JINJA2 et utilisation des Handlers | [TP4_roles_templates_handlers.md](./TP4_roles_templates_handlers.md)   | 
---

## 🧩 Pré-requis techniques

* 1 machine de contrôle (Ansible installé)
* 2 machines cibles (Linux, SSH activé)
* Un utilisateur disposant des droits `sudo`
* Accès réseau entre les machines (ping/SSH)

---

## 💡 Conseils pédagogiques

* Introduire chaque TP par une **démo rapide** sur un environnement de test local (ex : VirtualBox, Vagrant, ou Docker).
* Insister sur la **répétabilité** et la **déclarativité** des configurations.
* Encourager la **documentation du code** dans les playbooks.

---

## 🔐 Progression pédagogique

1. **TP1 — Bases de la configuration** : découverte d’Ansible, manipulation d’inventaires, premières commandes.
2. **TP2 — Automatisation pratique** : passage aux playbooks et configuration multi-services.
3. **TP3 — Sécurisation** : gestion de secrets avec Ansible Vault.

Chaque TP est conçu pour durer environ **1h à 1h30** et peut être complété par un mini-projet de synthèse :

> 🔧 *Déployer une stack Web complète (Nginx + base de données) avec secrets gérés via Vault.*

---

## 🚀 Pour aller plus loin

* [Documentation officielle Ansible](https://docs.ansible.com/)
* [Ansible Galaxy](https://galaxy.ansible.com/)
* [Collection community.general](https://docs.ansible.com/ansible/latest/collections/community/general/)

---

*Auteur : Matthieu Minet*

