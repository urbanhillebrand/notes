# postgresql

## Alfresco DB-Migration

```
sudo service alfresco start tomcat
pg_dump -f /pfad/zu/dump alfresco
sudo service alfresco stop
```

Auf neuem System:
```
sudo service alfresco start postgresql
dropdb -U postgres alfresco
createdb -U postgres -O alfresco alfresco
psql -f /pfad/zu/dump alfresco
```

## Neue DB mit User erstellen

```
createuser -P activiti
createdb -O activiti activiti

# oder ausführlicher mit mehr Optionen...
createdb --template=template0 --locale=en_US.UTF-8 --encoding=UTF8 --owner=nextcloud nextcloud
```

oder

```
psql template1
CREATE USER activiti WITH PASSWORD 'blahblah';
CREATE DATABASE activiti WITH OWNER activiti;
#GRANT ALL PRIVILEGES ON DATABASE activiti to activiti;
```

```
psql -c "ALTER USER postgres WITH PASSWORD 'neuespasswort'" -d postgres 
psql -U postgres -c "CREATE ROLE adempiere SUPERUSER LOGIN PASSWORD 'adempiere'" 
```

## Snippets

* **Tabellen-Infos anzeigen (wie mysql DESCRIBE)**\
`\d+ table_name`
* **Anzahl angezeigter rows limitieren**\
`select ... from ... LIMIT 10;`
* **Select mit substring-Match**\
`select ... from ... where ... LIKE '%substring';`

## Upgrade 9.1 -> 9.3:

apt-get install postgresql 
service postgresql stop
pg_dropcluster 9.3 main # durch installation erstellt
pg_upgradecluster 9.1 main
apt-get purge postgres...9.1
