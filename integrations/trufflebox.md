---
title: Truffle Box Moonbeam
description: Commencez à utiliser la Truffle Box Moonbeam  pour un moyen rapide et préconfiguré de déployer vos contrats intelligents Solidity sur Moonbeam à l'aide de Truffle.
---
# La Truffle Box Moonbeam

![Intro diagram](/images/builders/tools/truffle-banner.png)

## Introduction {: #introduction }
Dans le cadre d'un effort continu pour aider les développeurs qui souhaitent commencer à travailler sur Moonbeam, nous avons [lancé la Truffle Box Moonbeam](https://moonbeam.network/announcements/moonbeam-truffle-box-available-solidity-developers/). Avec elle, les développeurs trouveront une configuration standard pour commencer à déployer rapidement des contrats intelligents sur Moonbeam. Avec la Truffle Box Moonbeam,, nous avons également incorporé le plugin Moonbeam Truffle, qui introduit quelques commandes pour exécuter un nœud de développement dans votre environnement local en tant qu'image Docker. Cela supprime le processus de configuration d'un nœud local (qui peut prendre jusqu'à 40 minutes lors de la construction de son binaire) et constitue une solution rapide et facile pour commencer à développer dans votre environnement local.

Ce tutoriel vous guidera tout au long du processus de configuration de la box, à l'aide du plugin Moonbeam Truffle et au déploiement de contrats à la fois sur un nœud de développement Moonbeam et sur Moonbase Alpha en utilisant Truffle avec la configuration de base.

!!! remarque
    Ce guide est basé sur une installation Ubuntu 18.04. Au moment de la rédaction de cet article, les versions Node.js et npm utilisées étaient respectivement 15.2.1 et 7.0.8. Les versions Node.js supérieures à 10.23.0 sont requises. Nous avons également remarqué une erreur lors de l'installation des packages avec la version 7.0.15 de npm. Vous pouvez rétrograder npm en exécutant `npm install -g npm@version`, en définissant la version sur celle souhaitée.

## Vérification des prérequis {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

Au moment de la rédaction de ce guide, les versions utilisées étaient respectivement 15.2.1 et 7.0.8. Ensuite, nous pouvons éventuellement installer Truffle globalement. Pour ce faire, vous pouvez exécuter :


```
npm install -g truffle
```

Au moment de la rédaction de ce guide, la version utilisée était la 5.1.51. 

## Téléchargement et configuration de Truffle Box {: #downloading-and-setting-up-the-truffle-box } 

Pour commencer avec la Truffle Box Moonbeam, si vous avez installé Truffle en global, vous pouvez exécuter:

```
mkdir moonbeam-truffle-box && cd moonbeam-truffle-box
truffle unbox PureStake/moonbeam-truffle-box
```

![Unbox Moonbeam Truffle box](/images/builders/interact/truffle/truffle-1.png)

Néanmoins, la Box a également Truffle comme dépendance au cas où vous ne voudriez pas l'installer en global. Dans ce cas, vous pouvez directement cloner le référentiel suivant:

```
git clone https://github.com/PureStake/moonbeam-truffle-box
cd moonbeam-truffle-box
``` 

Avec les fichiers de votre système local, l'étape suivante consiste à installer toutes les dépendances en exécutant:

```
npm install
```

!!! remarque
    Nous avons remarqué une erreur lors de l'installation des packages avec la version 7.0.15 de npm. Vous pouvez rétrograder npm en exécutant `npm install -g npm@version` et en définissant la version sur celle souhaitée. Par exemple, 7.0.8 ou 6.14.9.

Cela met fin à toutes les conditions préalables dont vous avez besoin pour utiliser la Truffle box Moonbeam.

## Fonctionnalités de base {: #basic-functionalities } 

La Box est préconfigurée avec deux réseaux `dev` (pour un nœud de développement) et `moonbase` (TestNet Moonbeam). Un contrat de jeton ERC20 et un simple script de test sont également inclus, à titre d'exemple. Le compilateur Solidity défini par défaut est `^0.7.0`, mais cela peut être modifié si nécessaire. Si vous êtes expérimenté avec Truffle, cette configuration vous semblera familière.

```js
const HDWalletProvider = require('@truffle/hdwallet-provider');
// Moonbeam Development Node Private Key
const privateKeyDev =
   '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
// Moonbase Alpha Private Key --> Please change this to your own Private Key with funds
// NOTE: Do not store your private key in plaintext files
//       this is only for demostration purposes only
const privateKeyMoonbase =
   'YOUR_PRIVATE_KEY_HERE_ONLY_FOR_DEMOSTRATION_PURPOSES';

module.exports = {
   networks: {
      // Standalode Network
      dev: {
         provider: () => {
            //...
            return new HDWalletProvider(
               privateKeyDev,
               'http://localhost:9933/'
            );
         },
         network_id: 1281,
      },
      // Moonbase Alpha TestNet
      moonbase: {
         provider: () => {
            //...
            return new HDWalletProvider(
               privateKeyMoonbase,
               'https://rpc.testnet.moonbeam.network'
            );
         },
         network_id: 1287,
      },
   },
   // Solidity 0.7.0 Compiler
   compilers: {
      solc: {
         version: '^0.7.0',
      },
   },
   // Moonbeam Truffle Plugin
   plugins: ['moonbeam-truffle-plugin'],
};
```

