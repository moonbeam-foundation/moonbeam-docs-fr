---
title: Utiliser Remix
description: Apprenez à utiliser l'un des outils de développement Ethereum les plus populaires, l'IDE Remix, pour interagir avec un nœud Moonbeam local.
---

# Interagir avec Moonbeam à l'aide de Remix

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//RT_f1-ga_n4' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Vous pouvez trouver tout le code pertinent pour ce tutoriel sur <a href="{{ config.site_url }}resources/code-snippets/">code snippets page</a></div>

## Introduction

Remix est l'un des environnements de développement couramment utilisés pour les contrats intelligents sur Ethereum. Compte tenu des fonctionnalités de compatibilité Ethereum de Moonbeam, Remix peut être utilisé directement avec un nœud de développement Moonbeam ou le Moonbase Alpha TestNet.

Ce guide décrit le processus de création et de déploiement d'un contrat intelligent basé sur Solidity sur un nœud de développement Moonbeam à l'aide de l' [IDE Remix](https://remix.ethereum.org/). 

!!! remarque
   Ce tutoriel a été créé à l'aide du tag {{ networks.development.build_tag}} qui est basé sur la version {{ networks.moonbase.version }} de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.moonbase.version }}). La plate-forme Moonbeam et les composants [Frontier](https://github.com/paritytech/frontier) sur lesquels elle s'appuie pour la compatibilité Ethereum basée sur substrate sont toujours en cours de développement.

