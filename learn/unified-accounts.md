---
title: Comptes unifiés
description: Moonbeam utilise désormais nativement le système de compte H160 basé sur Ethereum et est pris en charge par Polkadot JS Apps
---
# Comptes unifiés

![Intro diagram](/images/learn/unifiedaccounts-banner.png)

## Introduction {: #introduction } 

Avec la [sortie de la mise à niveau v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/) pour Moonbase Alpha TestNet, nous avons apporté des mises à jour importantes au système de compte sous-jacent sur Moonbeam, en remplaçant les comptes et les clés de style Substrate par défaut par des comptes et des clés de style Ethereum.

L'interface Polkadot JS Apps a également été mise à jour afin de prendre en charge nativement les adresses H160 et les clés ECDSA. Vous pouvez consulter [ce tutoriel](/integrations/wallets/polkadotjs/) pour en savoir plus sur cette intégration.

## Blockchain compatible avec l'EVM substrate {: #substrate-evm-compatible-blockchain } 

Toute parachaine de l'écosystème Polkadot peut offrir une implémentation EVM complète, ce qui offre la possibilité d'exécuter des contrats intelligents basés sur Solidity avec un minimum ou aucun changement. Substrate rend cette intégration possible - il suffit de brancher la [pallete EVM ](https://docs.rs/pallet-evm/2.0.1/pallet_evm/) dans votre runtime pour la prise en charge EVM, et la [palette Ethereum avec Frontier](https://github.com/paritytech/frontier) pour avoir la compatibilité Ethereum RPC. La disponibilité de ces modules open-source que Moonbeam a développés avec Parity a conduit plusieurs parachains à offrir la compatibilité Ethereum sur leurs chaînes.

Mais il y a un problème important. Avec la configuration décrite ci-dessus, un utilisateur (disons Alice) peut avoir une adresse de style Ethereum (format H160), d'une longueur de 40 + 2 caractères hexadécimaux, dans une chaîne basée sur Substrate. Cette adresse correspond à une clé privée, qui peut être utilisée pour signer des transactions du côté Ethereum de la chaîne. En outre, l'adresse est mappée dans un emplacement de stockage à l'intérieur du solde de la palette Substrate à une adresse de type Substrate (format H256). 

Cependant, Alice ne connaît que la clé privée de l'adresse H160, et non la version mappée. Par conséquent, elle ne peut pas envoyer de transactions avec son adresse H256 et est limitée uniquement aux opérations en lecture seule via l'API de Substrate. En conséquence, Alice a besoin d'une autre adresse H256 correspondant à une clé privée différente pour pouvoir opérer du côté Substrate de la chaîne, qui comprend, entre autres, le staking, les équilibres et la gouvernance. 

Le diagramme suivant illustre cette configuration.

![Old account system diagram](/images/learn/unifiedaccounts-images-1.png)

Cela peut créer des frictions et une mauvaise expérience utilisateur pour Alice. Tout d'abord, elle doit déplacer les jetons vers son adresse H256 mappée H160 pour pouvoir effectuer des transactions et déployer des contrats via l'EVM. Deuxièmement, elle doit également détenir un solde dans son autre adresse H256 (pour laquelle elle a une clé privée différente) pour utiliser les fonctionnalités basées sur substrate. Bref, Alice a besoin d'un minimum de deux clés privées pour avoir le meilleur des deux mondes.

## Comptes unifiés Moonbeam {: #moonbeam-unified-accounts } 

L'objectif de Moonbeam est de créer un environnement entièrement compatible avec Ethereum sur Polkadot avec la meilleure expérience utilisateur possible. Cela va au-delà de l'ensemble de fonctionnalités Ethereum de base, avec des fonctionnalités supplémentaires telles que la gouvernance en chaîne, le staking et les intégrations inter-chaînes.

Avec les comptes unifiés, un utilisateur (appelons-le Bob) n'aura besoin que d'une seule adresse H160, avec sa clé privée correspondante, pour faire tout ce que nous avons mentionné ci-dessus, y compris les fonctions  EVM et Substrate.

Le diagramme de cette nouvelle configuration se présente comme suit.

![New account system diagram](/images/learn/unifiedaccounts-images-2.png)

Voilà, Bob ne détient qu'une seule clé privée qui correspond à une adresse. Il n'a pas besoin de déplacer les soldes entre 2 comptes différents et est capable d'accéder à toutes les fonctionnalités avec un seul compte et une clé privée. Nous avons normalisé ce compte unique pour se conformer à l'adresse H160 de style Ethereum et aux normes clés ECDSA.
