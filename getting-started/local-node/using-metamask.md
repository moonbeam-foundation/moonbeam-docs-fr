---
title: Utiliser MetaMask
description: Ce tutoriel vous explique comment interagir avec un nœud Moonbeam local à l'aide d'une installation par défaut du plug-in de navigateur MetaMask.
---

# Interagir avec un nœud Moonbeam à l'aide de MetaMask

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//hrpBd2-a7as' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>You can find all of the relevant code for this tutorial on the <a href="{{ config.site_url }}resources/code-snippets/">code snippets page</a></div>

## Introduction

MetaMask peut être utilisé pour se connecter à Moonbeam via le Moonbase Alpha TestNet ou via un nœud de développement Moonbeam fonctionnant localement.

Ce guide décrit les étapes nécessaires pour connecter MetaMask à un nœud de développement autonome Moonbeam afin d'envoyer des jetons entre les comptes. Si vous n'avez pas encore configuré votre propre nœud de développement local, reportez-vous à [ce tutoriel](/getting-started/local-node/setting-up-a-node/),  ou suivez les instructions du [Repo Github](https://github.com/PureStake/moonbeam/).

!!! note
    Ce tutoriel a été créé à l'aide du tag {{ networks.development.build_tag}} qui est basé sur la version {{ networks.moonbase.version }} de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.moonbase.version }}). La plate-forme Moonbeam et les composants [Frontier](https://github.com/paritytech/frontier) sur lesquels elle s'appuie pour la compatibilité Ethereum basée sur substrate sont toujours en cours de développement. 
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Vous pouvez interagir avec Moonbeam de deux manières: en utilisant des points de terminaison RPC Substrate ou en utilisant des points de terminaison RPC compatibles Web3. Ces derniers points de terminaison sont actuellement servis à partir du même serveur RPC que ceux de substrate. Dans ce tutoriel, nous utiliserons les points de terminaison RPC Web3 pour interagir avec Moonbeam.

## Installez l'extension MetaMask

Tout d'abord, nous commençons par une nouvelle installation par défaut de [MetaMask](https://metamask.io/) à partir du Chrome Store. Après avoir téléchargé, installé et initialisé l'extension, suivez le guide "Premiers pas". Ici, vous devez créer un portefeuille, définir un mot de passe et stocker votre phrase de sauvegarde secrète (cela donne un accès direct à vos fonds, alors assurez-vous de les stocker dans un endroit sécurisé). Une fois terminé, nous importerons le compte de développement:

![Importer un compte dev a Metamask](/images/metamask/using-metamask-1.png)

Les détails des comptes de développement préfinancés pour ce nœud de développement sont les suivants:

--8<-- 'code/setting-up-node/dev-accounts.md'

--8<-- 'code/setting-up-node/dev-testing-account.md'

Sur l'écran d'importation, sélectionnez “clé privée” et collez l'une des clés répertoriées ci-dessus. Pour cet exemple, nous utiliserons la clé de Gerald :

![Collez votre clé de compte dans MetaMask](/images/metamask/using-metamask-2.png)

Vous devriez vous retrouver avec un “Compte 2”  importé qui ressemble à ceci:
![MetaMask affichant votre nouveau compte 2](/images/metamask/using-metamask-3.png)

## Connecter MetaMask à Moonbeam

MetaMask peut être configuré pour se connecter à votre nœud de développement local ou à Moonbase Alpha TestNet. 

Pour connecter MetaMask à Moonbeam, accédez à Paramètres -> Réseaux -> Ajouter un réseau. C'est ici que vous pouvez configurer le réseau auquel vous souhaitez que MetaMask se connecte, en utilisant les configurations réseau suivantes:

Nœud de développement Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

Moonbase Alpha TestNet:

--8<-- 'text/testnet/testnet-details.md'

Pour les besoins de ce tutoriel, connectons MetaMask à notre nœud de développement Moonbeam exécuté localement.

![Entrez vos nouvelles informations de réseau dans MetaMask](/images/metamask/using-metamask-4.png)

Lorsque vous cliquez sur "sauvegarder" et quittez l'écran des paramètres réseau, MetaMask doit être connecté au nœud de développement Moonbeam local via son RPC Web3, et vous devriez voir le compte de développement Moonbeam avec un solde de 1207925.8196 DEV.

![Votre nouveau compte Moonbeam avec un solde de 1207925.8196](/images/metamask/using-metamask-5.png)

## Initier un transfert

Essayons d'envoyer des jetons avec MetaMask.

Pour plus de simplicité, nous allons transférer de ce compte de développement à celui créé lors de la configuration de MetaMask. Cliquez sur "envoyer" pour lancer le transfert. Par conséquent, nous pouvons utiliser l'option “Transférer entre mes comptes” . Transférons 100 jetons et laissons tous les autres paramètres tels quels:

![Lancer un transfert de jeton](/images/metamask/using-metamask-6.png)

Une fois que vous avez soumis la transaction, vous la verrez “en attente” jusqu'à ce qu'elle soit confirmée, comme le montre l'image suivante:

![Confirmation de transaction](/images/metamask/using-metamask-7.png)

Notez que le solde du compte 2 a été diminué du montant envoyé + les frais d'essence. En retournant au compte 1, nous voyons que les 100 jetons envoyés sont arrivés:

![Nouveau solde dans le compte 1](/images/metamask/using-metamask-8.png)

Si vous revenez à votre terminal sur lequel votre nœud Moonbeam est en cours d'exécution, vous commencerez à voir des blocs créés à mesure que les transactions arriveront:

![Nœud de développement Moonbeam](/images/metamask/using-metamask-9.png)

!!! note
    Si vous finissez par réinitialiser votre nœud de développement à l'aide de la commande Substrate purge-chain, vous devrez réinitialiser votre compte genesis MetaMask en utilisant Paramètres -> Avancé -> Réinitialiser le compte. Cela effacera l'historique des transactions de vos comptes et réinitialisera le nonce. Assurez-vous de ne rien effacer de ce que vous souhaitez conserver!
 
