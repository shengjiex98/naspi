# Configuring NGINX

Sample config files

```conf
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;

    server_name _;

    include /config/nginx/ssl.conf;

    set $root /config/www;
    root $root;
    index index.html index.htm index.php;

    location /zotero {
        # enable for basic auth
        auth_basic "Restricted";
        auth_basic_user_file /config/nginx/.htpasswd;

        dav_methods PUT DELETE MKCOL COPY MOVE;
        dav_ext_methods PROPFIND OPTIONS;
        dav_access user:rw group:rw all:rw;

        client_max_body_size 0;
        create_full_put_path on;
        client_body_temp_path /tmp/;
    }

    # Redirect to individual services
    location /plex {
        return 301 $scheme://pi:32400/;
    }

    location ~ ^(.+\.php)(.*)$ {
        try_files $fastcgi_script_name =404;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        include /etc/nginx/fastcgi_params;
    }

    # deny access to .htaccess/.htpasswd files
    location ~ /\.ht {
        deny all;
    }
}
```
