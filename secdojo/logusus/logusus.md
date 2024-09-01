# SecDojo: Logusus

![](images/logusus.png)

## Scan Initial

J'ai commencé par effectuer un scan Nmap pour identifier les ports ouverts sur la machine cible.

![](images/nmap.png)

Le scan Nmap a révélé que les ports 22 (SSH) et 80 (HTTP) sont ouverts sur la machine cible.

## Accès au site web

Je me rends sur mon navigateur et découvre une page de login avec un champ pour l'email et le mot de passe.

![](images/login.png)

### Interception de la requête avec Burp Suite

Je capture la requête de login avec Burp Suite.

![](images/rq_1.png)

La requête POST est envoyée à l'api `/api/v1/user/login/`

### Exploration de l'API

En inspectant l'API, j'ai découvert l'api `/api/v1/user/public`, qui expose des informations sur les utilisateurs.

![](images/public.png)

Cela m'a permis d'avoir les adresses email valides. Après une recherche, j'ai trouvé l'API qui permet de changer les mots de passe. Je choisis un utilisateur et modifie son mot de passe via l'api `/api/v1/user/password/`.

![](images/robot.png)

Une fois le mot de passe modifié, je me connecte à l'application en utilisant les nouvelles informations d'identification.

![](images/login_2.png)

Une fois connecté, un token d'accès m'est attribué.

![](images/token.png)

Sur le dashboard, je découvre toutes les API disponibles pour le système.

![](images/dsh.png)

### Recherche d'autres utilisateurs

J'essaie d'accéder à l'api `/api/v1/user/all`, mais je n'ai pas les privilèges nécessaires.

![](images/not_admin.png)

En utilisant l'api `/api/v1/user/`, je parviens à lister un autre utilisateur avec un `"access_level": 49`

![](images/level.png)

J'essaie de modifier son mot de passe mais ce compte n'a pas le droit de changer son mot de passe.

![](images/not_auth.png)

## Escalade des privilèges

Après une recherche approfondie, je découvre l'api `/api/v1/user/update/`.

![](images/view_update.png)

Qui me permet de changer le mot de passe de cet utilisateur.

![](images/update.png)

Je me connecte avec ce compte pour continuer l'exploitation avec son token.

![](images/loged.png)

Après avoir exploré plusieurs API, je découvre `/api/v1/user/change-avatar/`. En tentant de changer l'avatar via cette API, une erreur se produit.

![](images/error.png)

Je constate qu'une vulnérabilité LFI (Local File Inclusion) existe au niveau du `filename` par essais et erreurs.

![](images/passwd.png)   ![](images/etc.png)

En exploitant cette LFI, je parviens à accéder au flag.

![](images/root.png)
