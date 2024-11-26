# Zabbix Monitoring Sistēmas Instalācijas Instrukcija uz Raspberry Pi 4
### ---
###### https://www.zabbix.com/download?zabbix=7.0&os_distribution=raspberry_pi_os&os_version=12&components=server_frontend_agent&db=mysql&ws=apache
### ---
### 1. Lejupielādē un instalē Zabbix repozitorija pakotni
Izmanto šādas komandas, lai lejupielādētu un instalētu Zabbix repozitorija pakotni:
```bash
wget https://repo.zabbix.com/zabbix/7.0/raspbian/pool/main/z/zabbix-release/zabbix-release_latest+debian12_all.deb
sudo dpkg -i zabbix-release_latest+debian12_all.deb
sudo apt update
```
### 2. Uzstādi Zabbix komponentes
Instalē nepieciešamās Zabbix komponentes ar šādu komandu:
```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```
### 3. Instalē MariaDB datubāzi
MariaDB datubāze ir nepieciešama Zabbix darbībai. Instalējiet to ar šādu komandu:
```bash
sudo apt install -y mariadb-server
mysql_secure_installation
```
### 4. Izveido datubāzi Zabbix serverim
Atveriet MariaDB konsoli:
```bash
sudo mysql -uroot -p
```
Konsolē ievadiet šādas komandas:
```bash
create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'zabbix123';
grant all privileges on zabbix.* to zabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit;
```
### 5. Importē Zabbix datubāzes struktūru:
```bash
sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```
### 6. Konfigurē Zabbix servera failu
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Failā atrodiet un rediģējiet šo rindu, norādot datubāzes paroli:
```bash
DBPassword=zabbix123
```
### 7. Konfigurē MariaDB
```bash
sudo nano /etc/mysql/my.cnf
```
Failā atrodiet un rediģējiet šo rindu:
```bash
[mysqld]
bind-address = 0.0.0.0
#skip-networking
```
Saglabājiet izmaiņas un restartējiet MariaDB:
```bash
sudo systemctl restart mariadb
```
### 9. Restartē Zabbix un Apache
```bash
sudo systemctl restart zabbix-server zabbix-agent
sudo systemctl enable zabbix-server zabbix-agent
sudo systemctl enable apache2
sudo systemctl restart apache2
```
Pārliecinieties, ka visi pakalpojumi darbojas:
```bash
sudo systemctl status zabbix-server zabbix-agent apache2
```
### 10. Testēšana
1. Atveriet pārlūkprogrammu un ievadiet Raspberry Pi IP adresi, sekojoši: 
    ### http://<ip_adrese>/zabbix


2. Konfigurējiet zabbix (Laiks + parole no datubāzes)

3. Pieslēdzieties ar noklusējuma pieteikšanās datiem:
![Login](https://www.zabbix.com/documentation/current/assets/en/manual/quickstart/login.png)
    ```bash
    Username: Admin
    Password: zabbix
    ```

---
![Page](https://i.ytimg.com/vi/W_HIIvuZFUg/maxresdefault.jpg)