---
title: Aperçu
description: Un aperçu des réseaux prévus pour Moonbeam, une parachain contrat intelligent compatible Ethereum sur Polkadot.
---

# Réseaux

Nous prévoyons de créer plusieurs réseaux à longue durée de vie basés sur Moonbeam. De manière significative, Moonbeam sera déployé sur Kusama en plus de Polkadot.

Notre feuille de route en ce qui concerne les déploiements en tant que parachain est la suivante:

 - Moonbase Alpha: Parachain TestNet hébergé par PureStake (_Septembre 2020_) 
 - Moonrock: déploiement sur le TestNet Rococo  (_May 2021_)
 - Moonriver: déploiement sur Kusama (June 2021_)
 - Moonbeam: déploiement sur Polkadot (_fin T3 2021_)
 
Cette stratégie nous permet de réduire les risques liés aux mises à niveau logicielles de Moonbeam sur le MainNet Polkadot tout en maintenant une vitesse de mise à jour raisonnable. Nous ajouterons des détails sur la façon d'accéder aux différents réseaux basés sur Moonbeam au fur et à mesure que les réseaux seront disponibles.

## Moonbase Alpha {: #moonbase-alpha } 

Ce TestNet est un réseau hébergé par PureStake. Il comporte un schéma de chaîne parachain / relais. L'objectif est de permettre aux développeurs de tester les fonctionnalités de compatibilité Ethereum de Moonbeam dans un environnement de parachaine partagé sans avoir besoin d'exécuter leurs propres nœuds ou réseau

[En savoir plus sur Moonbase Alpha](/networks/testnet/).

## Moonrock {: #moonrock } 

We decided not to participate in the first parachain deployments to Rococo because we have been running our own parachain/relay chain setup since we launched our TestNet in September 2020.

However, Moonrock was deployed to Rococo for the first time in May 2021. 

## Moonriver {: #moonriver } 

In advance of deploying to the Polkadot MainNet, [Moonbeam launched Moonriver](https://moonbeam.network/announcements/moonriver-launch-kusama/) as a parachain on the Kusama network. The parachain functionality is live on Kusama and features are progressively being released according to the [Moonriver launch schedule](https://moonbeam.network/networks/moonriver/launch/). 

We plan to exercise parachain-related functionality such as [XCMP](https://wiki.polkadot.network/docs/learn-crosschain) and [SPREE](https://wiki.polkadot.network/docs/learn-spree) on Moonriver as those features become available.

[Learn more about Moonriver](/networks/moonriver/).

## Moonbeam MainNet sur Polkadot {: #moonbeam-polkadot-mainnet } 

Le MainNet de production Moonbeam sera déployé en tant que parachain sur Polkadot. Ce réseau Moonbeam offrira les plus hauts niveaux de sécurité et de disponibilité. Le code en cours d'exécution sur MainNet aura généralement été vérifié par un ou plusieurs des autres réseaux énumérés ci-dessus.
