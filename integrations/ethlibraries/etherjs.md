---
title: Ethers.js
description: Suivez ce tutoriel pour apprendre à utiliser la bibliothèque Ethereum Ether.JS pour déployer des contrats intelligents Solidity sur Moonbeam.
---
# Bibliothèque Javascript Ether.js.

![Intro diagram](/images/builders/tools/eth-libraries/ethersjs-banner.png)

## Introduction {: #introduction } 

La bibliothèque [ethers.js](https://docs.ethers.io/) fournit un ensemble d'outils pour interagir avec les nœuds Ethereum avec JavaScript, similaire à web3.js. Moonbeam dispose d'une API de type Ethereum entièrement compatible avec les invocations JSON RPC de style Ethereum. Par conséquent, les développeurs peuvent tirer parti de cette compatibilité et utiliser la bibliothèque ethers.js pour interagir avec un nœud Moonbeam comme s'ils le faisaient sur Ethereum. Vous pouvez en savoir plus sur ethers.js en lisant cet [article](https://medium.com/l4-media/announcing-ethers-js-a-web3-alternative-6f134fdd06f3).

## Configurer Ether.js avec Moonbeam {: #setup-ethersjs-with-moonbeam } 

Pour démarrer avec la bibliothèque Ethers.js, installez-la à l'aide de la commande suivante:

```
npm install ethers
```

Une fois terminé, la configuration la plus simple pour commencer à utiliser la bibliothèque et ses méthodes est la suivante:

```js
const ethers = require('ethers');

// Variables definition
const privKey = '0xPRIVKEY';

// Define Provider
const provider = new ethers.providers.StaticJsonRpcProvider('RPC_URL', {
    chainId: ChainId,
    name: 'NETWORK_NAME'
});

// Create Wallet
let wallet = new ethers.Wallet(privKey, provider);
```

Différentes méthodes sont disponibles à l'intérieur de `provider` et `wallet`. En fonction du réseau auquel vous souhaitez vous connecter, vous pouvez définir les valeurs `RPC_URL` suivantes:

Nœud de développement Moonbeam: 
 - RPC_URL: `http://127.0.0.1:9933`"
 - Id de chaine: `1281`
 - NOM_RESEAU: `moonbeam-development`
 
TestNet Moonbase Alpha: 
 - RPC_URL: `https://rpc.testnet.moonbeam.network`
 - Id de chaine: `1287`
 - NOM_RESEAU: `moonbase-alpha`

## Tutoriels étape par étape {: #step-by-step-tutorials } 

Si vous êtes intéressé par un guide étape par étape plus détaillé, vous pouvez consulter nos tutoriels spécifiques sur l'utilisation de ethers.js sur Moonbeam pour [envoyer une transaction](/getting-started/local-node/send-transaction/) ou [deployer un contrat](/getting-started/local-node/deploy-contract/).
