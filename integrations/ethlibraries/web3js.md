---
title: Web3.js
description: Suivez ce tutoriel pour apprendre à utiliser la bibliothèque JavaScript Ethereum Web3 pour déployer des contrats intelligents Solidity sur Moonbeam.
---
# Bibliothèque JavaScript Web3.js

![Intro diagram](/images/integrations/integrations-web3js-banner.png)

## Introduction {: #introduction } 

[Web3.js](https://web3js.readthedocs.io/) est un ensemble de bibliothèques qui permettent aux développeurs d'interagir avec les nœuds Ethereum à l'aide des protocoles HTTP, IPC ou WebSocket avec JavaScript. Moonbeam dispose d'une API de type Ethereum entièrement compatible avec les invocations JSON RPC de style Ethereum. Par conséquent, les développeurs peuvent tirer parti de cette compatibilité et utiliser la bibliothèque web3.js pour interagir avec un nœud Moonbeam comme s'ils le faisaient sur Ethereum.

## Configurer Web3.js avec Moonbeam {: #setup-web3js-with-moonbeam } 

Pour commencer avec la bibliothèque web3.js, nous devons d'abord l'installer à l'aide de la commande suivante:

```
npm install web3
```

Une fois terminé, la configuration la plus simple pour commencer à utiliser la bibliothèque et ses méthodes est la suivante:

```js
const Web3 = require('web3');

//Create web3 instance
const web3 = new Web3('RPC_URL');
```

En fonction du réseau auquel vous souhaitez vous connecter, vous pouvez définir les valeurs  `RPC_URL` suivantes:

 - Nœud de développement Moonbeam: `http://127.0.0.1:9933`
 - TestNet Moonbase Alpha: `https://rpc.testnet.moonbeam.network`

## Tutoriels étape par étape {: #stepbystep-tutorials } 

Si vous êtes intéressé par un guide étape par étape plus détaillé, consultez nos tutoriels spécifiques sur l'utilisation de web3.js sur Moonbeam pour [envoyer une transaction](/getting-started/local-node/send-transaction/) ou [deployer un contrat](/getting-started/local-node/deploy-contract/).