Le fichier `truffle-config.js` comprend également la clé privée du compte genesis pour le nœud de développement. L'adresse associée à cette clé contient tous les jetons de cet environnement de développement. Pour les déploiements dans Moonbase Alpha TestNet, vous devez fournir la clé privée d'une adresse qui détient des fonds. Pour ce faire, vous pouvez créer un compte dans MetaMask, le financer à l'aide du [Faucet du TestNet](https://docs.moonbeam.network/getting-started/testnet/faucet/), et exporter sa clé privée.

Comme pour l'utilisation de Truffle dans n'importe quel réseau Ethereum, vous pouvez exécuter les commandes normales pour compiler, tester et déployer des contrats intelligents dans Moonbeam. Par exemple, vous pouvez essayer les commandes suivantes à l'aide du contrat de jeton ERC20 inclus

```
truffle compile # compiles the contract
truffle test #run the tests included in the test folder
truffle migrate --network network_name  #deploys to the specified network
```

En fonction du réseau sur lequel vous souhaitez déployer les contrats, vous devez remplacer nom_réseau par dev (pour cibler le nœud de développement) ou moonbase (pour cibler le TestNet).

!!! remarque
    Si vous n'avez pas installé Truffle en global, vous pouvez utiliser `npx truffle` ou `./node_modules/.bin/truffle` a la place de `truffle` .

## Le plugin Truffle Moonbeam {: #the-moonbeam-truffle-plugin } 

Pour configurer un nœud de développement Moonbeam, vous pouvez actuellement suivre [ce tutoriel](/getting-started/local-node/setting-up-a-node/). Le processus prend environ 40 minutes au total et vous devez installer Substrate et toutes ses dépendances. Le plugin Truffle Moonbeam  fournit un moyen de démarrer beaucoup plus rapidement avec un nœud de développement, et la seule exigence est d'avoir Docker installé (au moment de l'écriture, la version Docker utilisée était 19.03.6). Pour plus d'informations sur l'installation de Docker, veuillez visiter [cette page](https://docs.docker.com/get-docker/). Pour télécharger l'image Docker, exécutez la ligne suivante:

```
truffle run moonbeam install
``` 

![Install Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-01.png)

 
Ensuite, vous disposez d'un ensemble de commandes disponibles pour contrôler le nœud inclus dans l'image Docker:
 
```
truffle run moonbeam start
truffle run moonbeam status
truffle run moonbeam pause
truffle run moonbeam unpause
truffle run moonbeam stop
truffle run moonbeam remove
```

Chacune des commandes ci-dessus effectue l'action suivante:

-  Start: démarre un nœud de développement Moonbeam. Cela fournit deux points de terminaison RPC: 
      - HTTP: `http://127.0.0.1:9933` 
      - WS: `ws://127.0.0.1:9944`
-  Status: indique à l'utilisateur si un nœud de développement Moonbeam est en cours d'exécution
-  Pause: interrompt le nœud de développement s'il est en cours d'exécution
-  Unpause: Rétablit le nœud de développement s'il est en pause
-  Stop: arrête le nœud de développement s'il est en cours d'exécution. Cela supprime également le conteneur Docker
-  Remove: supprime l'image Docker purestake / moonbase

Vous pouvez voir la sortie de ces commandes dans l'image suivante:

![Install Moonbeam Truffle box](/images/builders/interact/truffle/truffle-5.png)

Si vous connaissez Docker, vous pouvez ignorer les commandes du plugin et interagir directement avec l'image Docker.

## Test de la Truffle Box Moonbeam {: #testing-the-moonbeam-truffle-box } 

La box a la configuration minimale requise pour vous aider à démarrer. Compilons d'abord les contrats en exécutant :

```
truffle compile
``` 
![Compile Contracts](/images/legacy/trufflebox/trufflebox-03.png)

N'oubliez pas que si Truffle est installé en global, vous pouvez ignorer la partie ./node_modules/.bin/ dans les commandes. Une fois le contrat compilé, nous pouvons exécuter le test de base inclus dans la Box (notez que Ganache est utilisé pour ces tests et non le nœud de développement Moonbeam):

```
truffle test
```

![Test Contract Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-04.png)

Après avoir exécuté la commande d'installation du plugin, qui télécharge l'image Docker du nœud de développement Moonbeam, démarrons le nœud local et déployons le contrat de jeton dans notre environnement local:

```
truffle run moonbeam start
truffle migrate --network dev
```

![Deploy on Dev Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-05.png)

Enfin, nous pouvons déployer notre contrat de jeton sur Moonbase Alpha, mais d'abord, assurez-vous de définir une clé privée avec des fonds dans le fichier truffle-config.js. Une fois la clé privée définie, nous pouvons exécuter la commande migrate pointant vers le TestNet.

```
truffle migrate --network moonbase
```

![Deploy on Moonbase Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-06.png)

Et voilà, vous avez utilisé la Truffle Box Moonbeam pour déployer un simple contrat de jeton ERC20 dans votre nœud de développement Moonbeam et Moonbase Alpha.
 
