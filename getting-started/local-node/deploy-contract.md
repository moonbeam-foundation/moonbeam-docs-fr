---
title: Déployer un contrat
description: Apprenez à déployer des contrats intelligents basés sur Solidity non modifiés et inchangés sur un nœud Moonbeam avec un script simple utilisant Web3.js, Ethers.js ou Web3.py.
---

# Utiliser les bibliothèques Ethereum pour déployer des contrats intelligents sur Moonbeam

![Intégrations des bibliothèques Ethereum Moonbeam](/images/sendtx/web3-libraries-banner.png)

## Introduction

Ce guide décrit l'utilisation du compilateur Solidity et de trois bibliothèques Ethereum différentes pour signer et envoyer manuellement une transaction sur Moonbeam. Les trois bibliothèques couvertes par ce didacticiel sont:

 - [Web3.js](https://web3js.readthedocs.io/)
 - [Ethers.js](https://docs.ethers.io/)
 - [Web3.py](https://web3py.readthedocs.io/)

En outre, deux autres bibliothèques seront utilisées pour compiler le contrat intelligent:

 - [Solc-js](https://www.npmjs.com/package/solc) pour compiler des contrats intelligents Solidity à l'aide de JavaScript
 - [Py-solc-x](https://pypi.org/project/py-solc-x/) pour compiler des contrats intelligents Solidity avec Python

!!! note
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

## Vérification des prérequis

Les exemples utilisant à la fois web3.js et ethers.js nécessitent l'installation préalable de Node.js et NPM. Pour le web3.py, vous avez besoin de Python et PIP. Au moment de la rédaction de ce guide, les versions utilisées étaient:

 - Node.js v15.10.0
 - NPM v7.5.3
 - Python v3.6.9 (web3 requires Python >= 3.5.3 and < 4)
 - PIP3 v9.0.1

Ensuite, créez un répertoire pour stocker tous les fichiers pertinents:

```
mkdir incrementer && cd incrementer/
```

Pour les bibliothèques JavaScript, vous pouvez d'abord créer un fichier simple `package.json` (non requis):

```
npm init --yes
```

Dans le répertoire, installez la bibliothèque correspondante et le compilateur Solidity (_web3.py_ and _py-solc-x_ sont installés dans le répertoire par défaut de PIP3):

=== "Web3.js"
    ```
    npm i web3 npm i solc@0.8.0
    ```

=== "Ethers.js"
    ```
    npm i ethers npm i solc@0.8.0
    ```

=== "Web3.py"
    ```
    pip3 install web3 pip3 install py-solc-x
    ```

Les versions utilisées lors de la publication de ce guide étaient

 - Web3.js v1.33 (`npm ls web3`)
 - Ethers.js v5.0.31 (`npm ls ethers`)
 - Solc (JS) v0.8.0 (`npm ls solc`)
 - Web3.py v5.17.0 (`pip3 show web3`)
 - Py-solc-x v1.1.0 (`pip3 show py-solc-x`)

La configuration de cet exemple sera relativement simple et contiendra les fichiers suivants:

 - **_Incrementer.sol_** —  le fichier avec notre code Solidity
 - **_compile.\*_** — compile le contrat avec le compilateur Solidity
 - **_deploy.\*_**: il gérera le déploiement sur notre nœud Moonbeam local
 - **_get.\*_** — il fera un appel au nœud pour obtenir la valeur actuelle du nombre
 - **_increment.\*_** — il effectuera une transaction pour incrémenter le numéro stocké sur le nœud Moonbeam
 - **_reset.\*_** — la fonction à appeler qui remettra à zéro le nombre mémorisé

## Le fichier contrat

Le contrat utilisé est un simple incrémenteur, nommé arbitrairement _Incrementer.sol_, que vous pouvez trouver [ici](/snippets/code/web3-contract-local/Incrementer.sol). Le code Solidity est le suivant:

```solidity
--8<-- 'code/web3-contract-local/Incrementer.sol'
```

La fonction `constructor`, qui s'exécute lorsque le contrat est déployé, définit la valeur initiale de la variable numérique stockée sur la chaîne (la valeur par défaut est 0). La fonction `increment` ajoute la  `_value` fourni au numéro actuel, mais une transaction doit être envoyée, ce qui modifie les données stockées. Enfin, la fonction  `reset` réinitialise la valeur stockée à zéro.

!!! note
    Ce contrat est un exemple simple à des fins d'illustration uniquement et ne gère pas les valeurs enveloppantes.

## Compilation du contrat

Le seul but du fichier de compilation est d'utiliser le compilateur Solidity pour sortir le bytecode et l'interface (ABI) de notre contrat. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés de manière arbitraire `compile.*`):

 - Web3.js: [_compile.js_](/snippets/code/web3-contract-local/compile.js)
 - Ethers.js: [_compile.js_](/snippets/code/web3-contract-local/compile.js)
 - Web3.py: [_compile.py_](/snippets/code/web3py-contract/compile.py)

!!! note
    Le fichier de compilation pour les deux bibliothèques JavaScript est le même car ils partagent les liaisons JavaScript pour le compilateur Solidity (même package)

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/compile.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/web3-contract-local/compile.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/compile.py'
    ```

### Web3.js and Ethers.js

Dans la première partie [du script](/snippets/code/web3-contract-local/compile.js), le chemin du contrat est récupéré et son contenu lu.

Ensuite, l'objet d'entrée du compilateur Solidity est généré et il est passé en tant qu'entrée à la fonction `solc.compile` .

Enfin, extrayez les données du contrat `Incrementer` du fichier `Incrementer.sol` , et exportez-le pour que le script de déploiement puisse l'utiliser.

### Web3.py

Dans la première partie [du script](/snippets/code/web3py-contract/compile.py), le fichier du contrat est compilé à l'aide de la fonction `solcx.compile_files` . Notez que le fichier de contrat se trouve dans le même répertoire que le script de compilation.

!!! note
   Lors de l'exécution de `compile.py` vous pouvez obtenir une erreur indiquant que `Solc` doit être installé. Si tel est le cas, décommenter la ligne du fichier qui s'exécute `solcx.install_solc()` et réexécutez le fichier de compilation avec `python3 compile.py`. Plus d'informations peuvent être trouvées dans [ce lien](https://pypi.org/project/py-solc-x/).

Ensuite, et pour terminer le script, les données du contrat sont exportées. Dans cet exemple, seuls l'interface (ABI) et le bytecode ont été définis.

## Déployer le contrat

Quelle que soit la bibliothèque, la stratégie de déploiement du contrat intelligent compilé est quelque peu similaire. Une instance de contrat est créée à l'aide de son interface (ABI) et de son bytecode. À partir de cette instance, une fonction de déploiement est utilisée pour envoyer une transaction signée qui déploie le contrat. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés arbitrairement `deploy.*`):

 - Web3.js: [_deploy.js_](/snippets/code/web3-contract-local/deploy.js)
 - Ethers.js: [_deploy.js_](/snippets/code/ethers-contract-local/deploy.js)
 - Web3.py: [_deploy.py_](/snippets/code/web3py-contract/deploy.py)

Pour plus de simplicité, le fichier de déploiement est composé de deux sections. Dans la première section ("Définir le fournisseur et les variables"), la bibliothèque à utiliser ainsi que l'ABI et le bytecode du contrat sont importés. En outre, le fournisseur et le compte de (avec la clé privée) sont définis. Notez que `providerRPC` contient à la fois le point de terminaison RPC du nœud de développement standard et celui de [Moonbase Alpha](/networks/testnet/).

La deuxième section ("Déployer le contrat") décrit la partie réelle du déploiement du contrat. Notez que pour cet exemple, la valeur initiale de la variable `number` a été fixée à 5. Certains des points clés à retenir sont abordés ci-après.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/deploy.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/deploy.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/deploy.py'
    ```

!!! note
    Le script _deploy.\*_ Fournit l'adresse du contrat en tant que sortie. Ceci est pratique, car il est utilisé pour les fichiers d'interaction de contrat.

### Web3.js

Dans la première partie [du script](/snippets/code/web3-contract-local/deploy.js), l'instance `web3` (ou le fournisseur) est créée à l'aide du constructeur `Web3` avec le fournisseur RPC. En modifiant le fournisseur RPC donné au constructeur, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui est associée sont définies à des fins de signature de la transaction et de journalisation. Seule la clé privée est requise. De plus, le bytecode et l'interface (ABI) du contrat sont extraits de l'exportation de la compilation.

Dans la deuxième section, une instance de contrat est créée en fournissant l'ABI. Ensuite, la fonction `deploy` est utilisée, qui a besoin du bytecode et des arguments de la fonction constructeur. Cela générera l’objet de transaction du constructeur.

Ensuite, la transaction du constructeur peut être signée à l'aide de la méthode `web3.eth.accounts.signTransaction()` . Le champ de données correspond au bytecode, et les arguments d'entrée du constructeur sont codés ensemble. Notez que la valeur du gaz est obtenue en utilisant l'option `estimateGas()` à l'intérieur de la transaction constructeur.

Enfin, la transaction signée est envoyée et l'adresse du contrat est affichée dans le terminal.

### Ethers.js

Dans la première partie [du script](/snippets/code/ethers-contract-local/deploy.js), différents réseaux peuvent être spécifiés avec un nom, une URL RPC (obligatoire) et un ID de chaîne. Le fournisseur (similaire à l'instance `web3`) est créé avec la méthode `ethers.providers.StaticJsonRpcProvider` . Une alternative consiste à utiliser la méthode `ethers.providers.JsonRpcProvide(providerRPC)` , qui ne nécessite que l'adresse du point de terminaison RPC du fournisseur. Mais cela pourrait créer des problèmes de compatibilité avec les spécifications de projet individuelles.

La clé privée est définie pour créer une instance de portefeuille, ce qui nécessite également le fournisseur de l'étape précédente. L'instance de portefeuille est utilisée pour signer des transactions. De plus, le bytecode et l'interface (ABI) du contrat sont extraits de l'exportation de la compilation.

Dans la deuxième section, une instance de contrat est créée avec `ethers.ContractFactory()`, fournissant l'ABI, le bytecode et le portefeuille. Ainsi, l'instance de contrat a déjà un signataire. Ensuite, la fonction `deploy` est utilisée, qui a besoin des arguments d'entrée du constructeur. Cela enverra la transaction pour le déploiement du contrat. Pour attendre un reçu de transaction, vous pouvez utiliser la methode `deployed()` de la transaction de déploiement de contrat.

Enfin, l'adresse du contrat est affichée dans le terminal.

### Web3.py

Dans la première partie [du script](/snippets/code/web3py-contract/deploy.py), l'instance `web3` (ou le fournisseur) est créée à l'aide de la méthode `Web3(Web3.HTTPProvider(provider_rpc))` avec le fournisseur RPC. En modifiant le fournisseur RPC, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui est associée sont définies pour signer la transaction et établir l'adresse d'expédition.

Dans la deuxième section, une instance de contrat est créée avec `web3.eth.contract()`, fournissant l'ABI et le bytecode importés du fichier de compilation. Ensuite, la transaction du constructeur peut être générée à l'aide de la méthode `constructor().buildTransaction()` de l'instance de contrat. Notez qu'à l'intérieur de `constructor()`, vous devez spécifier les arguments d'entrée du constructeur. Le compte d'expédition `from` doit également être décrit. Assurez-vous d'utiliser celui associé à la clé privée. En outre, le nombre de transactions peut être obtenu avec la méthode `web3.eth.getTransactionCount(address)` .

La transaction du constructeur peut être signée à l'aide de `web3.eth.account.signTransaction()`, en passant la transaction du constructeur et la clé privée.

Enfin, la transaction signée est envoyée et l'adresse du contrat est affichée dans le terminal.

## Lecture du contrat (méthodes d'appel)

Les méthodes d'appel sont le type d'interaction qui ne modifie pas le stockage du contrat (modifier les variables), ce qui signifie qu'aucune transaction n'a besoin d'être envoyée.

Passons en revue le fichier _get.\*_  (Le plus simple de tous), qui récupère la valeur actuelle stockée dans le contrat. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés de manière arbitraire `get.*`):

 - Web3.js: [_get.js_](/snippets/code/web3-contract-local/get.js)
 - Ethers.js: [_get.js_](/snippets/code/ethers-contract-local/get.js)
 - Web3.py: [_get.py_](/snippets/code/web3py-contract/get.py)

Pour plus de simplicité, le fichier get est composé de deux sections. Dans la première section ("Définir le fournisseur et les variables"), la bibliothèque à utiliser et l'ABI du contrat sont importés. En outre, le fournisseur et l'adresse du contrat sont définis. Notez que `providerRPC` contient à la fois le point de terminaison RPC du nœud de développement standard et celui de [Moonbase Alpha](/networks/testnet/).

La deuxième section ("Fonction d'appel") décrit l'appel réel au contrat. Quelle que soit la bibliothèque, une instance de contrat est créée (liée à l'adresse du contrat), à partir de laquelle la méthode d'appel est interrogée. Certains des principaux points à retenir sont abordés ci-après.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/get.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/get.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/get.py'
    ```

### Web3.js

Dans la première partie [du script](/snippets/code/web3-contract-local/get.js), l'instance `web3` (ou le fournisseur) est créée à l'aide du constructeur `Web3` avec le fournisseur RPC. En modifiant le fournisseur RPC donné au constructeur, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

L'interface et l'adresse du contrat (ABI) sont également nécessaires pour interagir avec lui.

Dans la deuxième section, une instance de contrat est créée avec `web3.eth.Contract()` en fournissant l'ABI et l'adresse. Ensuite, la méthode à appeler peut être interrogée avec la fonction `contract.methods.methodName(_input).call()` , remplacant `contract`, `methodName` et `_input` avec l'instance de contrat, la fonction à appeler et la saisie de la fonction (si nécessaire). Cette promesse, une fois résolue, renverra la valeur demandée.

Enfin, la valeur est affichée dans le terminal.

### Ethers.js

Dans la première partie [du script](/snippets/code/ethers-contract-local/get.js), différents réseaux peuvent être spécifiés avec un nom, une URL RPC (obligatoire) et un ID de chaîne. Le fournisseur (similaire à l'instance `web3`) est créé avec la méthode `ethers.providers.StaticJsonRpcProvider` . Une alternative consiste à utiliser la méthode `ethers.providers.JsonRpcProvide(providerRPC)`, qui ne nécessite que l'adresse du point de terminaison RPC du fournisseur. Mais cela pourrait créer des problèmes de compatibilité avec les spécifications de projet individuelles.

L'interface et l'adresse du contrat (ABI) sont également nécessaires pour interagir avec lui.

Dans la deuxième section, une instance de contrat est créée avec `ethers.Contract()`, en fournissant son adresse, ABI et le fournisseur. Ensuite, la méthode à appeler peut être interrogée avec la fonction `contract.methodName(_input)` , remplacant `contract` `methodName`, et `_input` avec l'instance de contrat, la fonction à appeler et l'entrée de la fonction (si nécessaire). Cette promesse, une fois résolue, renverra la valeur demandée.

Enfin, la valeur est affichée dans le terminal.

### Web3.py

Dans la première partie [du script](/snippets/code/web3py-contract/get.py), l'instance `web3` (ou le fournisseur) est créée à l'aide de la méthode `Web3(Web3.HTTPProvider(provider_rpc))` avec le fournisseur RPC. En modifiant le fournisseur RPC, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

L'interface et l'adresse du contrat (ABI) sont également nécessaires pour interagir avec lui.

Dans la deuxième section, une instance de contrat est créée avec `web3.eth.contract()` en fournissant l'ABI et l'adresse. Ensuite, la méthode à appeler peut être interrogée avec la fonction `contract.functions.method_name(input).call()`, remlacant `contract`, `method_name` et `input` avec l'instance de contrat, la fonction à appeler et l'entrée de la fonction (si nécessaire). Cela renvoie la valeur demandée.

Enfin, la valeur est affichée dans le terminal.

## Interagir avec le contrat (méthodes d'envoi)

Les méthodes d'envoi sont le type d'interaction qui modifie le stockage du contrat (variables de changement), ce qui signifie qu'une transaction doit être signée et envoyée.

Tout d'abord, passons en revue le fichier _increment.\*_ , Qui incrémente le nombre actuel stocké dans le contrat d'une valeur donnée. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés de manière arbitraire `increment.*`):

 - Web3.js: [_increment.js_](/snippets/code/web3-contract-local/increment.js)
 - Ethers.js: [_increment.js_](/snippets/code/ethers-contract-local/increment.js)
 - Web3.py: [_increment.py_](/snippets/code/web3py-contract/increment.py)

Pour plus de simplicité, le fichier incrément est composé de deux sections. Dans la première section ("Définir le fournisseur et les variables"), la bibliothèque à utiliser et l'ABI du contrat sont importés. Le fournisseur, l'adresse du contrat et la valeur de la fonction `increment` sont également définis. Notez que `providerRPC` contient à la fois le point de terminaison RPC du nœud de développement standard et celui de [Moonbase Alpha](/networks/testnet/).

La deuxième section ("Fonction d'envoi") décrit la fonction réelle à appeler avec la transaction. Quelle que soit la bibliothèque, une instance de contrat est créée (liée à l'adresse du contrat), à partir de laquelle la fonction à utiliser est interrogée.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/increment.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/increment.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/increment.py'
    ```

Le deuxième fichier à interagir avec le contrat est le fichier _reset.\*_ , Qui remet à zéro le nombre stocké dans le contrat. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés de manière arbitraire `reset.*`):

 - Web3.js: [_reset.js_](/snippets/code/web3-contract-local/reset.js)
 - Ethers.js: [_reset.js_](/snippets/code/ethers-contract-local/reset.js)
 - Web3.py: [_reset.py_](/snippets/code/web3py-contract/reset.py)

La structure de chaque fichier est très similaire à son _increment.\*_ contrepartie pour chaque bibliothèque. La principale différence est la méthode appelée.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/reset.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/reset.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/reset.py'
    ```

### Web3.js

Dans la première partie du script ([incrémenter](/snippets/code/web3-contract-local/increment.js) ou [réinitialiser](/snippets/code/web3-contract-local/reset.js) les fichies), l'instance `web3` (ou le fournisseur) est créée à l'aide du constructeur `Web3` avec le fournisseur RPC. En modifiant le fournisseur RPC donné au constructeur, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui est associée sont définies à des fins de signature de la transaction et de journalisation. Seule la clé privée est requise. En outre, l'interface et l'adresse du contrat (ABI) sont nécessaires pour interagir avec lui. Si nécessaire, vous pouvez définir n'importe quelle variable requise comme entrée de la fonction avec laquelle vous allez interagir.

Dans la deuxième section, une instance de contrat est créée avec `web3.eth.Contract()` en fournissant l'ABI et l'adresse. Ensuite, vous pouvez construire l'objet de transaction avec la fonction `contract.methods.methodName(_input)` , remplacant `contract`, `methodName` et `_input` avec l'instance de contrat, fonction à appeler, et l' entrée de la fonction (si nécessaire).

Ensuite, la transaction peut être signée à l'aide de la méthode `web3.eth.accounts.signTransaction()` . Le champ de données correspond à l'objet de transaction de l'étape précédente. Notez que la valeur du gaz est obtenue en utilisant l'option `estimateGas()` à l'intérieur de l'objet de transaction.

Enfin, la transaction signée est envoyée et le hachage de la transaction est affiché dans le terminal.

### Ethers.js

Dans la première partie du script ([incrémenter](/snippets/code/ethers-contract-local/increment.js) ou [réinitialiser](/snippets/code/ethers-contract-local/reset.js) les fichiers), différents réseaux peuvent être spécifiés avec un nom, une URL RPC (obligatoire) et un ID de chaîne. Le fournisseur (similaire à l'instance `web3`) est créé avec la méthode `ethers.providers.StaticJsonRpcProvider`. Une alternative consiste à utiliser la méthode `ethers.providers.JsonRpcProvide(providerRPC)` , qui ne nécessite que l'adresse du point de terminaison RPC du fournisseur. Mais cela pourrait créer des problèmes de compatibilité avec les spécifications de projet individuelles.

La clé privée est définie pour créer une instance de portefeuille, ce qui nécessite également le fournisseur de l'étape précédente. L'instance de portefeuille est utilisée pour signer des transactions. En outre, l'interface et l'adresse du contrat (ABI) sont nécessaires pour interagir avec lui. Si nécessaire, vous pouvez définir n'importe quelle variable requise comme entrée de la fonction avec laquelle vous allez interagir.

Dans la deuxième section, une instance de contrat est créée avec `ethers.Contract()`, en fournissant son adresse, son ABI et son portefeuille. Ainsi, l'instance de contrat a déjà un signataire. Ensuite, la transaction correspondant à une fonction spécifique peut être envoyé à la fonction `contract.methodName(_input)` , remplacant `contract`, `methodName` et `_input` avec l'instance de contrat, fonction à appeler, et l' entrée de la fonction (si nécessaire). Pour attendre un reçu de transaction, vous pouvez utiliser la méthode `wait()`  de la transaction de déploiement de contrat.

Enfin, le hachage de la transaction est affiché dans le terminal.

### Web3.py

Dans la première partie du script ([incrémenter](/snippets/code/web3py-contract/increment.py) ou [réinitialiser](/snippets/code/web3py-contract/reset.py) les fichiers), l'instance `web3` (ou le fournisseur) est créée à l'aide de la méthode `Web3(Web3.HTTPProvider(provider_rpc))` avec le fournisseur RPC. En modifiant le fournisseur RPC, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui est associée sont définies pour signer la transaction et établir l'adresse d'expédition. En outre, l'interface et l'adresse du contrat (ABI) sont également nécessaires pour interagir avec lui.

Dans la deuxième section, une instance de contrat est créée avec `web3.eth.contract()` en fournissant l'ABI et l'adresse. Ensuite, vous pouvez construire l'objet de transaction avec la fonction `contract.functions.methodName(_input).buildTransaction` , remplacant `contract`, `methodName` et `_input` avec l'instance de contrat, la fonction à appeler, et l' entrée de la fonction (si nécessaire). À l'intérieur `buildTransaction()`, le compte d'expédition `from` doit être décrit. Assurez-vous d'utiliser celui associé à la clé privée. En outre, le nombre de transactions peut être obtenu avec la méthode `web3.eth.getTransactionCount(address)` .

La transaction peut être signée à l'aide de `web3.eth.account.signTransaction()`, en passant l'objet de transaction de l'étape précédente et la clé privée.

Enfin, le hachage de la transaction est affiché dans le terminal.

## Exécution des scripts

Pour cette section, le code ci-dessus a été adapté pour cibler un nœud de développement, que vous pouvez exécuter en suivant [ce tutoriel](/getting-started/local-node/setting-up-a-node/). De plus, chaque transaction a été envoyée à partir du compte préfinancé fourni avec le nœud:

--8<-- 'text/metamask-local/dev-account.md'

Tout d'abord, déployez le contrat en exécutant (notez que le répertoire a été renommé pour chaque bibliothèque):

=== "Web3.js"
    ```
    node deploy.js
    ```

=== "Ethers.js"
    ```
    node deploy.js
    ```

=== "Web3.py"
    ```
    python3 deploy.py
    ```

Cela déploiera le contrat et retournera l'adresse:

=== "Web3.js"
    ![Deploy Contract Web3js](/images/deploycontract/contract-deploy-web3js.png)

=== "Ethers.js"
    ![Deploy Contract Etherjs](/images/deploycontract/contract-deploy-ethers.png)

=== "Web3.py"
    ![Deploy Contract Web3py](/images/deploycontract/contract-deploy-web3py.png)

Ensuite, exécutez le fichier d'incrémentation. Vous pouvez utiliser le fichier get pour vérifier la valeur du nombre stocké dans le contrat avant et après l'incrémentation:

=== "Web3.js"
    ```
    # Get value
    node get.js 
    # Increment value
    increment.js
    # Get value
    node get.js
    ```

=== "Ethers.js"
    ```
    # Get value
    node get.js 
    # Increment value
    increment.js
    # Get value
    node get.js
    ```

=== "Web3.py"
    ```
    # Get value
    python3 get.py 
    # Increment value
    python3 increment.py
    # Get value
    python3 get.py
    ```

Cela affichera la valeur avant la transaction d'incrémentation, le hachage de la transaction et la valeur après:

=== "Web3.js"
    ![Increment Contract Web3js](/images/deploycontract/contract-increment-web3js.png)

=== "Ethers.js"
    ![Increment Contract Etherjs](/images/deploycontract/contract-increment-ethers.png)

=== "Web3.py"
    ![Increment Contract Web3py](/images/deploycontract/contract-increment-web3py.png)

Enfin, exécutez le fichier de réinitialisation. Encore une fois, vous pouvez utiliser le fichier get pour vérifier la valeur du nombre stocké dans le contrat avant et après sa réinitialisation:

=== "Web3.js"
    ```
    # Get value
    node get.js 
    # Reset value
    node reset.js 
    # Get value
    node get.js
    ```

=== "Ethers.js"
    ```
    # Get value
    node get.js 
    # Reset value
    node reset.js 
    # Get value
    node get.js
    ```

=== "Web3.py"
    ```
    # Get value
    python3 get.py 
    # Reset value
    python3 reset.py
    # Get value
    python3 get.py
    ```

Cela affichera la valeur avant la transaction de réinitialisation, le hachage de la transaction et la valeur après:

=== "Web3.js"
    ![Réinitialiser le contrat Web3js](/images/deploycontract/contract-reset-web3js.png)

=== "Ethers.js"
    ![Réinitialiser le contrat Etherjs](/images/deploycontract/contract-reset-ethers.png)

=== "Web3.py"
    ![Réinitialiser le contrat Web3py](/images/deploycontract/contract-reset-web3py.png)

