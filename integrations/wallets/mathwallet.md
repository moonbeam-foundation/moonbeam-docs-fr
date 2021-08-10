---
title: MathWallet
description: Ce guide vous explique comment connecter Mathwallet, un portefeuille basé sur un navigateur qui fonctionne avec Ethereum, à Moonbeam.
---

# Interagir avec Moonbeam à l'aide de MathWallet
 
![Intro banner](/images/mathwallet/mathwallet-banner.png)

## Introduction {: #introduction } 

MathWallet à [announcé](https://mathwallet.org/moonbeam-wallet/en/) qui supporte maintenant nativement le [Moonbase Alpha TestNet](/networks/moonbase/) et [Moonriver](/networks/moonriver/). Cela signifie que vous êtes maintenant en mesure d'interagir avec Moonbase Alpha et Moonriver en utilisant un autre portefeuille que MetaMask.

Dans ce tutoriel, nous verrons comment configurer MathWallet pour qu'il se connecte à nos applications [TestNet](#connect-to-moonbase-alpha) et [Moonriver](#connect-to-moonriver). Nous présenterons également un bref exemple d'utilisation de MathWallet comme Web3 provider pour d'autres outils tels que [Remix](/integrations/remix/).

## Vérification des prérequis {: #checking-prerequisites }

Tout d'abord, vous devez installer l'extension de navigateur MathWallet, que vous pouvez obtenir sur leur [site Web](https://mathwallet.org/en-us/).

Une fois l'extension de navigateur installée, veuillez l'ouvrir et définir un mot de passe.

![Définir le mot de passe du portefeuille](/images/mathwallet/mathwallet-images-1.png)

## Connectez MathWallet à Moonbase Alpha {: #connect-to-moonbase-alpha } 

Dans cette partie, nous allons suivre le processus de connexion de MathWallet à Moonbase Alpha. Activez Moonbase Alpha sous Paramètres (icône d'engrenage en haut à droite) -> Réseaux -> Ethereum.

![Activez Moonbase Alpha](/images/mathwallet/mathwallet-images-2.png)

Et enfin, sur l'écran principal, cliquez sur Changer de réseau et sélectionnez Moonbase Alpha

![Connectez-vous à Moonbase Alpha](/images/mathwallet/mathwallet-images-3.png)

Et voilà, vous avez maintenant MathWallet connecté au TestNet Moonbase Alpha! Votre portefeuille devrait ressembler à ceci:

![Portefeuille connecté à Moonbase Alpha](/images/mathwallet/mathwallet-images-4.png)

## Connectez à Moonriver {: #connect-to-moonbase-alpha } 

Le démarrage avec Moonriver est un processus simple. Tout ce que vous avez à faire, c'est de cliquer sur Switch Network et de sélectionner Moonriver.

![Connectez-vous à Moonriver](/images/mathwallet/mathwallet-images-5.png)

Et c'est tout, vous avez maintenant MathWallet connecté à Moonriver ! Votre portefeuille devrait ressembler à ceci :

![Portefeuille connecté à Moonriver](/images/mathwallet/mathwallet-images-6.png)

## Ajouter un portefeuille {: #adding-a-wallet } 

The following steps will show you how to interact with the Moonbase Alpha TestNet, but can also be used to interact with Moonriver.

Les étapes suivantes vous montrent comment interagir avec le TestNet de Moonbase Alpha, mais peuvent également être utilisées pour interagir avec Moonriver.

- Créer un portefeuille
- Importer un portefeuille existant à l'aide d'un mnémonique ou d'une clé privée
- Connecter le portefeuille matériel (_non pris en charge pour le moment_)

### Créer un portefeuille {: #create-a-wallet } 

Les étapes suivantes pour la création d'un portefeuille peuvent être modifiées pour Moonriver.

Pour créer un nouveau portefeuille, cliquez sur le signe + à côté de "Moonbase Alpha" et sélectionnez "Créer un portefeuille".

![Créer un porte-monnaie MathWallet](/images/mathwallet/mathwallet-images-7.png)

Définissez et confirmez un nom de portefeuille. Ensuite, assurez-vous de stocker en toute sécurité le mnémonique, car il offre un accès direct à vos fonds. Une fois le processus terminé, vous devriez voir votre portefeuille nouvellement créé avec son adresse publique associée.

![Création du portefeuille MathWallet](/images/mathwallet/mathwallet-images-8.png)

### Importer un portefeuille {: #import-a-wallet } 

Pour créer un nouveau portefeuille, cliquez sur le signe +  à côté de "Moonbase Alpha" et sélectionnez "Importer un portefeuille".

![Importe un porte-monnaie MathWallet](/images/mathwallet/mathwallet-images-9.png)

Ensuite, choisissez entre l'importation à l'aide d'un mnémonique ou d'une clé privée. Pour la première option, entrez le mnémonique mot par mot, séparé par des espaces. Pour la deuxième option, entrez la clé privée (avec le préfixe 0x ou non, cela fonctionne dans les deux cas).

![Importation de la clé privée ou du mnémonique de MathWallet](/images/mathwallet/mathwallet-images-10.png)

Après avoir cliqué sur Suivant, définissez un nom de portefeuille, et c'est tout! Vous devriez voir votre portefeuille importé avec son adresse publique associée.

![Portefeuille importé MathWallet](/images/mathwallet/mathwallet-images-11.png)

## Utilisation de MathWallet {: #using-mathwallet } 

MathWallet sert de Web3 provider dans des outils tels que [Remix](/integrations/remix/). En ayant MathWallet connecté à Moonbase Alpha ou Moonriver, vous pouvez déployer des contrats comme vous le souhaitez en utilisant MetaMask, en signant les transactions avec MathWallet à la place.

Par exemple, dans Remix, lorsque vous déployez un contrat intelligent sur Moonbase Alpha, assurez-vous de sélectionner l'option "Injected Web3" dans le menu "Environment". Si vous avez connecté MathWallet, vous verrez l'ID de la chaîne TestNet juste en dessous de la boîte (_1287_) et votre compte MathWallet injecté dans Remix également. Lorsque vous envoyez une transaction, vous devriez voir un pop-up similaire de MathWallet :

![Transaction avec le MathWallet](/images/mathwallet/mathwallet-images-12.png)

En cliquant sur «Accepter», vous signez cette transaction et le contrat sera déployé sur le TestNet Moonbase Alpha.

