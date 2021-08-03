---
title: Compatibilité Eth
description: Il peut sembler intimidant de passer à une parachain Polkadot si vous êtes habitué à Ethereum. Voici à quoi vous attendre lorsque vous utilisez Moonbeam pour la première fois.
---

# Compatibilité Ethereum

## Les Différences entre Moonbeam et Ethereum {: #differences-between-moonbeam-and-ethereum } 

Alors que Moonbeam s'efforce d'être compatible avec l'API Web3 d'Ethereum et l'EVM, il existe un certain nombre de différences importantes avec Moonbeam.

Premièrement, Moonbeam utilise un mécanisme de consensus basé sur la preuve d'enjeu, ce qui signifie que les concepts de preuve de travail, tels que la difficulté, les oncles, le hashrate, etc., n'ont généralement pas de sens dans Moonbeam. Pour les API qui renvoient des valeurs liées au proof of Work d'Ethereum, nous renvoyons les valeurs par défaut. Les contrats Ethereum existants qui reposent sur des éléments internes de preuve de travail (par exemple, les contrats de pool minier) ne fonctionneront certainement pas comme prévu sur Moonbeam.

Une autre différence significative entre Moonbeam et Ethereum est que Moonbeam comprend un ensemble complet de fonctionnalités de gouvernance en chaîne basées sur la fonctionnalité Substrate. Ces modules de gouvernance onchain incluent des fonctionnalités permettant de mettre à niveau la blockchain elle-même sur la base d'un vote pondéré par jeton.

## Ce qui reste pareil {: #what-stays-the-same } 

Si vous déplacez des parties de vos charges de travail existantes et de l'état de Ethereum Layer 1 vers Moonbeam, vous pouvez vous attendre à des modifications minimales requises (à l'exception des exceptions notées ci-dessus). Vos applications, contrats et outils resteront en grande partie inchangés.

Moonbeam prend en charge:

 - **Contrats intelligents basés sur Solidity**
 - **Outils d'écosystème** (par exemple, e.g., explorateurs de blocs, bibliothèques de développement frontales, portefeuilles)
 - **Outils de développement** (e.g., Truffle, Remix, MetaMask)
 - **Jetons Ethereum via des ponts** (par exemple, e.g., mouvement de jeton, visibilité de l'état, passage de message)
