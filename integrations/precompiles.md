---
title: Contrats précompilés
description:  Apprenez à utiliser des contrats précompilés sur Moonbase Alpha, le réseau Moonbeam TestNet qui est unique pour sa compatibilité complète avec Ethereum. 
---

# Contrats précompilés sur Moonbase Alpha

## Introduction

Une autre fonctionnalité ajoutée avec la [sortie de Moonbase Alpha v2](https://moonbeam.network/announcements/moonbase-alpha-v2-contract-events-pub-sub-capabilities/) est l'inclusion de certains [contrats précompilés](https://docs.klaytn.com/smart-contract/precompiled-contracts) disponibles nativement sur Ethereum. 

Cinq précompilations sont actuellement incluses, dont: ecrecover, sha256, ripemd-160, la fonction d'identité et l'exponentiation modulaire.

Dans ce guide, nous vous expliquerons comment utiliser et / ou vérifier ces précompilations.

## Vérification des prérequis

--8<-- 'text/common/install-nodejs.md'

Au moment de la rédaction de ce guide, les versions utilisées étaient respectivement 15.2.1 et 7.0.8. Nous devrons également installer le package Web3 en exécutant :

```
npm install --save web3
```

Pour vérifier la version installée de Web3, vous pouvez utiliser la commande `ls`:

```
npm ls web3
```
Au moment de la rédaction de ce guide, la version utilisée était la 1.3.0. Nous utiliserons également [Remix](/integrations/remix/), en le connectant au TestNet Moonbase Alpha via [MetaMask](/integrations/wallets/metamask/).

## Vérifier les signatures avec ECRECOVER

La fonction principale de cette précompilation est de vérifier la signature d'un message. En termes généraux, vous alimentez `ecrecover` les valeurs de signature de la transaction et elle renvoie une adresse. La signature est vérifiée si l'adresse renvoyée est la même que l'adresse publique qui a envoyé la transaction.

Passons à un petit exemple pour montrer comment tirer parti de cette fonction précompilée. Pour ce faire, nous devons récupérer les valeurs de signature de la transaction (v, r, s). Par conséquent, nous signerons et récupérerons le message signé où ces valeurs sont:

```solidity
const Web3 = require('web3');

// Provider
const web3 = new Web3('https://rpc.testnet.moonbeam.network');

// Address and Private Key
const address = '0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b';
const pk1 = '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
const msg = web3.utils.sha3('supercalifragilisticexpialidocious');

async function signMessage(pk) {
   try {
   // Sign and get Signed Message
      const smsg = await web3.eth.accounts.sign(msg, pk);
      console.log(smsg);
   } catch (error) {
      console.error(error);
   }
}

signMessage(pk1);
```

Ce code renverra l'objet suivant dans le terminal:

```js
{
  message: '0xc2ae6711c7a897c75140343cde1cbdba96ebbd756f5914fde5c12fadf002ec97',
  messageHash: '0xc51dac836bc7841a01c4b631fa620904fc8724d7f9f1d3c420f0e02adf229d50',
  v: '0x1b',
  r: '0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef',
  s: '0x7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb4',
  signature: '0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb41b'
}
```
Avec les valeurs nécessaires, nous pouvons aller sur Remix pour tester le contrat précompilé. Notez que cela peut également être vérifié avec la bibliothèque Web3 JS, mais dans notre cas, nous irons sur Remix pour nous assurer qu'il utilise le contrat précompilé sur la blockchain. Le code Solidity que nous pouvons utiliser pour vérifier la signature est le suivant:

```solidity
pragma solidity ^0.7.0;

contract ECRECOVER{
    address addressTest = 0x12Cb274aAD8251C875c0bf6872b67d9983E53fDd;
    bytes32 msgHash = 0xc51dac836bc7841a01c4b631fa620904fc8724d7f9f1d3c420f0e02adf229d50;
    uint8 v = 0x1b;
    bytes32 r = 0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef;
    bytes32 s = 0x7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb4;
    
    
    function verify() public view returns(bool) {
        // Use ECRECOVER to verify address
        return (ecrecover(msgHash, v, r, s) == (addressTest));
    }
}
```

En utilisant le [compilateur et le déploiement Remix](/getting-started/local-node/using-remix/) et avec [MetaMask pointant vers Moonbase Alpha](/getting-started/testnet/metamask/), nous pouvons déployer le contrat et appeler la méthode `verify()` qui retourne _true_ si l'adresse renvoyée par `ecrecover` est égale à l'adresse utilisée pour signer le message (liée à la clé privée et doit être défini manuellement dans le contrat).

## Hachage avec SHA256

Cette fonction de hachage renvoie le hachage SHA256 à partir des données données. Pour tester cette précompilation, vous pouvez utiliser cet [outil en ligne](https://md5calc.com/hash/sha256) pour calculer le hachage SHA256 de la chaîne de votre choix. Dans notre cas, nous le ferons avec `Hello World!`. Nous pouvons nous diriger directement vers Remix et déployer le code suivant, où le hachage calculé est défini pour la variable `expectedHash`:

```solidity
pragma solidity ^0.7.0;

contract Hash256{
    bytes32 public expectedHash = 0x7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069;

    function calculateHash() internal pure returns (bytes32) {
        string memory word = 'Hello World!';
        bytes32 hash = sha256(bytes (word));
        
        return hash;        
    }
    
    function checkHash() public view returns(bool) {
        return (calculateHash() == expectedHash);
    }
}

```
Une fois le contrat déployé, nous pouvons appeler la méthode `checkHash()` qui renvoie _true_ si le hachage retourné par `calculateHash()` est égal au hachage fourni.

## Hachage avec RIPEMD-160

Cette fonction de hachage renvoie un hachage RIPEMD-160 à partir des données données. Pour tester cette précompilation, vous pouvez utiliser cet [outil en ligne](https://md5calc.com/hash/ripemd160) pour calculer le hachage RIPEMD-160 de n'importe quelle chaîne. Dans notre cas, nous le ferons à nouveau avec `Hello World!`. Nous réutiliserons le même code que précédemment, mais utiliserons la fonction `ripemd160`. Notez qu'il renvoie une variable de type `bytes20`:

```solidity
pragma solidity ^0.7.0;

contract HashRipmd160{
    bytes20 public expectedHash = hex'8476ee4631b9b30ac2754b0ee0c47e161d3f724c';

    function calculateHash() internal pure returns (bytes20) {
        string memory word = 'Hello World!';
        bytes20 hash = ripemd160(bytes (word));
        
        return hash;        
    }
    
    function checkHash() public view returns(bool) {
        return (calculateHash() == expectedHash);
    }
}
```
Une fois le contrat déployé, nous pouvons appeler la méthode `checkHash()` qui retourne _true_ si le hachage retourné par `calculateHash()` est égal au hachage fourni.

## La fonction d'identité

Également connue sous le nom de copie de données, cette fonction est un moyen moins coûteux de copier des données en mémoire. Le compilateur Solidity ne le prend pas en charge, il doit donc être appelé avec l'assemblage en ligne. Le [code suivant](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-04-datacopy-data) (adapté à Solidity), peut être utilisé pour appeler ce contrat précompilé. Nous pouvons utiliser cet [outil en ligne](https://web3-type-converter.onbrn.com/) pour obtenir des octets à partir de n'importe quelle chaîne, car il s'agit de l'entrée de la fonction `callDataCopy()`.

```solidity
pragma solidity ^0.7.0;

contract Identity{
    
    bytes public memoryStored;

    function callDatacopy(bytes memory data) public returns (bytes memory) {
    bytes memory result = new bytes(data.length);
    assembly {
        let len := mload(data)
        if iszero(call(gas(), 0x04, 0, add(data, 0x20), len, add(result,0x20), len)) {
            invalid()
        }
    }
    
    memoryStored = result;

    return result;
    }
}
```
Une fois le contrat déployé, nous pouvons appeler la méthode `callDataCopy()` et vérifier si `memoryStored` correspond aux octets que vous passez en tant qu'entrée de la fonction.

## Exponentiation modulaire

Ce cinquième précompilé calcule le reste lorsqu'un entier _b_ (base) est élevé à la puissance _e_-th (l'exposant), et est divisé par un entier positif _m_ (le module).

Le compilateur Solidity ne le prend pas en charge, il doit donc être appelé avec l'assemblage en ligne. Le [code suivant](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x05-bigmodexp-base-exp-mod) a été simplifié pour montrer les fonctionnalités de cette précompilation. 

```solidity
pragma solidity ^0.7.0;

contract ModularCheck {

    uint public checkResult;

    // Function to Verify ModExp Result
    function verify( uint _base, uint _exp, uint _modulus) public {
        checkResult = modExp(_base, _exp, _modulus);
    }

    function modExp(uint256 _b, uint256 _e, uint256 _m) public returns (uint256 result) {
        assembly {
            // Free memory pointer
            let pointer := mload(0x40)
            // Define length of base, exponent and modulus. 0x20 == 32 bytes
            mstore(pointer, 0x20)
            mstore(add(pointer, 0x20), 0x20)
            mstore(add(pointer, 0x40), 0x20)
            // Define variables base, exponent and modulus
            mstore(add(pointer, 0x60), _b)
            mstore(add(pointer, 0x80), _e)
            mstore(add(pointer, 0xa0), _m)
            // Store the result
            let value := mload(0xc0)
            // Call the precompiled contract 0x05 = bigModExp
            if iszero(call(not(0), 0x05, 0, pointer, 0xc0, value, 0x20)) {
                revert(0, 0)
            }
            result := mload(value)
        }
    }
}
```

Vous pouvez essayer ceci dans [Remix](/integrations/remix/). Utilisez la fonction `verify()`, en passant la base, l'exposant et le module. La fonction stockera la valeur dans la variable `checkResult`. 

