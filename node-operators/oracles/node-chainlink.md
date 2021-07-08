---
title: Noeud Chainlink
description: Comment configurer un nœud Oracle Chainlink pour le réseau Moonbeam afin d'alimenter les données en chaîne à utiliser par les contrats intelligents
---

# Exécuter un nœud Oracle Chainlink sur Moonbeam

![Chainlink Moonbeam Banner](/images/chainlink/chainlinknode-banner.png)

## Introduction

En tant que réseau ouvert et sans autorisation, n'importe qui peut choisir d'exploiter un Oracle fournissant des données aux contrats intelligents exécutés sur Moonbeam.

Cet article donne un aperçu de la configuration d'un Oracle Chainlink sur Moonbase Alpha.

!!! remarque
    Les exemples fournis sont uniquement à des fins de démonstration. Les mots de passe **DOIVENT** être gérés en toute sécurité et ne jamais être stockés en clair. Ces exemples supposent un environnement basé sur Ubuntu 18.04, mais des appels pour MacOs sont inclus. Ce guide est uniquement destiné à une configuration de développement, ne l'utilisez pas pour un environnement de production.

## Modèle de requete basique

Avant de nous plonger dans la façon de commencer, il est important de comprendre les bases du "Modèle de requete basique."

--8<-- 'text/chainlink/chainlink-brm.md'

## Utilisateurs avancés

