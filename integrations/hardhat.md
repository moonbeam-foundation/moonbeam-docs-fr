---
title: Hardhat
description: Utilisez Hardhat pour compiler, déployer et déboguer les contrats intelligents Ethereum sur Moonbeam.
---

# Construire avec Hardhat sur Moonbeam

![Hardhat Create Project](/images/hardhat/hardhat-banner.png)

## Introduction {: #introduction } 

Hardhat est un environnement de développement Ethereum qui aide les développeurs à gérer et à automatiser les tâches récurrentes inhérentes à la création de contrats intelligents et de DApps. Hardhat peut interagir directement avec l'API Ethereum de Moonbeam afin qu'il puisse également être utilisé pour déployer des contrats intelligents dans Moonbeam.

Ce guide expliquera comment utiliser Hardhat pour compiler, déployer et déboguer des contrats intelligents Ethereum sur le TestNet Moonbase Alpha .

## Vérification des prérequis {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

Au moment de la rédaction de ce guide, les versions utilisées étaient respectivement 15.7.0 et 7.4.3.

De plus, vous aurez besoin des éléments suivants:

 - Avoir MetaMask installé et [connecté à Moonbase](/getting-started/testnet/metamask/)
 - Avoir un compte avec des fonds, que vous pouvez obtenir auprès de [Mission Control](/getting-started/testnet/faucet/)

Une fois que toutes les exigences ont été remplies, vous êtes prêt à construire avec Hardhat.

## Démarrer un projet Hardhat {: #starting-a-hardhat-project } 

Pour démarrer un nouveau projet, créez un répertoire pour celui-ci:

```
mkdir hardhat && cd hardhat
```

Ensuite, initialisez le projet en exécutant:

```
npm init -y
```

Vous remarquerez un nouvellement créé `package.json`, qui continuera de croître à mesure que vous installez les dépendances de projet.

Pour commencer avec Hardhat, nous allons l'installer dans notre répertoire de projet nouvellement créé:

```
npm install hardhat
```

Une fois installé, exécutez:

```
npx hardhat
```

Cela créera un fichier de configuration Hardhat (`hardhat.config.js`) dans notre répertoire de projet.

!!! remarque
    `npx` est utilisé pour lancer des exécutables installés localement dans votre projet. Bien que Hardhat puisse être installé globalement, nous vous recommandons de l'installer localement dans chaque projet afin que vous puissiez contrôler la version projet par projet.

Après avoir exécuté la commande, choisissez `Create an empty hardhat.config.js`:

![Hardhat Create Project](/images/hardhat/hardhat-images-1.png)

## Le repertoire contrat {: #the-contract-file } 

Nous allons stocker notre contrat dans le repertoire `contracts`. Créez-le:

```
mkdir contracts && cd contracts
```

Le contrat intelligent que nous allons déployer à titre d'exemple s'appellera Box: il permettra aux gens de stocker une valeur qui pourra être récupérée ultérieurement.

Nous enregistrerons ce fichier sous `contracts/Box.sol`:

```solidity
// contracts/Box.sol
pragma solidity ^0.8.1;

contract Box {
    uint256 private value;

    // Emitted when the stored value changes
    event ValueChanged(uint256 newValue);

    // Stores a new value in the contract
    function store(uint256 newValue) public {
        value = newValue;
        emit ValueChanged(newValue);
    }

    // Reads the last stored value
    function retrieve() public view returns (uint256) {
        return value;
    }
}
```

## Fichier de configuration Hardhat {: #hardhat-configuration-file } 

Modifions notre fichier de configuration Hardhat afin que nous puissions compiler et déployer ce contrat sur Moonbase Alpha.

Si vous ne l'avez pas encore fait, créez un compte MetaMask, [connectez-vous à Moonbase Alpha](/getting-started/testnet/metamask/), et financez-le via [Mission Control](/getting-started/testnet/faucet/). Nous utiliserons la clé privée du compte créé pour déployer le contrat.

Nous commençons par exiger le [plugin ethers](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html), qui apporte la bibliothèque [ethers.js][/integrations/ethers/] qui vous permet d'interagir avec la blockchain de manière simple. Nous pouvons installer le plugin `ethers` plugin en exécutant:

```
npm install @nomiclabs/hardhat-ethers ethers
```

Ensuite, nous importons la clé privée que nous avons récupérée de MetaMask et la stockons dans un fichier `.json` .

!!! remarque
    Veuillez toujours gérer vos clés privées avec un gestionnaire de secrets désigné ou un service similaire. Ne sauvegardez ni ne validez jamais vos clés privées dans vos repositories.

Dans le `module.exports`, nous devons fournir la version Solidity (`0.8.1` selon notre repertoire de contrat), et les détails du réseau:

 - Network name: `moonbase`
 - URL: `{{ networks.moonbase.rpc_url }}`
 - ChainID: `{{ networks.moonbase.chain_id }}`

Si vous souhaitez effectuer un déploiement sur un nœud de développement Moonbeam local, vous pouvez utiliser les détails de réseau suivants:

 - Network name: `dev`
 - URL: `{{ networks.development.rpc_url }}`
 - ChainID: `{{ networks.development.chain_id }}`

Le fichier de configuration Hardhat doit ressembler à ceci:

```js
// ethers plugin required to interact with the contract
require('@nomiclabs/hardhat-ethers');

// private key from the pre-funded Moonbase Alpha testing account
const { privateKey } = require('./secrets.json');

module.exports = {
  // latest Solidity version
  solidity: "0.8.1",

  networks: {
    // Moonbase Alpha network specification
    moonbase: {
      url: `{{ networks.moonbase.rpc_url }}`,
      chainId: {{ networks.moonbase.chain_id }},
      accounts: [privateKey]
    }
  }
};
```

