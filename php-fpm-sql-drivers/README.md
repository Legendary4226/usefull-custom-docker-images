# PHP FPM with PDO drivers of MySQL

Image d'un serveur PHP-FPM intégrant les drivers pour se connecter aux bases de données.
Initialement pour être proxié pare le biais de NGINX (fastcgi).

## Déploiement :
- Volume `/var/www` étant la racine des sites webs.
- Volume `/usr/local/etc/` contenant les fichiers de configuration. ⚠️ Regardez la section [Configurations permanentes](#configurations-permanentes) car l'image ne sera pas fonctionnelle.

## Config NGINX simple sans SSL :
```nginx
server {
        listen 80;
        listen [::]:80;

        server_name DOMAIN_NAME;
        root WEBSITE_DIRECTORY;
        resolver DOCKER_NETWORK_GATEWAY;
        index index.php;

        location = / {
                return 307 /index.php;
        }

        location ~ \.php$ {
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_pass DOCKER_CONTAINER_NAME:9000;
        }
}
```

## Configurations permanentes
Pour rendre permanent les configurations il faut un volume, lorsque vous déployez le conteneur, le dossier lié à l'emplacement `/usr/local/etc/` du conteneur sera vide ! Pour copier les fichiers de configuration **depuis l'image vers le dossier que vous voulez** suivez ces étapes :
* Déployez le conteneur avec comme volume côté conteneur `/temp` et comme chemin côté serveur la destination voulue.
* Lancez le conteneur et exécutez la commande suivante en remplacant bien le nom de votre conteneur :
```
docker exec CONTAINER_NAME cp -R /usr/local/etc/. /temp
```
* Re-déployez le conteneur en changeant le volume `/temp` en `/usr/local/etc`.
#### Vos configurations sont désormais permanentes.
