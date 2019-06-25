## step 1 : Update Ubuntu
```
# apt-get update && apt-get dist-upgrade && apt-get autoremove
```

## step 2 : Install Nginx

#### 更新Ubuntu後，運行以下命令安裝Nginx HTTP Server ..
```
# apt-get install nginx
```

#### 安裝Nginx後，以下命令可用於停止，啟動和啟用Nginx服務，以便始終啟動服務器啟動。
```
# systemctl stop nginx
# systemctl start nginx
# systemctl enable nginx
```

## step 3 : Mariadb database server

#### MariaDB數據庫服務器在開源和Linux社區中迅速超越MySQL ...... MariaDB是大多數Linux發行版上的默認數據庫服務器......而WordPress需要數據庫服務器..運行以下命令安裝MariaDB。
```
# apt-get install mariadb-server mariadb-client
```

#### 安裝後，以下命令可用於停止，啟動和啟用MariaDB服務，以便在服務器啟動時始終啟動。
```
# systemctl stop mariadb.service
# systemctl start mariadb.service
# systemctl enable mariadb.service
```

#### 之後，運行以下命令以保護MariaDB服務器並創建新的root密碼。
````
# mysql_secure_installation
````

#### 出現提示時，請按照指南迴答以下問題。

- Enter current password for root (enter for none): Just press Enter
- Set root password? [Y/n]: Y
- New password: Enter password
- Re-enter new password: Repeat password
- Remove anonymous users? [Y/n]: Y
- Disallow root login remotely? [Y/n]: Y
- Remove test database and access to it? [Y/n]:  Y
- Reload privilege tables now? [Y/n]:  Y

## step 4 : Install php-fpm and related modules

#### 現在安裝了Nginx和MariaDB，運行以下命令在新服務器上安裝PHP-FPM和相關的PHP模塊。這是一個很好的PHP模塊列表。
```
# apt-get install php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
```

#### 安裝PHP後，運行以下命令打開PHP-FPM默認配置文件。
````
#  vim /etc/php/7.2/fpm/php.ini
````

#### 然後向下滾動文件中的行並更改以下行並保存。
````
post_max_size = 100M
memory_limit = 256M
max_execution_time = 360
upload_max_filesize = 100M
date.timezone = Asia/Taipei
````

## step 5 : 創建一個 WORDPRESS 數據庫

#### 此時，安裝了所有必需的WordPress軟件包和服務器。現在，新服務器已準備好託管WordPress ...在新服務器上，創建一個空白的WordPress數據庫。WordPress將使用此空數據庫來存儲其內容。
#### 運行以下命令以登錄數據庫服務器。提示輸入密碼時，請鍵入您在上面創建的root密碼。
```
# mysql -u root -p
```

#### 然後創建一個名為WP_database  的空白數據庫，  您可以使用舊服務器中的相同數據庫名稱。
```
# CREATE DATABASE WP_database;
```

####  使用新密碼創建名為wp_user的數據庫用戶  。您可以使用舊服務器中的相同用戶名和密碼。
```
# CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'type_password_here';
```

####  然後授予用戶對數據庫的完全訪問權限。
```
# GRANT ALL ON WP_database.* TO 'wp_user'@'localhost' IDENTIFIED BY 'type_user_password_here' WITH GRANT OPTION;
```

####  最後，保存更改並退出。
```
# FLUSH PRIVILEGES;
# exit
```

## step 6 : 下載 wordpress

#### 接下來，訪問WordPress網站並下載最新的.... 或運行以下命令為您執行此操作。
```
# cd / tmp && wget https://wordpress.org/latest.tar.gz
# tar -zxvf latest.tar.gz
# sudo mv wordpress / var / www / html / wordpress
```

#### 然後運行以下命令為WordPress根目錄設置正確的權限。
```
# sudo chown -R www-data：www-data / var / www / html / wordpress /
# sudo chmod -R 755 / var / www / html / wordpress /
```

## step 7 : 配置 wordpress

#### 接下來，運行以下命令以創建WordPress  wp-config.php  文件。這是WordPress的默認配置文件。
```
# sudo mv /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php
```

#### 然後運行以下命令打開WordPress配置文件。
```
# vim /var/www/html/wordpress/wp-config.php
```

#### 輸入您為數據庫創建的下面突出顯示的文本並保存。
```
// ** MySQL設置 - 您可以從您的網站主機獲取此信息** //
/ ** WordPress的數據庫名稱* /
define（'DB_NAME'，' WP_database '）;

/ ** MySQL數據庫用戶名* /
define（'DB_USER'，' wp_user '）;

/ ** MySQL數據庫密碼* /
define（'DB_PASSWORD'，' new_password_here '）;

/ ** MySQL主機名* /
define（'DB_HOST'，'localhost'）;

/ **用於創建數據庫表的數據庫字符集。* /
define（'DB_CHARSET'，'utf8'）;

/ **數據庫整理類型。如果有疑問，請不要更改。* /
define（'DB_COLLATE'，''）;
```

## step 8 : 配置新的 wordpress 網站

#### 接下來，在服務器上配置WordPress站點配置文件。運行以下命令以創建名為wordpress的新配置文件 
```
# sudo nano /etc/nginx/sites-available/wordpress
```

#### 然後將下面的內容複製並粘貼到文件中並保存。將example.com替換  為您自己的域名。
```
server {
    listen 80;
    listen [::]:80;
    root /var/www/html/wordpress;
    index  index.php index.html index.htm;
    server_name  example.com www.example.com;

     client_max_body_size 100M;

    location / {
        try_files $uri $uri/ /index.php?$args;        
    }

    location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass             unix:/var/run/php/php7.2-fpm.sock;
    fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

## step 9 : 啟用 wordpress 網站

#### 配置上面的VirtualHost後，通過運行以下命令啟用它
```
# sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
```

#### 現在全部都配置好了...運行下面的命令重新加載Nginx Web服務器和PHP-FPM設置。
```
# sudo systemctl restart nginx.service
# sudo systemctl restart php7.2-fpm.service
```

#### 重新啟動Nginx後，打開瀏覽器並瀏覽到服務器IP地址或主機名。如果一切設置正確，您應該看到WordPress默認配置嚮導。

#### http://example.com







## 參考資料
- Install WordPress On Ubuntu 18.04 LTS With Nginx, MariaDB And PHP-FPM : https://websiteforstudents.com/install-wordpress-on-ubuntu-18-04-lts-bata-with-nginx-mariadb-and-php-fpm/
- How To Install WordPress with LEMP on Ubuntu 18.04 : https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lemp-on-ubuntu-18-04

