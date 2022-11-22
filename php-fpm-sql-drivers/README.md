Image d'un serveur PHP-FPM intégrant les drivers pour se connecter aux bases de données.
Initialement pour être proxié pare le biais de NGINX (fastcgi).

Déploiement :
- Volume /var/www étant la racine des sites webs.

Tips :
- Config NGINX simple sans SSL :
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
