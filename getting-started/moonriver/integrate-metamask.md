---
title: Intégration MetaMask
description: Apprenez à utiliser MetaMask avec Moonriver (Réseau principal de Moonbeam, déployé sur Kusama). Ce tutoriel vous montre comment connecter une installation par défaut de MetaMask à Moonriver.
---

# Connectez MetaMask à Moonriver

## Introduction {: #introduction } 

Ce guide décrit les étapes nécessaires pour connecter MetaMask à Moonriver. Contrairement à notre précédent guide MetaMask, c'est beaucoup plus simple car vous n'avez pas besoin de vous connecter à un nœud Moonbeam en cours d'exécution local. Allons-y.

--8<-- 'text/common/create-metamask-wallet.md'

## Connexion à Moonriver {: #connecting-to-moonriver } 

Une fois que vous avez terminé de créer ou d'importer un portefeuille, vous verrez l'interface principale de MetaMask. Cliquez sur le logo en haut à droite et allez dans Paramètres.

![MetaMask3](/images/testnet/testnet-metamask3.png)

Accédez à l'onglet Réseaux et cliquez sur le bouton "Ajouter un réseau".

![MetaMask4](/images/testnet/testnet-metamask4.png)

Remplissez les informations suivantes, puis cliquez sur Enregistrer:

 - Nom du réseau: `Moonriver`
 - Nouvelle URL de RPC: `{{ networks.moonriver.rpc_url }}`
 - ID de chaîne: `{{ networks.moonriver.chain_id }}`
 - Symbole (facultatif): `MOVR`
 - URL de l'explorateur de blocs (facultatif): `{{ networks.moonriver.block_explorer }}`

![Add Moonriver to MetaMask](/images/moonriver/moonriver-integrate-metamask-1.png)

Tout est parfait, vous avez réussi à vous connecter avec succès votre MetaMask à Moonriver le réseau principal de Moonbeam, déployé sur Kusama.

![MetaMask connected to Moonriver](/images/moonriver/moonriver-integrate-metamask-2.png)

!!! note
   Moonriver (MOVR) n'est actuellement pas disponible à la vente. Si vous avez participé au crowdloan, vos récompenses seront visibles dès que les transferts seront activés dans la phase 4 de l'opération [Moonriver network launch](https://moonbeam.network/networks/moonriver/launch/).