Ensuite, créons un `secrets.json`, où la clé privée mentionnée précédemment est stockée. Assurez-vous d'ajouter le fichier à votre projet `.gitignore`, et de ne jamais révéler votre clé privée. Le fichier `secrets.json` doit contenir une entrée `privateKey` entry, par exemple:

```js
{
    "privateKey": "YOUR-PRIVATE-KEY-HERE"
}
```

Toutes nos félicitations! Nous sommes prêts pour le déploiement!

## Compiler Solidity {: #compiling-solidity } 

Notre contrat, `Box.sol`, utilise Solidity 0.8.1. Assurez-vous que le fichier de configuration Hardhat est correctement configuré avec cette version de solidity. Si tel est le cas, nous pouvons compiler le contrat en exécutant:

```
npx hardhat compile
```

![Hardhat Contract Compile](/images/hardhat/hardhat-images-2.png)

Après compilation, un répertoire `artifacts` est créé: il contient le bytecode et les métadonnées du contrat, qui sont des fichiers `.json` . C'est une bonne idée d'ajouter ce répertoire à votre fichier `.gitignore`.

## Déployer le contrat {: #deploying-the-contract } 

Afin de déployer le contrat intelligent Box, nous devrons rédiger un simple `deployment script`. Commençons par créer un nouveau répertoire (`scripts`). Dans le répertoire nouvellement créé, ajoutez un nouveau fichier `deploy.js`.

```
mkdir scripts && cd scripts
touch deploy.js
```

Ensuite, nous devons écrire notre script de déploiement en utilisant `ethers`. Parce que nous allons l'exécuter avec Hardhat, nous n'avons pas besoin d'importer de bibliothèques. Le script est une version simplifiée de celui utilisé dans ce [ce tutoriel](/getting-started/local-node/deploy-contract/#deploying-the-contract).

Nous commençons par créer une instance locale du contrat avec la méthode `getContractFactory()` . Ensuite, utilisons la méthode `deploy()` qui existe dans cette instance pour lancer le contrat intelligent. Enfin, nous attendons son déploiement en utilisant `deployed()`. Une fois déployé, nous pouvons récupérer l'adresse du contrat à l'intérieur de l'instantiation de box .

```js
// scripts/deploy.js
async function main() {
   // We get the contract to deploy
   const Box = await ethers.getContractFactory('Box');
   console.log('Deploying Box...');

   // Instantiating a new Box smart contract
   const box = await Box.deploy();

   // Waiting for the deployment to resolve
   await box.deployed();
   console.log('Box deployed to:', box.address);
}

main()
   .then(() => process.exit(0))
   .catch((error) => {
      console.error(error);
      process.exit(1);
   });
```

En utilisant la commande `run`, nous pouvons maintenant déployer le contrat `Box` vers `Moonbase Alpha`:

```
  npx hardhat run --network moonbase scripts/deploy.js
```

!!! remarque
    Pour déployer sur un nœud de développement Moonbeam, remplacez `moonbase` par `dev` dans la commande `run` .

Après quelques secondes, le contrat est déployé et vous devriez voir l'adresse dans le terminal.

![Hardhat Contract Deploy](/images/hardhat/hardhat-images-3.png)

Félicitations, votre contrat est en ligne! Enregistrez l'adresse, car nous l'utiliserons pour interagir avec cette instance de contrat à l'étape suivante.

## Interagir avec le contrat {: #interacting-with-the-contract } 

Utilisons Hardhat pour interagir avec notre nouveau contrat déployé dans Moonbase Alpha. Pour ce faire, lancez `hardhat console` en exécutant:

```
npx hardhat console --network moonbase
```

!!! remarque
    Pour déployer sur un nœud de développement Moonbeam, remplacez `moonbase` par `dev` dans la commande  `console` .

Ensuite, ajoutez les lignes de code suivantes une ligne à la fois. Tout d'abord, nous créons à nouveau une instance locale du contrat `Box.sol`. Ne vous inquiétez pas de la sortie `undefined` que vous obtiendrez après l'exécution de chaque ligne:

```js
const Box = await ethers.getContractFactory('Box');
```

Ensuite, connectons cette instance à une instance existante en passant l'adresse que nous avons obtenue lors du déploiement du contrat:

```js
const box = await Box.attach('0x425668350bD782D80D457d5F9bc7782A24B8c2ef');
```

Après l'attachement au contrat, nous sommes prêts à interagir avec lui. Alors que la console est toujours en session, appelons la méthode `store` et stockons une valeur simple:

```
await box.store(5)
```

La transaction sera signée par votre compte Moonbase et diffusée sur le réseau. La sortie doit ressembler à:

![Transaction output](/images/hardhat/hardhat-images-4.png)

Notez votre adresse intitulée `from`, l'adresse du contrat et les `data` qui sont transmises. Maintenant, récupérons la valeur en exécutant:

```
(await box.retrieve()).toNumber()
```

Nous devrions voir `5` ou la valeur que vous avez stockée initialement.

Félicitations, vous avez terminé le tutoriel sur Hardhat! 🤯 🎉

Pour plus d'informations sur Hardhat, les plugins hardhat et d'autres fonctionnalités intéressantes, veuillez visiter [hardhat.org](https://hardhat.org/).
