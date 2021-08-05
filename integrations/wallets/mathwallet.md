---
title: MathWallet
description: Ce guide vous explique comment connecter Mathwallet, un portefeuille basé sur un navigateur qui fonctionne avec Ethereum, à Moonbeam.
---

# Interagir avec Moonbeam à l'aide de MathWallet
 
![Intro banner](/images/mathwallet/mathwallet-banner.png)

## Introduction {: #introduction } 

MathWallet [announced](https://mathwallet.org/moonbeam-wallet/en/) that is now natively supporting the [Moonbase Alpha TestNet](/networks/moonbase/) and [Moonriver](/networks/moonriver/). This means that you are now able to interact with Moonbase Alpha and Moonriver using another wallet besides MetaMask.

In this tutorial, we'll go through how to setup MathWallet to connect to our [TestNet](#connect-to-moonbase-alpha) and [Moonriver](#connect-to-moonriver). We'll also present a brief example of using MathWallet as a Web3 provider for other tools such as [Remix](/integrations/remix/).

## Checking Prerequisites {: #checking-prerequisites }

Tout d'abord, vous devez installer l'extension de navigateur MathWallet, que vous pouvez obtenir sur leur [site Web](https://mathwallet.org/en-us/).

Une fois l'extension de navigateur installée, veuillez l'ouvrir et définir un mot de passe.

![Set wallet password](/images/mathwallet/mathwallet-images-1.png)

## Connectez MathWallet à Moonbase Alpha {: #connect-to-moonbase-alpha } 

In this part, we'll go through the process of connecting MathWallet to Moonbase Alpha. Enable Moonbase Alpha under Settings (top right gear icon) -> Networks -> Ethereum.

![Enable Moonbase Alpha](/images/mathwallet/mathwallet-images-2.png)

Et enfin, sur l'écran principal, cliquez sur Changer de réseau et sélectionnez Moonbase Alpha

![Connect to Moonbase Alpha](/images/mathwallet/mathwallet-images-3.png)

Et voilà, vous avez maintenant MathWallet connecté au TestNet Moonbase Alpha! Votre portefeuille devrait ressembler à ceci:

![Wallet Connected to Moonbase Alpha](/images/mathwallet/mathwallet-images-4.png)

## Connectez à Moonriver {: #connect-to-moonbase-alpha } 

Getting started with Moonriver is a straightforward process. All you have to do is click Switch Network and select Moonriver

![Connect to Moonriver](/images/mathwallet/mathwallet-images-5.png)

And that is it, you now have MathWallet connected to Moonriver! Your wallet should look like this:

![Wallet Connected to Moonriver](/images/mathwallet/mathwallet-images-6.png)

## Ajouter un portefeuille {: #adding-a-wallet } 

The following steps will show you how to interact with the Moonbase Alpha TestNet, but can also be used to interact with Moonriver.

After you are connected to Moonbase Alpha, you can now create a wallet to get an account and start interacting with the TestNet. Currently, there are three ways to add a wallet:

 - Créer un portefeuille
 - Importer un portefeuille existant à l'aide d'un mnémonique ou d'une clé privée
- Connecter le portefeuille matériel (_non pris en charge pour le moment_)

### Créer un portefeuille {: #create-a-wallet } 

The following steps for creating a wallet can be modified for Moonriver.

Pour créer un nouveau portefeuille, cliquez sur le signe + à côté de "Moonbase Alpha" et sélectionnez "Créer un portefeuille".

![MathWallet create a wallet](/images/mathwallet/mathwallet-images-7.png)

Définissez et confirmez un nom de portefeuille. Ensuite, assurez-vous de stocker en toute sécurité le mnémonique, car il offre un accès direct à vos fonds. Une fois le processus terminé, vous devriez voir votre portefeuille nouvellement créé avec son adresse publique associée.

![MathWallet wallet created](/images/mathwallet/mathwallet-images-8.png)

### Importer un portefeuille {: #import-a-wallet } 

Pour créer un nouveau portefeuille, cliquez sur le signe +  à côté de "Moonbase Alpha" et sélectionnez "Importer un portefeuille".

![MathWallet import a wallet](/images/mathwallet/mathwallet-images-9.png)

Ensuite, choisissez entre l'importation à l'aide d'un mnémonique ou d'une clé privée. Pour la première option, entrez le mnémonique mot par mot, séparé par des espaces. Pour la deuxième option, entrez la clé privée (avec le préfixe 0x ou non, cela fonctionne dans les deux cas).

![MathWallet private key or mnemonic import](/images/mathwallet/mathwallet-images-10.png)

Après avoir cliqué sur Suivant, définissez un nom de portefeuille, et c'est tout! Vous devriez voir votre portefeuille importé avec son adresse publique associée.

![MathWallet imported wallet](/images/mathwallet/mathwallet-images-11.png)

## Utilisation de MathWallet {: #using-mathwallet } 

MathWallet serves as a Web3 provider in tools such as [Remix](/integrations/remix/). By having MathWallet connected to Moonbase Alpha or Moonriver, you can deploy contracts as you would like using MetaMask, signing the transactions with MathWallet instead.

For example, in Remix, when deploying a smart contract to Moonbase Alpha, make sure you select the "Injected Web3" option in the "Environment" menu. If you have MathWallet connected, you will see the TestNet chain ID just below the box (_1287_) and your MathWallet account injected into Remix as well. When sending a transaction, you should see a similar pop-up from MathWallet:

![MathWallet sign transaction](/images/mathwallet/mathwallet-images-12.png)

En cliquant sur «Accepter», vous signez cette transaction et le contrat sera déployé sur le TestNet Moonbase Alpha.

