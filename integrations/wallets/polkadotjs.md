---
title: Polkadot JS Apps
description: Suivez ce tutoriel rapide pour apprendre à utiliser les adresses H160 standard Ethereum de Moonbeam avec des applications basées sur substrate comme Polkadot JS.
---
# Polkadot JS Apps

![Intro diagram](/images/tokens/connect/polkadotjs/polkadotjs-banner.png)

## Introduction {: #introduction } 

Avec la [sortie de la mise a niveau v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/) pour le TestNet Moonbase Alpha, nous avons apporté des mises à jour importantes au système de compte sous-jacent sur Moonbeam, en remplaçant les comptes et les clés de style Substrate par défaut par des comptes et des clés de style Ethereum.

L'interface Polkadot JS Apps a également été mise à jour afin de prendre en charge nativement les adresses H160 et les clés ECDSA. Donc, dans ce tutoriel, vérifions cette nouvelle intégration de comptes basés sur Ethereum sur le site Polkadot JS Apps.

## Connexion à Moonbase Alpha {: #connecting-to-moonbase-alpha } 

Tout d'abord, nous devons le connecter au TestNet Moonbase Alpha en cliquant sur le logo en haut à gauche et en sélectionnant Moonbase Alpha (sous Test Networks).

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-1.png)

Après le changement, le site Polkadot JS se connectera non seulement à Moonbase Alpha, mais changera également son style pour faire une correspondance parfaite.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-2.png)

## Créer ou importer un compte H160 {: #creating-or-importing-an-h160-account } 

Voyons comment nous pouvons créer un nouveau compte ou importer un compte MetaMask déjà existant dans Polkadot JS Apps. Tout d'abord, accédez à la section comptes (1) et cliquez sur le bouton ajouter un compte (2).

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-3.png)

Cela ouvrira une fenêtre contextuelle d'assistant qui vous guidera tout au long du processus d'ajout d'un compte à l'interface Polkadot JS Apps. Assurez-vous de cliquer sur le menu déroulant et de passer de Mnemonic à Raw seed, cela vous permet d'ajouter un compte via une clé privée.

!!! remarque
    Actuellement, vous ne pouvez créer ou importer des comptes dans PolkadotJS que via une clé privée. Le faire avec le mnémonique entraînera une adresse publique différente si vous essayez ultérieurement d'importer ce compte dans un portefeuille Ethereum tel que MetaMask. En effet, PolkadotJS utilise BIP39, tandis qu'Ethereum utilise BIP32 ou BIP44.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-4.png)

Ensuite, si vous souhaitez créer un nouveau compte, assurez-vous de stocker la clé privée affichée par l'assistant. Si vous souhaitez importer un compte existant, entrez votre clé privée que vous pouvez exporter à partir de MetaMask, dans ce cas, nous importons le compte suivant:

- Clé privée:: `28194e8ddb4a2f2b110ee69eaba1ee1f35e88da2222b5a7d6e3afa14cf7a3347`
- Adresse publique: `0x44236223aB4291b93EEd10E4B511B37a398DEE55` 

!!! remarque
    Ne révélez jamais vos clés privées car elles donnent un accès direct à vos fonds. Les étapes de ce guide sont uniquement à des fins de démonstration. 
    
Assurez-vous d'inclure le préfixe dans la clé privée, c'est-à-dire 0x. Si vous avez correctement saisi les informations, l'adresse publique correspondante doit apparaître dans le coin supérieur gauche de la fenêtre.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-5.png)

Cliquez sur suivant et terminez l'assistant en définissant un nom de compte et un mot de passe. Après un message de confirmation, vous devriez voir dans l'onglet Comptes principal l'adresse avec le solde correspondant: dans notre cas, l'adresse de Bob. De plus, nous pouvons superposer l'extension MetaMask pour voir que les deux soldes sont identiques.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-6.png)

## Envoi d'une transaction via l'API de Substrate {: #sending-a-transaction-through-substrates-api } 

Maintenant, démontrons le potentiel du schéma de comptes unifiés de Moonbeam en effectuant un transfert via l'API de Substrate à l'aide de l’applications Polkadot JS. N'oubliez pas que nous interagissons avec Substrate en utilisant une adresse H160 de style Ethereum. Pour ce faire, nous avons importé un autre compte nommé Charley avec 5 jetons `DEV`.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-7.png)

Ensuite, cliquez sur le bouton d'envoi de Bob, qui ouvre un autre assistant qui vous guide tout au long du processus d'envoi d'une transaction. Définissez l'adresse d'envoi et le montant, qui pour notre exemple est de 5 jetons DEV. Lorsque vous êtes prêt, cliquez sur le bouton "Effectuer le transfert".

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-8.png)

Une fois la transaction signée à l'aide du mot de passe, Polkadot JS affichera des messages dans le coin supérieur droit pendant son traitement. Une fois confirmé, vous devriez voir les soldes mis à jour pour chaque compte.

![Connect to Moonbase Alpha](/images/tokens/connect/polkadotjs/polkadotjs-app-8.png)

Et c'est tout! Nous sommes ravis de pouvoir prendre en charge les comptes H160 dans Polkadot JS Apps, car nous pensons que cela améliorera considérablement l'expérience utilisateur dans le réseau Moonbeam et ses fonctionnalités de compatibilité Ethereum.
