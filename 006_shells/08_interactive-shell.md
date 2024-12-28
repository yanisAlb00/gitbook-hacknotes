Spawning Interactive Shells :

Présents nativement sur le système :
/bin/sh
/bin/bash

Mode interactif : 
/bin/sh -i
Executer Shell à partir de perl :
perl —e 'exec "/bin/sh";'
perl: exec "/bin/sh";

à partir de Ruby :
ruby: exec "/bin/sh"

à partir de LUA :
lua: os.execute('/bin/sh')

à partir de AWK :
awk 'BEGIN {system("/bin/sh")}'

à partir de la commande find :
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
find . -exec /bin/sh \; -quit

à partir de vim :
vim -c ':!/bin/sh'
vim
:set shell=/bin/sh
:shell