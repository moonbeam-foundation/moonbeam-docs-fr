---
title: IDE Remix 
description: Apprenez à utiliser l'un des outils de développement Ethereum les plus populaires, l'IDE Remix, pour interagir avec Moonbeam.
---

# Interagir avec Moonbeam à l'aide de Remix

![Intro diagram](/images/builders/tools/remix/remix-banner.png)

## Introduction {: #introduction } 

Un autre outil que les développeurs peuvent utiliser pour interagir avec Moonbeam est l' [IDE Remix](https://remix.ethereum.org/), l'un des environnements de développement les plus couramment utilisés pour les contrats intelligents sur Ethereum. Il fournit une solution Web pour compiler et déployer rapidement du code basé sur Solidity et Vyper sur une VM locale ou, plus intéressant encore, sur un fournisseur Web3 externe, tel que MetaMask. En combinant les deux outils, on peut démarrer très rapidement avec Moonbeam.
## Déployer un contrat sur Moonbeam {: #deploying-a-contract-to-moonbeam } 

Pour démontrer comment vous pouvez tirer parti de [Remix](https://remix.ethereum.org/) pour déployer des contrats intelligents sur Moonbeam, nous utiliserons le contrat de base suivant:

```solidity
pragma solidity ^0.7.5;

contract SimpleContract{
    string public text;
    
    constructor(string memory _input) {
        text = _input;
    }
}
```

Une fois compilé, nous pouvons accéder à l'onglet "Déployer et exécuter des transactions" . Nous devons d'abord définir notre environnement sur "Injected Web3." Cela utilise le fournisseur injecté par MetaMask, qui nous permet de déployer des contrats sur le réseau auquel il est connecté - dans ce cas, le TestNet Moonbase Alpha . 

Pour cet exemple, nous déploierons le contrat à partir d'un compte MetaMask financé. Vous pouvez utiliser notre [faucet du TestNet](/getting-started/testnet/faucet/) pour financer votre compte pour les déploiements sur Moonbase Alpha. Ensuite, passez en entrée `Test Contract` à notre fonction de contructeur et appuyez sur déployer. Une fenêtre contextuelle MetaMask affichera les informations concernant la transaction, que nous devrons signer en cliquant sur "confirmer."

![Deploying Contract](/images/builders/tools/remix/remix-1.png)

Une fois la transaction incluse, le contrat apparaît dans la section "Contrats déployés" sur Remix. Là, nous pouvons interagir avec les fonctions disponibles dans notre contrat.

![Interact with Contract](/images/builders/tools/remix/remix-2.png)

## Tutoriels étape par étape {: #step-by-step-tutorials } 
Si vous êtes intéressé par un guide étape par étape plus détaillé, consultez nos tutoriels spécifiques sur l'utilisation de [Remix sur un nœud de développement Moonbeam](/getting-started/local-node/using-remix/). Les étapes peuvent également être adaptées pour se déployer sur le Moonbase Alpha TestNet en y [connectant MetaMask](/getting-started/testnet/metamask/).

