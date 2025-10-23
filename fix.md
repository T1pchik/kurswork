```tcl
cat << EOF >> /root/zabbix.yml
services:
  zabbix-database:
    image: postgres:16
    container_name: zabbix-database
    restart: unless-stopped
    environment:
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix

  zabbix-server:
    image: zabbix/zabbix-server-pgsql
    container_name: zabbix-server
    restart: unless-stopped
    ports:
      - "10051:10051"
    environment:
      - DB_SERVER_HOST=zabbix-database
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix
    depends_on:
      - zabbix-database

  zabbix-agent:
    image: zabbix/zabbix-agent2:alpine-6.4-latest
    container_name: zabbix-agent
    restart: unless-stopped
    ports:
      - "10050:10050"
    environment:
      - ZBX_HOSTNAME=Zabbix server
      - ZBX_SERVER_HOST=zabbix-server
    depends_on:
      - zabbix-server

  zabbix-web:
    image: zabbix/zabbix-web-nginx-pgsql
    container_name: zabbix-web
    restart: unless-stopped
    ports:
      - "80:8080"
      - "443:8443"
    environment:
      - DB_SERVER_HOST=zabbix-database
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix
      - ZBX_SERVER_HOST=zabbix-server
      - PHP_TZ=Europe/Yekaterinburg
    depends_on:
      - zabbix-server
      - zabbix-database
EOF
```
