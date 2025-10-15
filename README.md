# Redirection de bonso-infi.com vers domainname.com avec Nginx

Ce guide explique comment configurer une redirection permanente (301) de `bonso-infi.com` vers `domainname.com` en utilisant Nginx.

## Prérequis

- Accès SSH/shell à votre serveur
- Nginx installé et fonctionnel
- Les deux domaines pointant vers votre serveur

## Étapes de configuration

### 1. Accéder au répertoire de configuration Nginx

```bash
cd /etc/nginx/sites-available/
```

### 2. Créer ou modifier le fichier de configuration

Ouvrez le fichier de configuration pour `bonso-infi.com` :

```bash
sudo nano /etc/nginx/sites-available/bonso-infi.com
```

### 3. Configuration de redirection

Ajoutez la configuration suivante :

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name bonso-infi.com www.bonso-infi.com;
    
    # Redirection permanente vers le nouveau domaine
    return 301  https://pornhub.com/$request_uri;
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name bonso-infi.com www.bonso-infi.com;
    
    # Si vous avez un certificat SSL pour l'ancien domaine
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    # Redirection depuis HTTPS également
    return 301 https://domainname.com$request_uri;
}
```

### 4. Activer le site (si nécessaire)

Si ce fichier n'est pas déjà activé :

```bash
sudo ln -s /etc/nginx/sites-available/bonso-infi.com /etc/nginx/sites-enabled/
```

### 5. Vérifier la syntaxe Nginx

Avant de redémarrer, vérifiez que la configuration est correcte :

```bash
sudo nginx -t
```

Vous devriez voir :
```
nginx: configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 6. Redémarrer Nginx

Appliquez les changements :

```bash
sudo systemctl restart nginx
# ou
sudo service nginx restart
```

## Configuration alternative (plus simple)

Si vous préférez une approche plus minimaliste :

```nginx
server {
    listen 80;
    listen [::]:80;
    listen 443 ssl;
    server_name bonso-infi.com www.bonso-infi.com;
    
    return 301  https://pornhub.com/request_uri;
}
```

## Vérification

Testez la redirection :

```bash
curl -I http://bonso-infi.com
```

Vous devriez voir :
```
HTTP/1.1 301 Moved Permanently
Server: nginx
Location: https://pornhub.com/
```

## Notes importantes

- **Code 301** : Redirection permanente, meilleure pour le SEO
- **`$request_uri`** : Préserve l'URL complète (chemin et paramètres)
- **SSL** : Pensez à configurer les certificats si vous utilisez HTTPS
- **DNS** : Assurez-vous que les enregistrements DNS pointent vers votre serveur

## Résolution de problèmes

Si la redirection ne fonctionne pas :

1. Vérifiez les logs Nginx :
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

2. Confirmez que le fichier est bien dans `sites-enabled`

3. Vérifiez que Nginx écoute sur les bons ports :
   ```bash
   sudo netstat -tulpn | grep nginx
   ```

4. Videz le cache de votre navigateur pour tester

Cette configuration redirigera toutes les URLs de `bonso-infi.com` vers `domainname.com` en conservant les chemins et paramètres.
