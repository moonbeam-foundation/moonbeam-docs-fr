---
title: Moonbase Alpha
description: Un aperçu de la configuration actuelle du TestNet Moonbeam, Moonbase Alpha et des informations sur la façon de commencer à l'utiliser à l'aide de Solidity.
---

# Le TestNet Moonbase Alpha

_Updated April 5, 2021_

## Objectif {: #goal } 

Le premier TestNet Moonbeam, nommé Moonbase Alpha, vise à fournir aux développeurs un endroit pour commencer à expérimenter et à construire sur Moonbeam dans un environnement partagé. Puisque Moonbeam sera déployé en tant que parachain sur Kusama et Polkadot, nous voulons que notre TestNet reflète notre configuration de production. Pour cette raison, nous avons décidé qu'il devait s'agir d'une configuration basée sur la parachain plutôt que d'une configuration de développement substrate.

Afin de collecter autant de commentaires que possible et de fournir une résolution rapide des problèmes, nous avons mis en place un [Discord avec un canal Moonbase AlphaNet dédié](https://discord.gg/PfpUATX).

## Configuration initiale {: #initial-configuration } 

Moonbase Alpha a la configuration suivante:

 - Moonbeam fonctionne comme une parachain connectée à une chaîne de relais
 - La parachain a deux assembleurs (hébergés par PureStake) qui assemblent des blocs. Les assembleurs externes peuvent rejoindre le réseau. Seuls les nœuds supérieurs de l'assembleur {{ networks.moonbase.staking.max_collators }} par enjeu sont choisis dans l'ensemble actif
 - La chaîne de relais héberge trois validateurs (hébergés par PureStake) pour finaliser les blocs de chaîne de relais. L'un d'eux est sélectionné pour finaliser chaque bloc assemblé par les assembleurs de Moonbeam. Cette configuration permet de passer à une configuration à deux parachaïnes à l'avenir
 - Il existe deux points de terminaison RPC (hébergés par PureStake). Les utilisateurs peuvent exécuter des nœuds complets pour accéder à leurs propres points de terminaison RPC privés

![TestNet Diagramme](/images/testnet/Moonbase-Alpha-v7.png)

## Caractéristiques {: #features } 

Les fonctionnalités suivantes sont disponibles:

??? Sortie de la v1 "_Septembre 2020_"
    - Production de blocs Ethereum entièrement émulés dans le substrate (palette Ethereum)
    - Fonctions distribuables pour interagir avec l'implémentation Rust EVM ([Palette EVM](https://docs.rs/pallet-evm/2.0.1/pallet_evm/))
    - Prise en charge native d'Ethereum RPC (Web3) dans Substrate ([Frontier](https://github.com/paritytech/frontier)). Cela offre une compatibilité avec les outils de développement Ethereum tels que MetaMask, Remix et Truffle 

??? Sortie de la v2 "_Octobre 2020_"
    - Prise en charge de l'abonnement aux événements (pub / sub), qui est un composant manquant du côté RPC Web3 et couramment utilisé par les développeurs de DApp. Vous pouvez trouver un tutoriel sur la façon de vous abonner à des événements [ici](/integrations/pubsub/)
    - Prise en charge des contrats de précompilation suivants: [ecrecover](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-01-ecrecover-hash-v-r-s), [sha256](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-02-sha-256-data), [ripemd160](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-03-ripemd-160-data) et la [fonction d'identité](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-04-datacopy-data) (ou datacopy)

??? Sortie de la v3 "_Novembre 2020_"
    - Unification des comptes Substrate et Ethereum sous le format H160, un effort que nous appelons [Comptes Unifiés](https://medium.com/moonbeam-network/moonbase-alpha-v3-introducing-unified-accounts-88fae3564cda). Par conséquent, il n'y aura qu'un seul type de compte dans le système représenté par une seule adresse
    - Mise à niveau de la prise en charge de l'abonnement aux événements, ajoutant la possibilité d'utiliser des caractères génériques et une mise en forme conditionnelle pour les sujets. Vous pouvez trouver plus d'informations [ici](https://docs.moonbeam.network/integrations/pubsub/#using-wildcards-and-conditional-formatting)
    - Polkadot JS Apps prend désormais en charge nativement les adresses H160 et les clés ECDSA. Vous pouvez utiliser votre adresse de style Ethereum pour les fonctions de substrate (le cas échéant) telles que le jalonnement, les équilibres et la gouvernance. Vous pouvez trouver plus d'informations [ici](/integrations/wallets/polkadotjs/)

??? Sortie de la v4 "_Decembre 2020_"
    - Mise à jour vers la dernière version du protocole Polkadot parachain ([Parachains V1](https://w3f.github.io/parachain-implementers-guide/)), qui a corrigé plusieurs problèmes de synchronisation des nœuds, ouvrant la voie à la synchronisation de plusieurs collators dans la même parachain
    - Plusieurs améliorations apportées à nos fonctionnalités de compatibilité Ethereum:
        * L'ID d'abonnement à l'événement renvoie désormais un ID d'abonnement de style Ethereum
        * Correction de problèmes d'estimation de gaz pour des cas d'utilisation spécifiques
        * Ajout de la prise en charge du message de motif de retour
        * Prise en charge des transactions Ethereum sans ChainId

??? Sortie de la v5 "_Janvier 2021_"      
    - Ajout d'une version personnalisée du [Staking pallet](https://wiki.polkadot.network/docs/en/learn-staking) (à des fins de test et de développement uniquement)
    - Ajout de la prise en charge de l'interrogation des transactions en attente lorsqu'elles sont dans le pool 
    - Correction de certains problèmes lors de la récupération d'événements passés et d'autres correctifs mineurs liés aux événements de contrat intelligent
    - Plusieurs améliorations sous le capot qui incluent une optimisation du temps d'exécution EVM, le rendant 15 à 50 fois plus rapide
    - Prise en charge des contrats de précompilation [modexp](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x05-bigmodexp-base-exp-mod)

??? Sortie de la v6 "_Fevrier 2021_"      
    - Publication publique du [Staking pallet](https://wiki.polkadot.network/docs/en/learn-staking) personnalisée. Désormais, les détenteurs de jetons peuvent nommer des assembleurs et gagner des récompenses
    - Ajout du [Democracy pallet](https://github.com/paritytech/substrate/tree/HEAD/frame/democracy). Les détenteurs de jetons peuvent désormais [soumettre des propositions](/governance/proposals/) et [voter dessus](/governance/voting/)
    - Mise à jour vers la dernière version du [RPC Frontier](https://github.com/paritytech/frontier), ce qui augmente l'efficacité d'exécution de l’EVM d'un facteur 5
    - La limite de gaz a été augmentée à 15M par bloc, avec une limite de 13M par transaction

??? Sortie de la v7 "_Avril 2021_"      
    - Ajout de la prise en charge des modules de débogage / traçage Ethereum. Ceux-ci sont désactivés par défaut, pour les utiliser, vous devez faire tourner un nœud complet et activer la fonctionnalité
    - Correction des problèmes de propagation de bloc afin que ce ne soit plus limité aux collators, améliorant la stabilité du réseau
    - Ajout de conseils et d'un comité technique, élargissement des fonctionnalités de gouvernance
    - Le module de jalonnement a été remanié, avec de nouveaux noms pour améliorer l'expérience de l'utilisateur final
    - Ajout de trois nouvelles précompilations: [Bn128Add](https://eips.ethereum.org/EIPS/eip-196), [Bn128Mul](https://eips.ethereum.org/EIPS/eip-196) et [Bn128Pairing](https://eips.ethereum.org/EIPS/eip-197)

### Notes de version {: #release-notes } 

Pour plus de détails sur les mises à jour de Moonbase Alpha, veuillez consulter les notes suivantes:

 - [Moonbase Alpha v2](https://github.com/PureStake/moonbeam/releases/tag/v0.2.0)
 - [Moonbase Alpha v3](https://github.com/PureStake/moonbeam/releases/tag/v0.3.0)
 - [Moonbase Alpha v4](https://github.com/PureStake/moonbeam/releases/tag/v0.4.0)
 - [Moonbase Alpha v5](https://github.com/PureStake/moonbeam/releases/tag/v0.5.0)
 - [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0)
 - [Moonbase Alpha v7](https://github.com/PureStake/moonbeam/releases/tag/v0.7.0)

## Commencer {: #get-started } 

--8<-- 'text/testnet/connect.md'

## Télémétrie {: #telemetry } 

Vous pouvez voir les informations de télémétrie actuelles de Moonbase Alpha en visitant [ce lien](https://telemetry.polkadot.io/#list/Moonbase%20Alpha).

## Jetons {: #tokens } 

--8<-- 'text/testnet/faucet.md'

## Stade précoce du Proof of Stake {: #early-stage-proof-of-stake } 

Avec la sortie de Moonbase Alpha v6, Le TestNet fonctionne désormais avec un système précoce de Proof of Stake. Cela signifie qu'à des fins de test, les partenaires de Moonbeam seront encouragés à être les premiers assembleurs du réseau.

Au fur et à mesure que Moonbase Alpha progresse, nous prévoyons d'évoluer vers un réseau de preuve d'enjeu entièrement décentralisé.

## Limites {: #limitations } 

C'est le premier TestNet pour Moonbeam, il y a donc quelques limites.

Certaines [precompilations](https://docs.klaytn.com/smart-contract/precompiled-contracts) ne sont pas encore incluses dans cette version. Vous pouvez consulter une liste des précompilations prises en charge [ici](/integrations/precompiles/). Cependant, toutes les fonctions intégrées sont disponibles.

Depuis la sortie de Moonbase Alpha v6, la limite maximale de gaz par bloc a été fixée à 15M, avec une limite de gaz maximale par transaction de 13M.

Les utilisateurs n'ont accès qu'à la parachaine Moonbeam. Dans les futurs réseaux, nous ajouterons un accès à la chaîne de relais afin que les utilisateurs puissent tester le transfert de jetons.

## Purge de la chaîne {: #chain-purge } 

Ce réseau est en cours de développement actif. Parfois, des purges de chaîne peuvent être nécessaires pour réinitialiser la blockchain à son état initial. Ceci est nécessaire lors des mises à niveau ou de la maintenance majeures de TestNet. Nous annoncerons quand une purge de chaîne aura lieu via notre [Chaine Discord](https://discord.gg/PfpUATX) au moins 24 heures à l'avance.

Veuillez noter que PureStake ne migrera pas l'état de la chaîne. Ainsi, toutes les données stockées dans la blockchain seront perdues lors d'une purge de chaîne. Cependant, comme il n'y a pas de limite de gaz, les utilisateurs peuvent facilement recréer leur état de pré-purge.

