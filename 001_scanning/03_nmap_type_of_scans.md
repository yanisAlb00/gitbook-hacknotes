# TCP SYN SCAN "Half-open" Scan

Pour essayer d'ouvrir une connexion vers un serveur destination on envoie un TCP SYN (Flag SYN à 1) sur le port recherché :

- Si la destinnation répond avec un SYN/ACK (flag SYN à 1 et flag ACK à 1 ) : le port est ouvert
- Si la destination répond avec un RST/ACK (flag RST à 1 et flag ACK à 1) : le port est fermé
- Si la destination ne répond pas : le port est sûrement filtré par un pare-feu

nmap -sS

# TCP CONNECT SCAN "Full-open" Scan

Pour initier une connexion, on envoie un TCP ACK après la réponse SYN/ACK.

nmap -sT

# TCP FIN SCAN "Stealth Scan"

Envoyer un paquet TCP FIN (flag FIN à 1) pour terminer la connexion :

- Si la destination ne répond pas, le port est ouvert ou filtré
- Si la destination répond avec un RST/ACK, le port est fermé

nmap -sF

--> Incapable de détecter la présence d'un pare-feu

# TCP XMAS SCAN

Envoyer un paquet TCP avec les Flags FIN, PSH et URG à 1 :
- Si la destination ne répond pas, le port est ouvert ou filtré
- Si la destination répond avec un RST/ACK, le port est fermé

nmap -sX

--> Incapable de détecter la présence d'un pare-feu

# TCP NULL SCAN 

Envoyer un paquet TCP avec tous les flags TCP à 0 :
- Si la destination ne répond pas, le port est ouvert ou filtré
- Si la destination répond avec un RST/ACK, le port est fermé

nmap -sN

--> Incapable de détecter la présence d'un pare-feu

# TCP ACK SCAN

Envoyer un paquet TCP avec le flag ACK à 1 :
- Si la destination ne répond pas, le port est filtré
- Si la destination répond avec un flag RST à 1, le port est non-filtré

nmap -sA

--> Utilisé pour détecter la présence d'un pare-feu