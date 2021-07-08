---
title: Utiliser Truffle
description: Moonbeam facilite incroyablement le déploiement d'un contrat intelligent basé sur Solidity sur un nœud Moonbeam en utilisant Truffle. Apprenez comment dans ce tutoriel.
---

# Interagir avec Moonbeam en utilisant Truffle

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//RD5MefSPNeo' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>You can find all of the relevant code for this tutorial on the <a href="{{ config.site_url }}resources/code-snippets/">code snippets page</a></div>

## Introduction

Ce guide décrit le processus de déploiement d'un contrat intelligent basé sur Solidity sur un nœud Moonbeam à l'aide de [Truffle](https://www.trufflesuite.com/), un outil de développement couramment utilisé pour les contrats intelligents sur Ethereum. Compte tenu des fonctionnalités de compatibilité Ethereum de Moonbeam, Truffle peut être utilisé directement avec un nœud Moonbeam.

!!! remarque
    Ce tutoriel a été créé à l'aide du tag {{ networks.development.build_tag}} qui est basé sur la version {{ networks.moonbase.version }} de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.moonbase.version }}).  La plate-forme Moonbeam et les composants [Frontier](https://github.com/paritytech/frontier)  sur lesquels elle s'appuie pour la compatibilité Ethereum basée sur les substrats sont toujours en cours de développement.
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Pour ce guide, vous aurez besoin d'un nœud de développement Moonbeam fonctionnant en mode `--dev`. Cela peut être fait en suivant les étapes détaillées [ici](/getting-started/local-node/setting-up-a-node/) ou en utilisant le [plugin Truffle de Moonbeam](/integrations/trufflebox/#the-moonbeam-truffle-plugin), que nous utiliserons dans les exemples de ce tutoriel.

## Vérification des prérequis

--8<-- 'text/common/install-nodejs.md'


De plus, vous pouvez installer globalement Truffle en exécutant :

```
npm install -g truffle
```

A la date de publication de ce guide, les versions utilisées étaient respectivement 15.12.0, 7.6.3, et 5.2.4 .

!!! remarque
    Pour les exemples suivants, vous n'avez pas besoin d'installer Truffle, car il est inclus en tant que dépendance de la box Moonbeam Truffle. Si vous préférez, vous pouvez exécuter  `npx truffle` ou `./node_modules/.bin/truffle` à la place de `truffle`.

## Premiers pas avec Truffle

Pour faciliter le processus de démarrage avec Truffle, nous avons [sorti la box Moonbeam Truffle](https://moonbeam.network/announcements/moonbeam-truffle-box-available-solidity-developers/). Cela fournit une configuration standard pour accélérer le processus de déploiement des contrats sur Moonbeam. Pour en savoir plus sur la box, vous pouvez visiter [ce lien](/integrations/trufflebox/).

Pour télécharger la box Moonbeam Truffle, suivez [ces instructions](/integrations/trufflebox/#downloading-and-setting-up-the-truffle-box). Une fois dans le répertoire, jetons un œil au fichier `truffle-config.js` (pour les besoins de ce guide, certaines informations ont été supprimées):

```js
const HDWalletProvider = require('@truffle/hdwallet-provider');
// Moonbeam Development Node Private Key
const privateKeyDev =
   '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
//...
module.exports = {
   networks: {
      dev: {
         provider: () => {
            ...
            return new HDWalletProvider(privateKeyDev, 'http://localhost:9933/')
         },
         network_id: 1281,
      },
      //...
   },
   plugins: ['moonbeam-truffle-plugin']
};
```

Notez que nous utilisons `HD-Wallet-Provider` de Truffle comme portefeuille hiérarchique déterministe. En outre, nous avons défini un réseau `dev` qui pointe vers l'URL du fournisseur du nœud, et la clé privée du compte de développement, qui détient tous les fonds dans le nœud de développement, y est incluse.

## Exécution d'un nœud de développement

Pour configurer un nœud de développement Moonbeam, vous pouvez suivre [ce tutoriel](/getting-started/local-node/setting-up-a-node/). Le processus prend environ 40 minutes au total et vous devez installer Substrate et toutes ses dépendances. Le plugin Moonbeam Truffle fournit un moyen de démarrer beaucoup plus rapidement avec un nœud de développement, et la seule exigence est d'avoir Docker installé (au moment de l'écriture, la version Docker utilisée était 19.03.6).

Pour démarrer un nœud de développement Moonbeam dans votre environnement local, nous devons d'abord télécharger l'image Docker correspondante:

```
truffle run moonbeam install
```

![Téléchargement de l'images Docker](/images/truffle/using-truffle-1.png)

Une fois téléchargé, nous pouvons procéder au démarrage du nœud local avec la commande suivante:

```
truffle run moonbeam start
```

Vous verrez un message indiquant que le nœud a démarré, suivi des deux points de terminaison disponibles.

![Le nœud local Moonbeam a démarré](/images/truffle/using-truffle-2.png)

Une fois que vous avez fini d'utiliser votre nœud de développement Moonbeam, vous pouvez exécuter les lignes suivantes pour l'arrêter et supprimer l'image Docker si tel est le cas:

```
truffle run moonbeam stop && \
truffle run moonbeam remove
```

![Nœud local Moonbeam arrêté et image supprimée](/images/truffle/using-truffle-3.png)

## Le fichier du contrat

Il existe également un contrat de jeton ERC-20 inclus avec la box Truffle:

```solidity
pragma solidity ^0.7.5;

// Import OpenZeppelin Contract
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

// This ERC-20 contract mints the specified amount of tokens to the contract creator.
contract MyToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("MyToken", "MYTOK")
    {
        _mint(msg.sender, initialSupply);
    }
}
```

Il s'agit d'un simple contrat ERC-20 basé sur le contrat OpenZepplin. Il crée "MyToken" avec le symbole "MYTOK" et les 18 décimales standard. En outre, il attribue la fourniture initiale de jetons créée au créateur du contrat.

Si nous examinons le script de migration de contrat Truffle ci-dessous  `migrations/2_deploy_contracts.js`,  il contient les éléments suivants:

```javascript
var MyToken = artifacts.require('MyToken');

module.exports = function (deployer) {
   deployer.deploy(MyToken, '8000000000000000000000000');
};
```

"8000000000000000000000000" est le nombre de jetons à frapper initialement avec le contrat, soit 8 millions avec 18 décimales.

## Déployer un contrat sur Moonbeam avec Truffle

Avant de pouvoir déployer nos contrats, nous devons les compiler. (Nous disons "contrats" car les déploiements Truffle normaux incluent le contrat `Migrations.sol`.) Vous pouvez le faire avec la commande suivante:

```
truffle compile
```

En cas de succès, vous devriez voir une sortie comme celle-ci:

![Message Truffle de réussite de la compilation ](/images/truffle/using-truffle-4.png)

Nous sommes maintenant prêts à déployer les contrats compilés. Vous pouvez le faire avec la commande suivante:

```
truffle migrate --network dev
```

En cas de succès, vous verrez les actions de déploiement, y compris l'adresse du contrat déployé:

![Actions de déploiement de contrat réussies](/images/truffle/using-truffle-5.png)

Une fois que vous avez suivi le [Guide MetaMask](/getting-started/local-node/using-metamask/) et le [Guide Remix](/getting-started/local-node/using-remix/), vous pourrez prendre l'adresse du contrat déployé qui est retournée et la charger dans MetaMask ou Remix.

