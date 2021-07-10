---
title: Aperçu
description: Un aperçu des réseaux prévus pour Moonbeam, une parachain contrat intelligent compatible Ethereum sur Polkadot.
---

# Réseaux

Nous prévoyons de créer plusieurs réseaux à longue durée de vie basés sur Moonbeam. De manière significative, Moonbeam sera déployé sur Kusama en plus de Polkadot.

Notre feuille de route en ce qui concerne les déploiements en tant que parachain est la suivante:

 - Moonbase Alpha: Parachain TestNet hébergé par PureStake (_Septembre 2020_) 
 - Moonrock: déploiement sur le TestNet Rococo  (_à déterminer_)
 - Moonriver: déploiement sur Kusama (_fin T2 2021_)
 - Moonbeam: déploiement sur Polkadot (_fin T3 2021_)
 
Cette stratégie nous permet de réduire les risques liés aux mises à niveau logicielles de Moonbeam sur le MainNet Polkadot tout en maintenant une vitesse de mise à jour raisonnable. Nous ajouterons des détails sur la façon d'accéder aux différents réseaux basés sur Moonbeam au fur et à mesure que les réseaux seront disponibles.

## Moonbase Alpha

Ce TestNet est un réseau hébergé par PureStake. Il comporte un schéma de chaîne parachain / relais. L'objectif est de permettre aux développeurs de tester les fonctionnalités de compatibilité Ethereum de Moonbeam dans un environnement de parachaine partagé sans avoir besoin d'exécuter leurs propres nœuds ou réseau

[En savoir plus sur Moonbase Alpha](/networks/testnet/).

## Moonrock  

Nous avons décidé de ne pas participer aux premiers déploiements de parachaine/Relais au Rococo. Nous exécutons notre propre configuration de chaîne relais depuis le lancement de notre TestNet en septembre 2020.

Cependant, nous prévoyons de déployer Moonbeam en tant que parachaine sur le TestNet Rococo une fois que les fonctionnalités d'interopérabilité seront disponibles. Cela fournira un endroit pour tester ces fonctionnalités avec d'autres chaînes.

## Moonriver

Moonbeam sera lancé en tant que parachaine sur le réseau Kusama, avant le déploiement sur le MainNet Polkadot ([plus de détails ici](https://www.purestake.com/news/moonbeam-on-kusama/)). Cela nécessite que la fonctionnalité de parachaine soit en direct sur Kusama. 

Nous prévoyons d'exercer des fonctionnalités liées à la parachain comme telles que [Crowdloan](https://wiki.polkadot.network/docs/en/learn-crowdloans), [XCMP](https://wiki.polkadot.network/docs/en/learn-crosschain), et [SPREE](https://wiki.polkadot.network/docs/en/learn-spree) sur Moonriver à mesure que ces fonctionnalités seront disponibles.

## Moonbeam MainNet sur Polkadot

Le MainNet de production Moonbeam sera déployé en tant que parachain sur Polkadot. Ce réseau Moonbeam offrira les plus hauts niveaux de sécurité et de disponibilité. Le code en cours d'exécution sur MainNet aura généralement été vérifié par un ou plusieurs des autres réseaux énumérés ci-dessus.
