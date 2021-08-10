---
title: Aperçu
description: Un aperçu des réseaux prévus pour Moonbeam, une parachain contrat intelligent compatible Ethereum sur Polkadot.
---

# Réseaux

Nous prévoyons de créer plusieurs réseaux à longue durée de vie basés sur Moonbeam. De manière significative, Moonbeam sera déployé sur Kusama en plus de Polkadot.

Notre feuille de route en ce qui concerne les déploiements en tant que parachain est la suivante:

 - Moonbase Alpha: Parachain TestNet hébergé par PureStake (_Septembre 2020_) 
 - Moonrock: déploiement sur le TestNet Rococo  (_Mai 2021_)
 - Moonriver: déploiement sur Kusama (_Juin 2021_)
 - Moonbeam: déploiement sur Polkadot (_fin T3 2021_)
 
Cette stratégie nous permet de réduire les risques liés aux mises à niveau logicielles de Moonbeam sur le MainNet Polkadot tout en maintenant une vitesse de mise à jour raisonnable. Nous ajouterons des détails sur la façon d'accéder aux différents réseaux basés sur Moonbeam au fur et à mesure que les réseaux seront disponibles.

## Moonbase Alpha {: #moonbase-alpha } 

Ce TestNet est un réseau hébergé par PureStake. Il comporte un schéma de parachain/relay chain. L'objectif est de permettre aux développeurs de tester les fonctionnalités de compatibilité Ethereum de Moonbeam dans un environnement de parachaine partagé sans avoir besoin d'exécuter leurs propres nœuds ou réseau

[En savoir plus sur Moonbase Alpha](/networks/testnet/).

## Moonrock {: #moonrock } 

Nous avons décidé de ne pas participer aux premiers déploiements de la parachaîne sur Rococo, car nous utilisons notre propre configuration de parachain/relay chain depuis que nous avons lancé notre TestNet en septembre 2020.

Cependant, Moonrock a été déployé sur Rococo pour la première fois en mai 2021. 

## Moonriver {: #moonriver } 

Avant de se déployer sur le MainNet Polkadot, [Moonbeam launched Moonriver](https://moonbeam.network/announcements/moonriver-launch-kusama/) en tant que parachaîne sur le réseau Kusama. La fonctionnalité parachain est en ligne sur Kusama et les fonctionnalités sont progressivement mises à disposition selon le [calendrier de lancement de Moonriver](https://moonbeam.network/networks/moonriver/launch/). 

Nous prévoyons d'exercer les fonctionnalités liées aux parachaînes telles que [XCMP](https://wiki.polkadot.network/docs/learn-crosschain) et [SPREE](https://wiki.polkadot.network/docs/learn-spree) sur Moonriver dès que ces fonctionnalités seront disponibles.

[En savoir plus sur Moonriver](/networks/moonriver/).

## Moonbeam MainNet sur Polkadot {: #moonbeam-polkadot-mainnet } 

Le MainNet de production Moonbeam sera déployé en tant que parachain sur Polkadot. Ce réseau Moonbeam offrira les plus hauts niveaux de sécurité et de disponibilité. Le code en cours d'exécution sur MainNet aura généralement été vérifié par un ou plusieurs des autres réseaux énumérés ci-dessus.
