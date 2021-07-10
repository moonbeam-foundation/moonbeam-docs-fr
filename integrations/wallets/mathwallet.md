---
title: MathWallet
description: Ce guide vous explique comment connecter Mathwallet, un portefeuille basé sur un navigateur qui fonctionne avec Ethereum, à Moonbeam.
---

# Interagir avec Moonbeam à l'aide de MathWallet
 
![Intro banner](/images/mathwallet/mathwallet-banner.png)

## Introduction

MathWallet à [announcé](https://mathwallet.org/moonbeam-wallet/en/) qu'il supporte désormais nativement le [TestNet Moonbase Alpha](/networks/testnet/). Cela signifie que vous pouvez désormais interagir avec Moonbase Alpha en utilisant un autre portefeuille en plus de MetaMask.

Dans ce tutoriel, nous verrons comment configurer MathWallet pour se connecter à notre TestNet. Nous présenterons également un bref exemple d'utilisation de MathWallet en tant que fournisseur Web3 pour d'autres outils tels que [Remix](/integrations/remix/).

## Connectez MathWallet à Moonbeam

Dans cette partie, nous allons passer par le processus de connexion de MathWallet à Moonbase Alpha. 

Tout d'abord, vous devez installer l'extension de navigateur MathWallet, que vous pouvez obtenir sur leur [site Web](https://mathwallet.org/en-us/).

Une fois l'extension de navigateur installée, veuillez l'ouvrir et définir un mot de passe.

![Set wallet password](/images/mathwallet/mathwallet-images-1.png)

Ensuite, activons Moonbase Alpha sous Paramètres (icône d'engrenage en haut à droite) -> Réseaux -> Ethereum.

![Enable Moonbase Alpha](/images/mathwallet/mathwallet-images-2.png)

Et enfin, sur l'écran principal, cliquez sur Changer de réseau et sélectionnez Moonbase Alpha

![Connect to Moonbase Alpha](/images/mathwallet/mathwallet-images-3.png)

Et voilà, vous avez maintenant MathWallet connecté au TestNet Moonbase Alpha! Votre portefeuille devrait ressembler à ceci:

![Wallet Connected to Moonbase Alpha](/images/mathwallet/mathwallet-images-4.png)

## Ajouter un portefeuille

Maintenant que MathWallet est connecté à Moonbase Alpha, nous pouvons créer un portefeuille pour obtenir un compte et commencer à interagir avec le TestNet. Actuellement, il existe trois façons d'ajouter un portefeuille:

 - Créer un portefeuille
 - Importer un portefeuille existant à l'aide d'un mnémonique ou d'une clé privée
- Connecter le portefeuille matériel (_non pris en charge pour le moment_)

### Créer un portefeuille

Pour créer un nouveau portefeuille, cliquez sur le signe + à côté de "Moonbase Alpha" et sélectionnez "Créer un portefeuille".

![MathWallet create a wallet](/images/mathwallet/mathwallet-images-5.png)

Définissez et confirmez un nom de portefeuille. Ensuite, assurez-vous de stocker en toute sécurité le mnémonique, car il offre un accès direct à vos fonds. Une fois le processus terminé, vous devriez voir votre portefeuille nouvellement créé avec son adresse publique associée.

![MathWallet wallet created](/images/mathwallet/mathwallet-images-6.png)

### Importer un portefeuille

Pour créer un nouveau portefeuille, cliquez sur le signe +  à côté de "Moonbase Alpha" et sélectionnez "Importer un portefeuille".

![MathWallet import a wallet](/images/mathwallet/mathwallet-images-7.png)

Ensuite, choisissez entre l'importation à l'aide d'un mnémonique ou d'une clé privée. Pour la première option, entrez le mnémonique mot par mot, séparé par des espaces. Pour la deuxième option, entrez la clé privée (avec le préfixe 0x ou non, cela fonctionne dans les deux cas).

![MathWallet private key or mnemonic import](/images/mathwallet/mathwallet-images-8.png)

Après avoir cliqué sur Suivant, définissez un nom de portefeuille, et c'est tout! Vous devriez voir votre portefeuille importé avec son adresse publique associée.

![MathWallet imported wallet](/images/mathwallet/mathwallet-images-9.png)

## Utilisation de MathWallet

MathWallet sert de fournisseur Web3 dans des outils tels que [Remix](/integrations/remix/). En ayant MathWallet connecté à Moonbase Alpha, vous pouvez déployer des contrats comme vous le souhaitez en utilisant MetaMask, en signant les transactions avec MathWallet à la place.

Par exemple, dans Remix, lors du déploiement d'un contrat intelligent, assurez-vous de sélectionner l'option "Injected Web3" dans le menu "Environment". Si vous avez connecté MathWallet, vous verrez l'ID de la chaîne TestNet juste en dessous ( 1287 ) et votre compte MathWallet injecté dans Remix également. Lors de l'envoi d'une transaction, vous devriez voir une fenêtre contextuelle similaire sur MathWallet:

![MathWallet sign transaction](/images/mathwallet/mathwallet-images-10.png)

En cliquant sur «Accepter», vous signez cette transaction et le contrat sera déployé sur le TestNet Moonbase Alpha.