Si vous êtes habitué à exécuter des nœuds Oracle Chainlink, ces informations vous permettront de démarrer rapidement sur le Testnet Moonbase Alpha:

 - Documentation Chainlink, qui peut être trouvée [ici](https://docs.chain.link/docs/running-a-chainlink-node)
 - Point de terminaison WSS Moonbase alpha: `wss://wss.testnet.moonbeam.network`
 - ID de chaine Moonbase Alpha: `1287`
 - Jeton LINK sur Moonbase Alpha: `0xa36085F69e2889c224210F603D836748e7dC0088`
 - Obtenez des jetons Moonbase Alpha depuis [notre Faucet](/getting-started/testnet/faucet/)

## Commencer

Ce guide vous guidera à travers le processus de configuration du nœud Oracle, résumé comme suit:

 - Configurer un nœud Chainlink connecté à Moonbase Alpha
 - Noeud de fonds
 - Déployer un contrat Oracle
 - Créer un job sur le nœud Chainlink
 - Nœud de liaison et Oracle
 - Tester à l'aide d'un contrat client

Les exigences de base sont:

 - Docker pour exécuter les conteneurs de nœuds Postgres DB et ChainLink. Pour plus d'informations sur l'installation de Docker, veuillez visiter [cette page](https://docs.docker.com/get-docker/)
 - Un compte avec des fonds. Vous pouvez en créer un avec [Metamask](/integrations/wallets/metamask/), qui peut être financé par [notre Faucet](https://docs.moonbeam.network/getting-started/testnet/faucet/)
 - Accès à l'IDE Remix au cas où vous souhaiteriez l'utiliser pour déployer le contrat Oracle. Vous pouvez trouver plus d'informations sur Remix sur Moonbeam [ici](/integrations/remix/)

## Configuration du nœud

Tout d'abord, créons un nouveau répertoire pour placer tous les fichiers nécessaires. Par example:

```
mkdir -p ~/.chainlink-moonbeam //
cd ~/.chainlink-moonbeam
```

Ensuite, créons une base de données Postgres avec Docker. Pour ce faire, exécutez la commande suivante (les utilisateurs de MacOs peuvent remplacer `--network host \` with `-p 5432:5432`):

```
docker run -d --name chainlink_postgres_db \
    --volume chainlink_postgres_data:/var/lib/postgresql/data \
    -e 'POSTGRES_PASSWORD={YOU_PASSWORD_HERE}' \
    -e 'POSTGRES_USER=chainlink' \
    --network host \
    -t postgres:11
```

Assurez-vous de remplacer `{YOU_PASSWORD_HERE}` par un vrai mot de passe.

!!! remarque
    Rappel, ne stockez aucun mot de passe de production dans un fichier en clair. Les exemples fournis sont uniquement à des fins de démonstration.

Docker procédera au téléchargement des images nécessaires si elles ne sont pas disponibles. Maintenant, nous devons créer un fichier d'environnement pour Chainlink dans le répertoire nouvellement créé. Ce fichier est lu lors de la création du conteneur Chainlink. Les utilisateurs de MacOs peuvent remplacer `localhost` par `host.docker.internal`.

```
echo "ROOT=/chainlink
LOG_LEVEL=debug
ETH_CHAIN_ID=1287
MIN_OUTGOING_CONFIRMATIONS=2
LINK_CONTRACT_ADDRESS={LINK TOKEN CONTRACT ADDRESS}
CHAINLINK_TLS_PORT=0
SECURE_COOKIES=false
GAS_UPDATER_ENABLED=false
ALLOW_ORIGINS=*
ETH_URL=wss://wss.testnet.moonbeam.network
DATABASE_URL=postgresql://chainlink:{YOUR_PASSWORD_HERE}@localhost:5432/chainlink?sslmode=disable
MINIMUM_CONTRACT_PAYMENT=0" > ~/.chainlink-moonbeam/.env
```

Ici, outre le mot de passe (`{YOUR_PASSWORD_HERE}`), nous devons fournir le contrat de jeton Link (`{LINK TOKEN CONTRACT ADDRESS}`). Après avoir créé le fichier d'environnement, nous avons également besoin d'un fichier `.api` qui stocke l'utilisateur et le mot de passe utilisés pour accéder à l'API du nœud, à l'interface utilisateur de l'opérateur du nœud et à la ligne de commande Chainlink.

```
echo "{AN_EMAIL_ADDRESS}" >  ~/.chainlink-moonbeam/.api
echo "{ANOTHER_PASSWORD}"   >> ~/.chainlink-moonbeam/.api
```

Définissez à la fois une adresse e-mail et un autre mot de passe. Enfin, nous avons besoin d'un autre fichier qui stocke le mot de passe du portefeuille pour l'adresse du nœud:

```
echo "{THIRD_PASSWORD}" > ~/.chainlink-moonbeam/.password
```

Maintenant que nous avons fini de créer tous les fichiers nécessaires, nous pouvons lancer les conteneurs avec la commande suivante (les utilisateurs de MacOs peuvent remplacer `--network host \` with `-p 6688:6688`):

```
docker run -d --name chainlink_oracle_node \
    --volume $(pwd):/chainlink \
    --env-file=.env \
    --network host \
    -t smartcontract/chainlink:0.9.2 \
        local n \
        -p /chainlink/.password \
        -a /chainlink/.api
```

Pour vérifier que tout fonctionne et que les journaux progressent, utilisez:

```
docker ps #Containers Running
docker logs --tail 50 {container_id} #Logs progressing
```

![Docker logs](/images/chainlink/chainlinknode-image1.png)

## Configuration du contrat

Avec le nœud Oracle en cours d'exécution, configurons le côté contrat intelligent des choses.

Tout d'abord, nous devons récupérer l'adresse que le nœud Oracle utilisera pour envoyer des transactions et écrire des données en chaîne. Pour récupérer l'adresse, connectez-vous à l [UI du nœud ChainLink](http://localhost:6688/) (situé à `http://localhost:6688/`) en utilisant les informations d'identification du fichier `.api` .

![Chainlink login](/images/chainlink/chainlinknode-image2.png)

Allez a la 'Page de configuration` et copiez l'adresse du nœud. Utilisez le [Faucet Moonbeam](https://docs.moonbeam.network/getting-started/testnet/faucet/) pour le financer.

![Chainlink address](/images/chainlink/chainlinknode-image3.png)

Ensuite, nous devons déployer le contrat Oracle, qui est le middleware entre la chaîne et le nœud. Le contrat émet un événement avec toutes les informations nécessaires, qui sont lues par le nœud Oracle. Ensuite, le nœud répond à la demande et écrit les données demandées dans le contrat de l'appelant.

Le code source du contrat Oracle se trouve dans le repo officiel GitHub de Chainlink [ici](https://github.com/smartcontractkit/chainlink/tree/develop/evm-contracts/src/v0.6). Pour cet exemple, nous utiliserons Remix pour interagir avec Moonbase Alpha et déployer le contrat. Dans Remix, nous pouvons copier le code suivant:

```
pragma solidity ^0.6.6;

import "https://github.com/smartcontractkit/chainlink/evm-contracts/src/v0.6/Oracle.sol";
```

Après avoir compilé le contrat, accédez à l'onglet "Deploy and Run Transactions" , entrez l'adresse du jeton link et déployez le contrat. Une fois déployé, copiez l'adresse du contrat.

![Deploy Oracle using Remix](/images/chainlink/chainlinknode-image4.png)

Enfin, nous devons lier le nœud Oracle et le contrat intelligent Oracle. Un nœud peut écouter les demandes envoyées à un certain contrat Oracle, mais seuls les nœuds autorisés (alias liés) peuvent répondre à la demande avec un résultat.

Pour paramétrer cette autorisation, on peut utiliser la fonction `setFulfillmentPermission()` du contrat Oracle. Cela nécessite deux paramètres:

 - L'adresse du nœud que nous voulons lier au contrat (ce que nous avons fait dans une étape précédente)
 - Un booléen indiquant l'état de la liaison. Dans ce cas, nous l'avons mis à `true`

Nous pouvons utiliser l'instance du contrat déployé sur Remix pour le faire, et vérifier que le nœud Oracle est autorisé avec la fonction de vue `getAuthorizationStatus()`, en passant l'adresse du nœud Oracle.

![Authorize Chainlink Oracle Node](/images/chainlink/chainlinknode-image5.png)

## Créer un job sur le nœud Oracle

La dernière étape pour avoir un Chainlink Oracle entièrement configuré consiste à créer un job. Se référant à [Documentation officielle de Chainlink](https://docs.chain.link/docs/job-specifications):

> Les spécifications d'un Job, ou specs, contiennent les tâches séquentielles que le nœud doit effectuer pour produire un résultat final. Une spécification contient au moins un initiateur et une tâche, qui sont décrits en détail ci-dessous. Les spécifications sont définies à l'aide de JSON standard afin qu'elles soient lisibles par l'homme et puissent être facilement analysées par le nœud Chainlink.

Considérant un Oracle comme un service API, un Job ici serait l'une des fonctions que nous pouvons appeler et qui renverra un résultat. Pour créer notre premier Job, allez dans les [sections Jobs de votre nœud](http://localhost:6688/jobs) et cliquez sur "New Job."

![Chainlink Oracle New Job](/images/chainlink/chainlinknode-image6.png)

Ensuite, collez le JSON suivant. Cela créera un job qui demandera le prix ETH actuel en USD. Assurez-vous d'entrer votre adresse de contrat Oracle (`YOUR_ORACLE_CONTRACT_ADDRESS`).

```
{
  "initiators": [
    {
      "type": "runlog",
      "params": { "address": "YOUR_ORACLE_CONTRACT_ADDRESS" }
    }
  ],
  "tasks": [
    {
      "type": "httpget",
      "params": { "get": "https://min-api.cryptocompare.com/data/price?fsym=ETH&tsyms=USD" }
    },
    {
      "type": "jsonparse",
      "params": { "path": [ "USD" ] }
    },
    {
      "type": "multiply",
      "params": { "times": 100 }
    },
    { "type": "ethuint256" },
    { "type": "ethtx" }
  ]
}
```

![Chainlink New Job JSON Blob](/images/chainlink/chainlinknode-image7.png)

Et c'est tout ! Vous avez entièrement configuré un nœud Oracle Chainlink qui s'exécute sur Moonbase Alpha.

## Tester l'Oracle

Pour vérifier qu'Oracle est opérationnel et qu'il répond aux demandes, suivez notre tutoriel [utiliser un Oracle](/integrations/oracles/chainlink/) . L'idée principale est de déployer un contrat client qui demande à l'Oracle, et l'Oracle écrit les données demandées dans le stockage du contrat.

