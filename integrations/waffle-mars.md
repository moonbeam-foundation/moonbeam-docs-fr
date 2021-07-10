---
title: Waffle & Mars
description: Apprenez à utiliser Waffle et Mars pour écrire, compiler, tester et déployer des contrats intelligents Ethereum sur Moonbeam.
---

# Utiliser Waffle & Mars sur Moonbeam

![Waffle et Mars sur Moonbeam](/images/waffle-mars/waffle-mars-banner.png)

## Introduction

[Waffle](https://getwaffle.io/) est une bibliothèque pour compiler et tester des contrats intelligents, et [Mars](https://github.com/EthWorks/Mars) est un gestionnaire de déploiement. Ensemble, Waffle et Mars peuvent être utilisés pour écrire, compiler, tester et déployer des contrats intelligents Ethereum. Étant donné que Moonbeam est compatible avec Ethereum, Waffle et Mars peuvent être utilisés pour déployer des contrats intelligents sur un nœud de développement Moonbeam ou sur Moonbase Alpha TestNet.

Waffle utilise des dépendances minimales, a une syntaxe facile à apprendre et à étendre, et fournit des temps d'exécution rapides lors de la compilation et du test de contrats intelligents. De plus, il est compatible [TypeScript](https://www.typescriptlang.org/) et utilise [Chai matchers](https://ethereum-waffle.readthedocs.io/en/latest/matchers.html) pour faire des tests facile à lire et à écrire.

Mars fournit un cadre simple et compatible TypeScript pour créer des scripts de déploiement avancés et rester en phase avec les changements d'état. Mars se concentre sur l'infrastructure en tant que code, permettant aux développeurs de spécifier comment leurs contrats intelligents doivent être déployés, puis d'utiliser ces spécifications pour gérer automatiquement les changements d'état et les déploiements.

Dans ce guide, vous allez créer un projet TypeScript pour écrire, compiler et tester un contrat intelligent à l'aide de Waffle, puis le déployer sur Moonbase Alpha TestNet à l'aide de Mars.

## Vérification des prérequis

--8<-- 'text/common/install-nodejs.md'

Au moment de la rédaction de ce guide, les versions utilisées étaient respectivement 15.12.0 et 7.6.3.

Waffle et Mars peuvent être utilisés avec un nœud de développement Moonbeam exécuté localement, mais pour les besoins de ce guide, vous allez déployer sur Moonbase Alpha. Par conséquent, vous aurez besoin d'un compte financé pour le développement. Vous pouvez choisir de [créer un compte avec MetaMask](/getting-started/testnet/metamask/#creating-a-wallet) ou [créer un compte avec PolkadotJS Apps](/integrations/wallets/polkadotjs/#creating-or- importer-un-compte-h160). 

Une fois que vous avez créé un compte, vous devez exporter la clé privée à utiliser dans ce guide. Avant de continuer, assurez-vous que votre compte dispose de fonds et, si nécessaire, obtenez des jetons « DEV » du [faucet](/getting-started/testnet/faucet/).

## Créer un projet TypeScript avec Waffle & Mars

Pour commencer, vous allez créer un projet TypeScript et installer et configurer quelques dépendances.

1. Créez le répertoire du projet et accédez-y :
```
mkdir gaufres-mars && cd gaufres-mars
```

2. Initialisez le projet. Ce qui créera un `package.json` dans le répertoire :
```
npm init -y
```

3. Installez les dépendances suivantes :
```
npm install ethereum-waffle ethereum-mars ethers \
@openzeppelin/contracts typescript ts-node chai \
@types/chai mocha @types/mocha
```
    - [Waffle](https://github.com/EthWorks/Waffle) - for writing, compiling, and testing smart contracts
    - [Mars](https://github.com/EthWorks/Mars) - for deploying smart contracts to Moonbeam
    - [Ethers](https://github.com/ethers-io/ethers.js/) - for interacting with Moonbeam's Ethereum API
    - [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts) - the contract you'll be creating will use OpenZeppelin's ERC20 base implementation
    - [TypeScript](https://github.com/microsoft/TypeScript) - the project will be a TypeScript project
    - [TS Node](https://github.com/TypeStrong/ts-node) - for executing the deployment script you'll create later in this guide
    - [Chai](https://github.com/chaijs/chai) - an assertion library used alongside Waffle for writing tests
    - [@types/chai](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/chai) - contains the type definitions for chai
    - [Mocha](https://github.com/mochajs/mocha) - a testing framework for writing tests alongside Waffle
    - [@types/mocha](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/mocha) - contains the type definitions for mocha

4. Créer un fichier de [configuration TypeScript](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html):
```
touch tsconfig.json
```

5. Ajouter une configuration TypeScript de base:
```
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2019",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "module": "CommonJS",
    "composite": true,
    "sourceMap": true,
    "declaration": true,
    "noEmit": true
  }
}
```

Maintenant, vous devriez avoir un projet TypeScript de base avec les dépendances nécessaires pour commencer à créer avec Waffle et Mars.
## Ajouter un contrat

Pour ce guide, vous allez créer un contrat ERC-20 qui émet un nombre spécifié de jetons au créateur du contrat. Il est basé sur le modèle ERC-20 de Open Zeppelin.

1. Créez un répertoire pour stocker vos contrats et un fichier pour le contrat intelligent :
```
contrats mkdir && contrats cd && toucher MyToken.sol
```

2. Ajoutez le contrat suivant à MyToken.sol :
```
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MYTOK") {}

    function initialize(uint initialSupply) public {
      _mint(msg.sender, initialSupply);
    }
}
```

Dans ce contrat, vous créez un token ERC20 appelé MyToken avec le symbole MYTOK, qui vous permet, en tant que créateur du contrat, de minter autant de MYTOK que vous le souhaitez.

## Utilisez Waffle pour compiler et tester

### Compiler avec Waffle

Maintenant que vous avez écrit un contrat intelligent, la prochaine étape consiste à utiliser Waffle pour le compiler. Avant de vous lancer dans la compilation de votre contrat, vous devrez configurer Waffle.

1. Retournez dans le répertoire racine du projet et créez un fichier `waffle.json` pour configurer Waffle :
```
cd .. && touch waffle.json
```

2. Modifiez le `waffle.json` pour spécifier les configurations du compilateur, le répertoire contenant vos contrats, etc. Pour cet exemple, nous utiliserons `solcjs` et la version Solidity que vous avez utilisée pour le contrat, qui est `0.8.0` :
```json
{
  "compilerType": "solcjs", // Specifies compiler to use
  "compilerVersion": "0.8.0", // Specifies version of the compiler
  "compilerOptions": {
    "optimizer": { // Optional optimizer settings
      "enabled": true, // Enable optimizer
      "runs": 20000 // Optimize how many times you want to run the code
    }
  },
  "sourceDirectory": "./contracts", // Path to directory containing smart contracts
  "outputDirectory": "./build", // Path to directory where Waffle saves compiler output
  "typechainEnabled": true // Enable typed artifact generation
}
```

3. Ajouter un script pour executer Waffle dans `package.json`:
```json
"scripts": {
  "build": "waffle"
},
```

C'est tout ce que vous devez faire pour configurer Waffle, maintenant vous êtes prêt à compiler le contrat `MyToken` en utilisant le script `build` :

```
npm run build
```

![Sortie du compilateur Waffle](/images/waffle-mars/waffle-mars-1.png)

Après avoir compilé vos contrats, Waffle stocke la sortie JSON dans le répertoire `build`. Le contrat de ce guide est basé sur le modèle ERC-20 d'Open Zeppelin, de sorte que les fichiers JSON ERC-20 pertinents apparaîtront également dans le répertoire `build`.

### Test avec Waffle

Avant de déployer votre contrat et de l'envoyer dans la nature, vous devez d'abord le tester. Waffle fournit un cadre de test avancé et dispose de nombreux outils pour vous aider à tester.

Vous exécuterez des tests sur Moonbase Alpha TestNet et aurez besoin de l'URL RPC correspondante pour vous y connecter : `https://rpc.testnet.moonbeam.network`. Étant donné que vous allez exécuter des tests sur TestNet, cela peut prendre quelques minutes pour exécuter tous les tests. Si vous souhaitez une expérience de test plus efficace, vous pouvez [créer un nœud de développement Moonbeam](/getting-started/local-node/setting-up-a-node/) en utilisant [`instant seal`](/getting-started /local-node/setting-up-a-node/#node-options). L'exécution d'un nœud de développement local Moonbeam avec la fonctionnalité « scellage instantané » est similaire à l'expérience rapide et itérative que vous obtiendriez avec [Ganache](https://www.trufflesuite.com/ganache).

1. Créez un répertoire pour contenir vos tests et un fichier pour tester votre contrat 'MyToken' :
```
test mkdir && test cd && touchez MyToken.test.ts
```

2. Ouvrez le fichier `MyToken.test.ts` et configurez votre fichier de test pour utiliser le plug-in Solidity de Waffle et utilisez le fournisseur JSON-RPC personnalisé d'Ethers pour vous connecter à Moonbase Alpha :
```typescript
import { use, expect } from 'chai';
import { Provider } from '@ethersproject/providers';
import { solidity } from 'ethereum-waffle';
import { ethers, Wallet } from 'ethers';
import { MyToken, MyTokenFactory } from '../build/types';

// Tell Chai to use Waffle's Solidity plugin
use(solidity);

describe ('MyToken', () => {
  // Use custom provider to connect to Moonbase Alpha
  let provider: Provider = new ethers.providers.JsonRpcProvider('https://rpc.testnet.moonbeam.network');
  let wallet: Wallet;
  let walletTo: Wallet;
  let token: MyToken;

  beforeEach(async () => {
    // Logic for setting up the wallet and deploying MyToken will go here
  });

  // Tests will go here
})
```

3. Avant l'exécution de chaque test, vous souhaiterez créer des portefeuilles et les connecter au fournisseur, utiliser les portefeuilles pour déployer une instance du contrat "MyToken", puis appeler la fonction "initialize" une fois avec un approvisionnement initial de 10 jetons:
```typescript
  beforeEach(async () => {
    const PRIVATE_KEY = '<insert-your-private-key-here>'
    // Create a wallet instance using your private key & connect it to the provider
    wallet = new Wallet(PRIVATE_KEY).connect(provider);

    // Create a random account to transfer tokens to & connect it to the provider
    walletTo = Wallet.createRandom().connect(provider);

    // Use your wallet to deploy the MyToken contract
    token = await new MyTokenFactory(wallet).deploy();

    // Mint 10 tokens to the contract owner, which is you
    let contractTransaction = await token.initialize(10);

    // Wait until the transaction is confirmed before running tests
    await contractTransaction.wait();
  });
```

4. Vous pouvez maintenant créer votre premier test. Le premier test vérifiera votre solde initial pour vous assurer que vous avez reçu la fourniture initiale de 10 jetons. Cependant, pour suivre les bonnes pratiques de test, écrivez d'abord un test d'échec:
```typescript
it('Mints the correct initial balance', async () => {
  expect(await token.balanceOf(wallet.address)).to.equal(1); // This should fail
});
```

5. Avant de pouvoir exécuter votre premier test, vous devrez revenir à la racine et ajouter un fichier de configuration Mocha `.mocharc.json`:
```
cd .. && touch .mocharc.json
```

6. Maintenant éditez le fichier `.mocharc.json` pour configurer Mocha:
```json
{
  "require": "ts-node/register/transpile-only", // Use ts-node to transpile the code for tests
  "timeout": 600000, // Set timeout to 10 minutes
  "extension": "test.ts" // Specify extension for test files
}
```

7. Vous devrez également ajouter un script dans le `package.json` pour exécuter vos tests:
```json
"scripts": {
  "build": "waffle",
  "test": "mocha",
},
```

8. Vous êtes prêt à exécuter les tests, utilisez simplement le script `test` que vous venez de créer et d'exécuter:
```
npm run test
```
Veuillez noter que le traitement peut prendre quelques minutes car les tests sont exécutés sur Moonbase Alpha, mais si tout fonctionnait comme prévu, vous devriez avoir un test qui échoue.

9. Ensuite, vous pouvez revenir en arrière et modifier le test pour vérifier les 10 jetons :
```typescript
it('Mints the correct initial balance', async () => {
  expect(await token.balanceOf(wallet.address)).to.equal(10); // This should pass
});
```
10. Si vous exécutez à nouveau les tests, vous devriez maintenant voir un test réussi:
```
npm run test
```

11. Vous avez testé la possibilité de minter des jetons, vous testerez ensuite la possibilité de transférer les jetons mintés. Si vous voulez d'abord écrire à nouveau un test d'échec, c'est à vous de décider, mais le test final devrait ressembler à ceci:
```typescript
it('Should transfer the correct amount of tokens to the destination account', async () => {
  // Send the destination wallet 7 tokens
  await (await token.transfer(walletTo.address, 7)).wait();

  // Expect the destination wallet to have received the 7 tokens
  expect(await token.balanceOf(walletTo.address)).to.equal(7);
});
```

Félicitations, vous devriez maintenant avoir deux tests réussis ! Au total, votre fichier de test devrait ressembler à ceci:
```typescript
import { use, expect } from 'chai';
import { Provider } from '@ethersproject/providers';
import { solidity } from 'ethereum-waffle';
import { ethers, Wallet } from 'ethers';
import { MyToken, MyTokenFactory } from '../build/types';

use(solidity);

describe ('MyToken', () => {
  let provider: Provider = new ethers.providers.JsonRpcProvider('https://rpc.testnet.moonbeam.network');
  let wallet: Wallet;
  let walletTo: Wallet;
  let token: MyToken;

  beforeEach(async () => {
    const PRIVATE_KEY = '<insert-your-private-key-here>'
    wallet = new Wallet(PRIVATE_KEY).connect(provider);
    walletTo = Wallet.createRandom().connect(provider);
    token = await new MyTokenFactory(wallet).deploy();
    let contractTransaction = await token.initialize(10);
    await contractTransaction.wait();
  });

  it('Mints the correct initial balance', async () => {
    expect(await token.balanceOf(wallet.address)).to.equal(10);
  });

  it('Should transfer the correct amount of tokens to the destination account', async () => {
    await (await token.transfer(walletTo.address, 7)).wait();
    expect(await token.balanceOf(walletTo.address)).to.equal(7);
  });
})
```

Si vous souhaitez écrire plus de tests par vous-même, vous pouvez envisager de tester les transferts de comptes sans fonds ou les transferts de comptes sans fonds suffisants.

## Utilisez Mars pour déployer sur Moonbase Alpha

Après avoir compilé vos contrats et avant le déploiement, vous devrez générer des artefacts de contrat pour Mars. Mars utilise les artefacts de contrat pour les vérifications de type dans les déploiements. Ensuite, vous devrez créer un script de déploiement et déployer le contrat intelligent « MyToken ».

N'oubliez pas que vous effectuerez un déploiement sur Moonbase Alpha et devrez utiliser les configurations TestNet :

--8<-- 'text/testnet/testnet-details.md'

Le déploiement sera divisé en trois sections : [générer des artefacts](#generate-artifacts), [créer un script de déploiement](#create-a-deployment-script) et [déployer avec Mars](#deploy-with- Mars).

### Générer des artefacts

Les artefacts doivent être générés pour Mars afin que les vérifications de type soient activées dans les scripts de déploiement.

1. Mettez à jour le script existant pour exécuter Waffle dans le `package.json` afin d'inclure Mars :
```json
"scripts": {
  "build": "gaufre && mars",
  "test": "moka",
},
```

2. Générez les artefacts et créez le fichier `artifacts.ts` nécessaire aux déploiements :
```
npm run build
```
![Sortie du compilateur Waffle et Mars](/images/waffle-mars/waffle-mars-2.png)

Si vous ouvrez le répertoire `build`, vous devriez maintenant voir un fichier `artifacts.ts` contenant les données d'artefact nécessaires aux déploiements. Pour continuer le processus de déploiement, vous devrez écrire un script de déploiement. Le script de déploiement sera utilisé pour indiquer à Mars quel contrat déployer, sur quel réseau et quel compte doit être utilisé pour déclencher le déploiement.

### Créer un script de déploiement

Vous devez maintenant configurer le déploiement du contrat « MyToken » vers le Moonbase Alpha TestNet.

Dans cette étape, vous allez créer le script de déploiement qui définira comment le contrat doit être déployé. Mars propose une fonction de « déploiement » à laquelle vous pouvez transmettre des options telles que la clé privée du compte pour déployer le contrat, le réseau sur lequel déployer, etc. À l'intérieur de la fonction « déployer » se trouve l'endroit où les contrats à déployer sont définis. Mars a une fonction « contract » qui accepte les « nom », « artefact » et « constructorArgs ». Cette fonction sera utilisée pour déployer le contrat 'MyToken' avec une fourniture initiale de 10 MYTOK.


1. Créez un répertoire `src` pour contenir vos scripts de déploiement et créez le script pour déployer le contrat `MyToken` :
```
mkdir src && cd src && touch deploy.ts
```

2. Dans `deploy.ts`, utilisez la fonction `deploy` de Mars pour créer un script à déployer sur Moonbase Alpha en utilisant la clé privée de votre compte :
```javascript
importer { déployer } depuis 'ethereum-mars' ;

const privateKey = "<insérez-votre-clé-privée-ici>";
deploy({network: 'https://rpc.testnet.moonbeam.network', privateKey},(deployer) => {
  // La logique de déploiement ira ici
});
```

3. Configurez la fonction « deploy » pour déployer le contrat « MyToken » créé dans les étapes précédentes :
```javascript
import { deploy, contract } from 'ethereum-mars';
import { MyToken } from '../build/artifacts';

const privateKey = "<insert-your-private-key-here>";
deploy({network: 'https://rpc.testnet.moonbeam.network', privateKey}, () => {
  contract('myToken', MyToken, [10]);
});
```

4. Ajoutez un script de déploiement à l'objet `scripts` dans le `package.json`:
```json
  "scripts": {
    "build": "waffle && mars",
    "test": "mocha",
    "deploy": "ts-node src/deploy.ts"
  },
```

Jusqu'à présent, vous devriez avoir créé un script de déploiement dans `deploy.ts` qui déploiera le contrat `MyToken` sur Moonbase Alpha, et ajouté la possibilité d'appeler facilement le script et de déployer le contrat.

### Déployer avec Mars

Vous avez configuré le déploiement, il est maintenant temps de déployer réellement sur Moonbase Alpha.

1. Déployez le contrat à l'aide du script que vous venez de créer :
```
npm exécuter déployer
```

2. Dans votre Terminal, Mars vous invitera à appuyer sur 'ENTER' pour envoyer votre transaction :
![Mars confirme le déploiement](/images/waffle-mars/waffle-mars-3.png)

En cas de succès, vous devriez voir les détails de votre transaction, y compris son hachage, le bloc dans lequel elle a été incluse et son adresse.

![Sortie de déploiement de Mars](/images/waffle-mars/waffle-mars-4.png)

Toutes nos félicitations! Vous avez déployé un contrat sur Moonbase Alpha en utilisant Waffle et Mars !

## Exemple de projet

Si vous souhaitez voir un exemple terminé d'un projet Waffle et Mars sur Moonbeam, consultez le [moonbeam-waffle-mars-example](https://github.com/EthWorks/moonbeam-waffle-mars-example) créé par l'équipe derrière Waffle et Mars, EthWorks.
