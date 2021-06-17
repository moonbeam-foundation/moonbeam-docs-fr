---
title: Envoyer une transaction
description: Apprenez à créer et envoyer des transactions sur le réseau compatible Ethereum de Moonbeam avec un script simple utilisant web3.js, ethers.js ou web3.py.
---

# Utilisation des bibliothèques Ethereum pour envoyer des transactions sur Moonbeam

![Intégrations des bibliothèques Ethereum Moonbeam](/images/sendtx/web3-libraries-banner.png)

## Introduction

Ce guide décrit l'utilisation de trois bibliothèques Ethereum différentes pour signer et envoyer manuellement une transaction sur Moonbeam. Les trois bibliothèques couvertes dans ce didacticiel sont:

 - [Web3.js](https://web3js.readthedocs.io/)
 - [Ethers.js](https://docs.ethers.io/)
 - [Web3.py](https://web3py.readthedocs.io/)

!!! note
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

## Vérification des prérequis

Les exemples utilisant à la fois web3.js et ethers.js nécessitent une installation préalable de Node.js et NPM. L'exemple utilisant web3.py nécessite Python et PIP. Au moment de la rédaction de ce guide, les versions utilisées étaient:

 - Node.js v15.10.0
 - NPM v7.5.3
 - Python v3.6.9 (web3 requires Python >= 3.5.3 and < 4)
 - PIP3 v9.0.1

Ensuite, vous devez créer un répertoire pour stocker tous les fichiers pertinents:

```
mkdir transaction && cd transaction/
```

Pour les bibliothèques JavaScript, vous pouvez d'abord créer un fichier simple `package.json` (non obligatoire):

```
npm init --yes
```

Dans le répertoire, installez la bibliothèque à utiliser (web3.py est installé dans le répertoire par défaut de PIP3):

=== "Web3.js"
    ```
    npm i web3
    ```

=== "Ethers.js"
    ```
    npm i ethers
    ```

=== "Web3.py"
    ```
    pip3 install web3
    ```

Les versions utilisées au moment de la publication de ce guide étaient:

 - Web3.js v1.33 (`npm ls web3`)
 - Ethers.js v5.0.31 (`npm ls ethers`)
 - Web3.py v5.17.0 (`pip3 show web3`)

## Le fichier de transaction

Un seul fichier est nécessaire pour exécuter une transaction entre comptes. Le script présenté dans cette section transférera 1 jeton d'une adresse d'origine (à partir de laquelle vous détenez la clé privée), vers une autre adresse. Vous pouvez trouver l'extrait de code de chaque bibliothèque ici (ils ont été nommés de manière arbitraire `transaction.*`):

 - Web3.js: [_transaction.js_](/snippets/code/web3-tx-local/transaction.js)
 - Ethers.js: [_transaction.js_](/snippets/code/ethers-tx-local/transaction.js)
 - Web3.py: [_transaction.py_](/snippets/code/web3py-tx/transaction.py)

Chacun des fichiers, quelle que soit la bibliothèque utilisée, a été divisé en trois sections. Dans la première section ("Définir le fournisseur et les variables"), la bibliothèque à utiliser est importée, et le fournisseur et les autres variables sont définis (les variables dépendent de la bibliothèque). Notez que `providerRPC` contient à la fois le point de terminaison RPC du nœud de développement standard et celui de [Moonbase Alpha](/networks/testnet/).

La deuxième section ("Créer et déployer une transaction") décrit les fonctions nécessaires pour envoyer la transaction elle-même. Certains des principaux points à retenir sont abordés ci-après.

=== "Web3.js"
    ```
    --8<-- 'code/web3-tx-local/transaction.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-tx-local/transaction.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-tx/transaction.py'
    ```

### Web3.js

Dans la première section [du script](/snippets/code/web3-tx-local/transaction.js), l'instance `web3` (ou le fournisseur) est créée à l'aide du constructeur `Web3` avec le fournisseur RPC. En modifiant le fournisseur RPC donné au constructeur, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui est associée sont définies pour la signature de la transaction et la journalisation, respectivement. Seule la clé privée est requise.

L' `addressTo`, où la transaction sera envoyée, est également défini ici, et elle est obligatoire.

Dans la deuxième section, l'objet de transaction est créé avec les champs `to`, `value`, et `gas`. Ceux-ci décrivent le destinataire, le montant à envoyer et le gaz consommé par la transaction (21 000 dans ce cas). Vous pouvez utiliser la fonction `web3.utils.toWei()` pour saisir la valeur dans ETH (par exemple) et obtenir la sortie dans WEI. La transaction est signée avec la clé privée à l'aide de la méthode `web3.eth.accounts.signTransaction()` .  Notez que cela renvoie une promesse qui doit être résolue.

Ensuite, avec la transaction signée (vous pouvez `console.log(createTransaction)` pour voir les valeurs v-r-s), vous pouvez l'envoyer en utilisant la méthode `web3.eth.sendSignedTransaction()` , en fournissant la transaction signée située dans `createTransaction.rawTransaction`.

Enfin, exécutez la fonction de déploiement asynchrone.

### Ethers.js

Dans la première section [du script](/snippets/code/ethers-tx-local/transaction.js), différents réseaux peuvent être spécifiés avec un nom, une URL RPC (obligatoire) et un ID de chaîne. Le fournisseur (similaire à l'instance `web3`) est créé avec la méthode `ethers.providers.StaticJsonRpcProvider` . Une alternative consiste à utiliser la méthode `ethers.providers.JsonRpcProvide(providerRPC)` , qui ne nécessite que l'adresse du point de terminaison RPC du fournisseur. Mais cela pourrait créer des problèmes de compatibilité avec les spécifications de projet individuelles.

La clé privée est définie pour créer une instance de portefeuille, ce qui nécessite également le fournisseur de l'étape précédente. L'instance de portefeuille est utilisée pour signer des transactions.

L `addressTo`, où la transaction sera envoyée, est également défini ici, et elle est obligatoire.

Dans la deuxième section, une fonction asynchrone encapsule la méthode `wallet.sendTransaction(txObject)` . L'objet de transaction est assez simple. Il ne nécessite que l'adresse du destinataire et le montant à envoyer. Notez que `ethers.utils.parseEther()` qui gère les conversions d'unités nécessaires de ETH à WEI peut être utilisé, - similaire à l'utilisation de `ethers.utils.parseUnits(value,'ether')`.

Une fois la transaction envoyée, vous pouvez obtenir la réponse de la transaction (nommée `createReceipt` dans cet exemple), qui a quelques propriétés. Par exemple, vous pouvez appeler la méthode `createReceipt.wait()`  pour attendre que la transaction soit traitée (l'état de réception est OK).

Enfin, exécutez la fonction de déploiement asynchrone.

### Web3.py

Dans la première section [du script](/snippets/code/web3py-tx/transaction.py), l'instance `web3` (ou le fournisseur) est créée à l'aide de la méthode `Web3(Web3.HTTPProvider(provider_rpc))` avec le fournisseur RPC. En modifiant le fournisseur RPC, vous pouvez choisir le réseau auquel vous souhaitez envoyer la transaction.

La clé privée et l'adresse publique qui lui sont associées sont définies pour la signature de la transaction et à des fins de journalisation. L'adresse publique n'est pas requise.

L' `addressTo`, où la transaction sera envoyée, est également défini ici, et il est obligatoire.

Dans la deuxième section, l'objet de transaction est créé avec les champs `nonce`, `gasPrice`, `gas`, `to`, et `value` . Ceux-ci décrivent le nombre de transactions, le prix du gaz (0 pour le développement et Moonbase Alpha), le gaz (21 000 dans ce cas), le destinataire et le montant à envoyer. Notez que le nombre de transactions peut être obtenu avec la méthode `web3.eth.getTransactionCount(address)` . Aussi, vous pouvez utiliser la fonction `web3.toWei()` pour saisir la valeur dans ETH (par exemple) et obtenir la sortie dans WEI. La transaction est signée avec la clé privée à l'aide de la méthode `web3.eth.account.signTransaction()` .

Ensuite, une fois la transaction signée, vous pouvez l'envoyer en utilisant la méthode `web3.eth.sendSignedTransaction()` , en fournissant la transaction signée située dans `createTransaction.rawTransaction`.

## Le fichier de solde

Avant d'exécuter le script, un autre fichier qui est nécessaire vérifie les soldes des deux adresses avant et après la transaction. Cela peut être facilement fait par une simple requête sur le solde d'un compte.

Vous pouvez trouver l'extrait de code pour chaque bibliothèque ici (les fichiers ont été nommés arbitrairement `balances.*`):

 - Web3.js: [_balances.js_](/snippets/code/web3-tx-local/balances.js)
 - Ethers.js: [_balances.js_](/snippets/code/ethers-tx-local/balances.js)
 - Web3.py: [_balances.py_](/snippets/code/web3py-tx/balances.py)

Pour plus de simplicité, le fichier de solde est composé en deux sections. Comme précédemment, dans la première section ("Définir le fournisseur et les variables"), la bibliothèque à utiliser est importée, et le fournisseur et l'adresse de / à (pour vérifier les soldes) sont définis.

La deuxième section ("Fonction d'appel de solde") décrit les fonctions nécessaires pour récupérer les soldes des comptes précédemment définis. Notez qu'il contient à la fois le point de terminaison RPC du nœud de développement standard `providerRPC` et celui de [Moonbase Alpha](/networks/testnet/). Certains des principaux points à retenir sont abordés ci-après.

=== "Web3.js"
    ```
    --8<-- 'code/web3-tx-local/balances.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-tx-local/balances.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-tx/balances.py'
    ```

### Web3.js

La première section [du script](/snippets/code/web3-tx-local/balances.js) est très similaire à celle du [fichier de transaction](/getting-started/local-node/send-transaction/#web3js). La principale différence est qu'aucune clé privée n'est nécessaire car il n'est pas nécessaire d'envoyer une transaction.

Dans la deuxième section, une fonction asynchrone enveloppe la méthode de web3 utilisée pour récupérer le solde d'une adresse, `web3.eth.getBalance(address)`. Une fois de plus, vous pouvez utiliser la fonction `web3.utils.fromWei()` pour transformer la balance en un nombre plus lisible dans ETH.

### Ethers.js

La première section [du script](/snippets/code/ethers-tx-local/balances.js) est très similaire à celle du [fichier de transaction](/getting-started/local-node/send-transaction/#ethersjs). La principale différence est qu'aucune clé privée n'est nécessaire car il n'est pas nécessaire d'envoyer une transaction. Au contraire, les besoins `addressFrom` doivent être définis.

Dans la deuxième section, une fonction asynchrone encapsule la méthode de fournisseur utilisée pour récupérer le solde d'une adresse, qui est `provider.getBalance(address)`. Une fois de plus, vous pouvez utiliser la fonction `ethers.utils.formatEther()` pour transformer la balance en un nombre plus lisible dans ETH.

### Web3.py

La première section [du script](/snippets/code/web3py-tx/balances.py) est très similaire à celle du [fichier de transaction](/getting-started/local-node/send-transaction/#web3py). La principale différence est qu'aucune clé privée n'est nécessaire car il n'est pas nécessaire d'envoyer une transaction.

Dans la deuxième section, la méthode `web3.eth.getBalance(address)` est utilisée pour récupérer le solde d'une adresse cible. Une fois de plus, vous pouvez utiliser la fonction `eb3.fromWei()` pour transformer la balance en un nombre plus lisible dans ETH.

## Exécution des scripts

Pour cette section, le code ci-dessus a été adapté pour cibler un nœud de développement, que vous pouvez exécuter en suivant [ce tutoriel](/getting-started/local-node/setting-up-a-node/). De plus, chaque transaction a été envoyée à partir du compte préfinancé fourni avec le nœud:

--8<-- 'text/metamask-local/dev-account.md'

Tout d'abord, vérifiez les soldes des deux adresses avant la transaction en exécutant (notez que le répertoire a été renommé pour chaque bibliothèque):

=== "Web3.js"
    ```
    node balances.js
    ```

=== "Ethers.js"
    ```
    node balances.js
    ```

=== "Web3.py"
    ```
    python3 balances.py
    ```

Next, run the _transaction.\*_ script to execute the transaction:

=== "Web3.js"
    ```
    node transaction.js
    ```

=== "Ethers.js"
    ```
    node transaction.js
    ```

=== "Web3.py"
    ```
    python3 transaction.py
    ```

Et enfin, revérifiez le solde pour vous assurer que le transfert a réussi. L'exécution entière devrait ressembler à ceci:

=== "Web3.js"
    ![Send Tx Web3js](/images/sendtx/sendtx-web3js.png)

=== "Ethers.js"
    ![Send Tx Etherjs](/images/sendtx/sendtx-ethers.png)

=== "Web3.py"
    ![Send Tx Web3py](/images/sendtx/sendtx-web3py.png)

