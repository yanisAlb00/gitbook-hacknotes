# API Testing

## API Introdcution

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Une API permet de récupérer des données formatées de manière uniformisée.

Format utilisé pour structurer les données :&#x20;

* JSON (utilisé par les API REST en général)
* XML (utilisé par les API SOAP en général)
* YAML

Le JSON se base sur un fonctionnement de type clé/valeur avec des données typées (`string`, `integer`, `booleen`, `array`)

```json
{
    "user": {
        "id": 1,
        "username": "toto",
        "attributes": {
            "personnal_information": [
                { "phone": "123", "lastChange": "21/05/2024" },
                { "address": "12th street", "lastChange": "20/05/2024" },
                { "age": 23, "lastChange": "21/05/2024" }
            ]
        },
        "enable": true
    }
}
```

## API Rest

Une API Rest est caractérisée par :

* Basée sur des conventions de communications HTTP : `GET`, `POST`, `PUT`, `DELETE`
* Plusieurs Endpoints qui correspondent à différentes routes
  * Exemple : `/api/profile` , `/api/login` , `/api/docs`
* Under-Fetching / Over-Fetching : Correspond au fait de récupérer + ou - de données que souhaité en requêtant un endpoint spécifique

`POST` : Requête d'une page en passant des valeurs en paramètres

`PUT` : Insertion de données en base

Exemple de cas d'usage :

1. Demande d'authentification en `POST` avec des paramètres user/password
2. Réponse de l'API avec un cookie de session et UserID
3. Requête `PUT` pour insérer des données en base en passant le cookie de session et le UserID

## Phase de reconnaissance

* Utiliser une [SecList ](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/api/api-endpoints.txt)pour énumérer les endpoints API d'un domaine&#x20;
* Trouver la documentation associée à une API : Swagger, GitHub, endpoints spécifiques (`/api/docs`)
* Identifier les méthodes acceptées
  * Fuzzing
  * Utilisation de la méthode `OPTIONS` qui permet de lister les méthodes acceptées
* Identifier les formats de données acceptés (`Content-Type`) : JSON, XML, YAML

## Phase d'exploitation

Exploiter une vulnérabilité :

* Injections : SQL, NoSQL, Command Injections, SSRF
* Information disclosure (IDOR)
* Path Traversal
* Broken Access Control : BAC
  * Modifier le mot de passe d'un autre utilisateur
  * Accéder à une donnée qui est censée être authentifiée
* Mass assignement : On s'aperçoit que certains paramètres sont présents dans les réponses sans qu'ils aient été spécifiés dans la requête. On peut essayer de forcer leurs valeurs en les précisant dans les requêtes.
* Race Condition : envoyer une requête plus rapide qu'une requête en backend utilisée par l'application
* Parameter Pollution&#x20;
* Cryptographic failure : stockage d'une clé AES en clair, secret faible dans un JWT

Les développeurs utilisent des UID pour référencer des objets. Le 1er Digit du 3ème paquet dans un UID permet d'identifier la version.

Si le paquet commence par 1 alors version d'UID = v1

Si le paquet commence par 4 alors version d'UID = v4

v4 est plus sécurisée que v1. L'utilisation de la v1 permet d'anticiper les valeurs d'UID en fonction de certaines valeurs ("Sandwich Attack")
