---
title: Intégrer MetaMask
description: Apprenez à utiliser MetaMask avec le TestNet Moonbeam. Ce tutoriel vous montre comment connecter une installation par défaut de MetaMask à Moonbase Alpha.
---

# Connectez MetaMask à Moonbase Alpha

## Introduction {: #introduction } 

This guide outlines the steps needed to connect MetaMask to Moonbase Alpha. In contrast to the MetaMask guide for a Moonbeam development node, this is much simpler because you don't need to connect to a local running Moonbeam node. Let's jump right into it.

Si vous avez déjà installé MetaMask, vous pouvez facilement connecter MetaMask au réseau de test Moonbase Alpha :

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask">Connect MetaMask</a>
</div>

!!! remarque
    MetaMask apparaîtra pour demander la permission d'ajouter Moonbase Alpha en tant que réseau personnalisé. Une fois que vous avez approuvé les autorisations, MetaMask basculera votre réseau actuel vers Moonbase Alpha.

--8<-- 'text/common/create-metamask-wallet.md'

![MetaMask2](/images/testnet/testnet-metamask2.png)

## Connexion à Moonbase Alpha {: #connecting-to-moonbase-alpha } 

Une fois que vous avez terminé de créer ou d'importer un portefeuille, vous verrez l'interface principale de MetaMask. Cliquez sur le logo en haut à droite et allez dans Paramètres.

![MetaMask3](/images/testnet/testnet-metamask3.png)

Accédez à l'onglet Réseaux et cliquez sur le bouton "Ajouter un réseau" .

![MetaMask4](/images/testnet/testnet-metamask4.png)

Remplissez les informations suivantes, puis cliquez sur Enregistrer:

  - Network Name: `Moonbase Alpha`
  - RPC URL: `{{ networks.moonbase.rpc_url }}`
  - ChainID: `{{ networks.moonbase.chain_id }}`
  - Symbol (Optional): `DEV`
  - Block Explorer: `{{ networks.moonbase.block_explorer }}`
  
![MetaMask5](/images/testnet/testnet-metamask5.png)

C'est tout! Vous avez connecté avec succès MetaMask au TestNet Moonbeam, Moonbase.
