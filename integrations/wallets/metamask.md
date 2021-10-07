---
title: MetaMask
description: Ce guide vous explique comment connecter MetaMask, un portefeuille Ethereum basé sur un navigateur, à Moonbeam.
---

# Interagir avec Moonbeam à l'aide de MetaMask

![Intro diagram](/images/tokens/connect/metamask/metamask-banner.png)

## Introduction {: #introduction } 

Les développeurs peuvent tirer parti des fonctionnalités de compatibilité Ethereum de Moonbeam pour intégrer des outils, tels que [MetaMask](https://metamask.io/), dans leurs DApp. Ce faisant, ils peuvent utiliser la bibliothèque injectée fournie par MetaMask pour interagir avec la blockchain.

Actuellement, MetaMask peut être configuré pour se connecter à deux réseaux: un nœud de développement Moonbeam ou le TestNet Moonbase Alpha.

Si vous avez déjà installé MetaMask, vous pouvez facilement connecter MetaMask au TestNet Moonbase Alpha :

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask" value="moonbase">Connect to MetaMask</a>
</div>

!!! remarque
    MetaMask apparaîtra pour demander la permission d'ajouter Moonbase Alpha en tant que réseau personnalisé. Une fois que vous avez approuvé les autorisations, MetaMask basculera votre réseau actuel vers Moonbase Alpha.

Apprenez [comment intégrer un bouton Connecter MetaMask](https://medium.com/moonbeam-network/integrate-metamask-into-a-dapp-ea7528c5a786) dans votre dapp, afin que les utilisateurs puissent se connecter à Moonbase Alpha d'un simple clic sur un bouton.

## Connectez MetaMask à Moonbeam {: #connect-metamask-to-moonbeam } 

Une fois [MetaMask](https://metamask.io/) installé, vous pouvez le connecter à Moonbeam en cliquant sur le logo en haut à droite et en ouvrant les paramètres.

![MetaMask3](/images/tokens/connect/metamask/metamask-6.png)

Ensuite, accédez à l'onglet Réseaux et cliquez sur le bouton "Ajouter un réseau" .

![MetaMask4](/images/tokens/connect/metamask/metamask-7.png)

Ici, vous pouvez configurer MetaMask pour les réseaux suivants:

Nœud de développement Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

TestNet Moonbase Alpha :

--8<-- 'text/testnet/testnet-details.md'

## Tutoriels étape par étape {: #step-by-step-tutorials } 

Dans le cas où vous seriez intéressé par un guide pas à pas plus détaillé, vous pouvez accéder à nos tutoriels spécifiques:

 - MetaMask sur un [nœud de développement Moonbeam](/getting-started/local-node/using-metamask/)
 - MetaMask sur [Moonbase Alpha](/getting-started/testnet/metamask/)
