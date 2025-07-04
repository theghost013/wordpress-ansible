# README - Déploiement WordPress avec MariaDB et Apache via Ansible

## Description du rôle

Ce rôle Ansible permet d’installer et configurer un environnement complet WordPress avec :

- Une base de données MariaDB sécurisée.
- Un serveur web Apache configuré pour héberger WordPress.
- Le téléchargement et le déploiement des fichiers WordPress dans le répertoire web.
- La configuration de la connexion entre WordPress et la base de données.

Le rôle est conçu pour fonctionner sur des systèmes Ubuntu/Debian ainsi que sur Rocky Linux (RedHat), avec des adaptations automatiques des paquets, noms de services, utilisateurs, et chemins.

---

## Variables principales

| Variable                 | Description                                                                                        | Exemple                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `mariadb_root_password`  | Mot de passe root pour MariaDB.                                                                    | `"examplerootPW"`                          |
| `wordpress_db_name`      | Nom de la base de données dédiée à WordPress.                                                      | `"wordpress"`                              |
| `wordpress_db_user`      | Utilisateur MySQL pour WordPress.                                                                  | `"example"`                                |
| `wordpress_db_password`  | Mot de passe de l'utilisateur MySQL WordPress.                                                     | `"examplePW"`                              |
| `wordpress_download_url` | URL de téléchargement de WordPress.                                                                | `"https://wordpress.org/latest.zip"`       |
| `wordpress_web_root`     | Répertoire racine du serveur web où WordPress sera déployé.                                        | `"/var/www/html"`                          |
| `apache_service_name`    | Nom du service Apache selon la distribution (apache2 ou httpd).                                    | `"apache2"` ou `"httpd"`                   |
| `apache_server_admin`    | Adresse email de l’administrateur pour la configuration Apache.                                    | `"admin@localhost"`                        |
| `apache_document_root`   | Document root configuré dans Apache pour WordPress.                                                | `"/var/www/html/wordpress"`                |
| `web_user`               | Utilisateur propriétaire des fichiers web selon OS (`www-data` pour Debian, `apache` pour RedHat). | `"www-data"` ou `"apache"`                 |
| `mariadb_start_command`  | Commande pour démarrer MariaDB sans systemd (mode `mysqld_safe`).                                  | `"mysqld_safe --datadir=/var/lib/mysql &"` |

---

## Déroulement de l’exécution

1. **Installation des paquets nécessaires**  
   Selon la distribution, les paquets Apache, PHP, MariaDB, wget, unzip, etc., sont installés.

2. **Démarrage de MariaDB**  
   MariaDB est démarré en mode `mysqld_safe`, suivi d’une pause pour s’assurer qu’il est opérationnel.

3. **Configuration sécurisée de MariaDB**

   - Définition du mot de passe root MariaDB.
   - Suppression des utilisateurs anonymes et de la base de test par défaut.
   - Création de la base de données WordPress et de son utilisateur avec les droits appropriés.

4. **Déploiement de WordPress**

   - Téléchargement et extraction de WordPress depuis l’URL officielle.
   - Copie des fichiers dans le répertoire web racine.
   - Déploiement du fichier `wp-config.php` configuré avec les variables de connexion.
   - Suppression du fichier de configuration d’exemple.

5. **Configuration du serveur Apache**
   - Déploiement de la configuration Apache spécifique à WordPress.
   - Activation du site et des modules nécessaires (comme `rewrite` sous Debian/Ubuntu).
   - Redémarrage ou rechargement du service Apache pour appliquer les modifications.

---

## Particularités

- Le rôle adapte automatiquement les paquets, utilisateurs, chemins et services en fonction de la famille d’OS détectée (`Debian` vs `RedHat`).
- Le démarrage de MariaDB est géré sans systemd, avec une commande shell et une pause explicite.
- Les tâches sont organisées en sous-fichiers (`install_packages.yml`, `configure_mariadb.yml`, etc.) pour plus de modularité.
- Le playbook inclut des handlers pour recharger Apache uniquement lorsque nécessaire.

---

## Utilisation

1. Définir les variables nécessaires dans un fichier `vars.yml` ou directement dans l’inventaire.
2. Lancer le playbook principal avec Ansible :
   ```bash
   ansible-playbook -i inventory site.yml
   ```
