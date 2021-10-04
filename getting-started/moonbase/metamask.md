---
title: Intégrer MetaMask
description: Apprenez à utiliser MetaMask avec le TestNet Moonbeam. Ce tutoriel vous montre comment connecter une installation par défaut de MetaMask à Moonbase Alpha.
---

# Connectez MetaMask à Moonbase Alpha

## Introduction {: #introduction } 

Ce guide décrit les étapes nécessaires pour connecter MetaMask à Moonbase Alpha. Contrairement au guide MetaMask pour un nœud de développement Moonbeam, c'est beaucoup plus simple car vous n'avez pas besoin de vous connecter à un nœud Moonbeam local en cours d'exécution. Allons-y.

Si vous avez déjà installé MetaMask, vous pouvez facilement connecter MetaMask au réseau de test Moonbase Alpha :

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask" value="moonbase">Connect MetaMask</a>
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

- Nom du Réseau: `Moonbase Alpha`
- URL RPC: `{{ networks.moonbase.rpc_url }}`
- ID de Chaine: `{{ networks.moonbase.chain_id }}`
- Symbole (facultatif): `DEV`
- Explorateur de Blocs: `{{ networks.moonbase.block_explorer }}`

![MetaMask5](/images/testnet/testnet-metamask5.png)

C'est tout! Vous avez connecté avec succès MetaMask au TestNet Moonbeam, Moonbase.
