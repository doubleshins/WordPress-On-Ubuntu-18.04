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






## 參考資料
- Install WordPress On Ubuntu 18.04 LTS With Nginx, MariaDB And PHP-FPM : https://websiteforstudents.com/install-wordpress-on-ubuntu-18-04-lts-bata-with-nginx-mariadb-and-php-fpm/