Ce guide suppose que vous disposez d'un nœud Moonbeam local en mode `--dev` et que vous disposez d'une installation de [MetaMask](https://metamask.io/)  configurée pour utiliser ce nœud local. Vous pouvez trouver des instructions pour exécuter un nœud Moonbeam en local [ici](/getting-started/local-node/setting-up-a-node/) et des instructions pour y connecter MetaMask [ici](/getting-started/local-node/using-metamask/).
## Vérification des prérequis

Si vous avez suivi les guides ci-dessus, vous devriez avoir un nœud local Moonbeam qui commencera à créer des blocs au fur et à mesure que les transactions arriveront:

![Noeud Moonbeam local produisant des blocs](/images/remix/using-remix-1.png)

Et vous devriez avoir une installation MetaMask connectée à votre nœud de développement Moonbeam avec au moins un compte qui a un solde. Cela devrait ressembler à quelque chose comme ceci (vue développée):

![Installation de MetaMask avec un solde](/images/remix/using-remix-2.png)

!!! remarque
    Assurez-vous que vous êtes connecté à votre nœud Moonbeam et non à un autre réseau !

## Premiers pas avec Remix

Maintenant, lançons Remix pour exercer des fonctionnalités plus avancées dans Moonbeam.

Lancez Remix en accédant à [https://remix.ethereum.org/](https://remix.ethereum.org/).  Dans l'écran principal, sous Environnements, sélectionnez Solidity pour configurer Remix pour le développement Solidity, puis accédez à la vue Explorateurs de fichiers:

![Explorateur de fichiers](/images/remix/using-remix-3.png)

Nous allons créer un nouveau fichier pour enregistrer le contrat intelligent Solidity. Appuyez sur le bouton + sous Explorateurs de fichiers et entrez le nom "MyToken.sol" dans la boîte de dialogue contextuelle

![Créer un nouveau dossier pour votre contrat Solidity](/images/remix/using-remix-4.png)

Ensuite, collons le contrat intelligent suivant dans l'onglet éditeur qui apparaît:

```solidity
--8<-- 'code/remix-local/contract.md'
```

Il s'agit d'un simple contrat ERC-20 basé sur le modèle actuel ERC-20 Open Zeppelin. Il crée MyToken avec le symbole MYTOK et frappe l'intégralité de la fourniture initiale au créateur du contrat.

Une fois que vous avez collé le contrat dans l'éditeur, il devrait ressembler à ceci:

![Coller le contrat dans l'éditeur](/images/remix/using-remix-5.png)

Maintenant, accédez à l'option de la barre latérale de compilation pour appuyer sur le bouton “Compile MyToken.sol”:

![Compile MyToken.sol](/images/remix/using-remix-6.png)

Vous verrez Remix télécharger toutes les dépendances d'Open Zeppelin et compiler le contrat.

## Déployer un contrat sur Moonbeam à l'aide de Remix

Nous pouvons maintenant déployer le contrat en accédant à l'option Déploiement de la barre latérale. Vous devez modifier la liste déroulante “Environnement” la plus élevée de “JavaScript VM” à “Injected Web3.” Cela indique à Remix d'utiliser le fournisseur injecté MetaMask, qui le dirigera vers votre nœud de développement Moonbeam. Si vous voulez essayer ceci en utilisant le Moonbase Alpha TestNet, vous devrez connecter MetaMask au TestNet au lieu de votre nœud de développement local.

Dès que vous sélectionnez "Injected Web3", vous serez invité à autoriser Remix à se connecter à votre compte MetaMask.

![Remplacer](/images/remix/using-remix-7.png)

Appuyez sur “suivant” dans Metamask pour permettre à Remix d'accéder au compte sélectionné.

De retour sur Remix, vous devriez voir que le compte que vous souhaitez utiliser pour le déploiement est désormais géré par MetaMask. À côté du bouton Déployer, spécifions un approvisionnement initial de 8 millions de jetons. Puisque ce contrat utilise la valeur par défaut de 18 décimales, la valeur à mettre dans la case est `8000000000000000000000000`.

Une fois que vous avez entré cette valeur, sélectionnez "Déployer."

![Saisir un solde de compte et déployer](/images/remix/using-remix-8.png)

7Vous serez invité dans MetaMask à confirmer la transaction de déploiement du contrat.

![Confirmez le message de transaction](/images/remix/using-remix-9.png)

!!! remarque
    Si vous rencontrez des problèmes pour déployer un contrat spécifique, vous pouvez essayer d'augmenter manuellement la limite de gaz. Vous pouvez le faire sous Paramètres -> Avancé -> Contrôles de gaz avancés = ON.

Après avoir appuyé sur Confirmer et que le déploiement est terminé, vous verrez la transaction répertoriée dans MetaMask. Le contrat apparaîtra sous Contrats déployés dans Remix.

![Étiquette confirmée sur une transaction](/images/remix/using-remix-10.png)

Une fois le contrat déployé, vous pouvez interagir avec lui depuis Remix.

Explorez le contrat sous “Contrats déployés.” Cliquez sur le nom, le symbole et totalSupply devrait renvoyer respectivement “MyToken,” “MYTOK,” and “8000000000000000000000000”. Si vous copiez l'adresse à partir de laquelle vous avez déployé le contrat et le collez dans le champ balanceOf, vous devriez voir l'intégralité du solde comme appartenant à cet utilisateur. Copiez l'adresse du contrat en cliquant sur le bouton à côté du nom et de l'adresse du contrat.

![Interagir avec le contrat depuis Remix](/images/remix/using-remix-11.png)

## Interagir avec un jeton ERC-20 basé sur Moonbeam depuis MetaMask

Maintenant, ouvrez MetaMask pour ajouter les jetons ERC-20 nouvellement déployés. Avant de le faire, assurez-vous d'avoir copié l'adresse du contrat à partir de Remix. De retour dans MetaMask, cliquez sur “Ajouter un Jeton” comme indiqué ci-dessous. Assurez-vous que vous êtes dans le compte qui a déployé le contrat de jeton.

![Ajouter un jeton](/images/remix/using-remix-12.png)

Collez l'adresse du contrat copiée dans le champ “Jeton personnalisé”. Les champs “symbole du jeton” and “Décimales de précision” doivent être automatiquement renseignés.

![Collez l'adresse de contrat copiée](/images/remix/using-remix-13.png)

Après avoir cliqué sur “Suivant,” vous devrez confirmer que vous souhaitez ajouter ces jetons à votre compte MetaMask. Cliquez sur “Ajouter un jeton” et vous devriez voir un solde de 8M MyTokens dans MetaMask:

![Ajoutez les jetons à votre compte MetaMask](/images/remix/using-remix-14.png)

Nous pouvons maintenant envoyer certains de ces jetons ERC-20 à l'autre compte que nous avons configuré dans MetaMask. Cliquez sur “envoyer” pour lancer le transfert de 500 MyTokens et sélectionnez le compte de destination.

Après avoir cliqué sur “suivant,” il vous sera demandé de confirmer (semblable à ce qui est illustré ci-dessous).

![Confirmation du transfert de jeton](/images/remix/using-remix-15.png)

Cliquez sur “Confirmer” et, une fois la transaction terminée, vous verrez une confirmation et une réduction du solde du compte MyToken du compte de l'expéditeur dans MetaMask:

![Vérifier la réduction du solde du compte](/images/remix/using-remix-16.png)

Si vous possédez le compte auquel vous avez envoyé les jetons, vous pouvez ajouter l'actif de jeton pour vérifier que le transfert est arrivé.

