---
title: Protocole Band
description: Comment utiliser les données de requètes d'un Oracle Band Protocol dans votre DApp Moonbeam Ethereum à l'aide de contrats intelligents ou de javascript
---
# Oracle Band Protocole 

![Band Protocol Moonbeam Diagram](/images/band/band-banner.png)

## Introduction {: #introduction } 
Les développeurs ont deux manières différentes d'obtenir les prix à partir de l'infrastructure oracle du protocole Band. D'une part, ils peuvent utiliser les contrats intelligents de Band sur Moonbeam. Ce faisant, ils accèdent aux données qui sont en chaîne et sont mises à jour à intervalles réguliers ou lorsque le glissement de prix est supérieur à un montant cible (différent pour chaque jeton). D'autre part, les développeurs peuvent utiliser la bibliothèque d'assistance Javascript, qui utilise un point de terminaison d'API pour récupérer les données en utilisant des fonctions similaires à celles des contrats intelligents, mais cette implémentation contourne complètement la blockchain. Cela peut être utile si votre DApp a besoin d'un accès direct aux données.

L'adresse du contrat d'agrégation se trouve dans le tableau suivant:

|     Réseau    | |         Adresse du contrat de l'agrégateur        |
|:--------------:|-|:------------------------------------------:|
| Moonbase Alpha | | 0xDA7a001b254CD22e46d3eAB04d937489c93174C3 |

## Jeton pris en charge  {: #supported-token } 
Les demandes de prix avec n'importe quelle dénomination sont disponibles tant que les symboles de base et de cotation sont pris en charge (_base_/_quote_). Par exemple

 - `BTC/USD`
 - `BTC/ETH`
 - `ETH/EUR`

Au moment de la rédaction de cet article, la liste des symboles pris en charge peut être trouvée en suivant [ce lien](https://data.bandprotocol.com). Il y a plus de 146 paires de prix disponibles.

## Interroger les prix {: #querying-prices } 
Comme indiqué précédemment, les développeurs peuvent utiliser deux méthodes pour interroger les prix depuis l'oracle de Band protocole: 

 - Contrat intelligent de Band sur Moonbeam (déployé sur Moonbase Alpha TestNet pour le moment)
 - Bibliothèque d'aide Javascript

## Obtenir des données à l'aide de contrats intelligents {: #get-data-using-smart-contracts } 
Les contrats peuvent interroger des données en chaîne, telles que les prix des jetons, à partir de l'oracle de Band en implémentant l'interface du contrat `StdReference` qui expose les fonctions `getReferenceData` et `getReferenceDataBulk` .

La première fonction, `getReferenceData`, prend deux chaînes (la base et le symbole des guillemets) comme entrées. La fonction interroge le contrat `StdReference`  pour connaître les derniers tarifs disponibles pour ces deux jetons. Il renvoie une structure `ReferenceData` .

La structure `ReferenceData` comprend les éléments suivants:

 - Taux: le taux de change en termes de _base/quote_. La valeur renvoyée est multipliée par 10<sup>18</sup>
 - Dernière mise à jour de la base: la dernière fois que le prix de base a été mis à jour (depuis l'époque UNIX)
 - Dernier devis mis à jour: la dernière fois que le prix indiqué a été mis à jour (depuis l'époque UNIX)
 
```js
struct ReferenceData {
   uint256 rate; 
   uint256 lastUpdatedBase; 
   uint256 lastUpdatedQuote;
}
```

La deuxième fonction, `getReferenceDataBulk`, prend les informations sous forme de tableaux de données. Par exemple, si nous passons en base `['BTC','BTC','ETH']` et en cotation  `['USD','ETH','EUR']`, le tableau `ReferenceData` retourné contient les informations concernant les paires suivantes:

 - `BTC/USD`
 - `BTC/ETH`
 - `ETH/EUR`

### Exemple de contrat {: #example-contract } 

Le code de contrat intelligent suivant fournit quelques exemples simples du contrat `StdReference` et de la fonction `getReferenceData` -  ceux-ci ne sont pas destinés à la production. L'interface `IStdReference.sol` définit la structure ReferenceData et les fonctions disponibles pour effectuer les requêtes.

```solidity
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

interface IStdReference {
    /// A structure returned whenever someone requests for standard reference data.
    struct ReferenceData {
        uint256 rate; // base/quote exchange rate, multiplied by 1e18.
        uint256 lastUpdatedBase; // UNIX epoch of the last time when base price gets updated.
        uint256 lastUpdatedQuote; // UNIX epoch of the last time when quote price gets updated.
    }

    /// Returns the price data for the given base/quote pair. Revert if not available.
    function getReferenceData(string memory _base, string memory _quote)
        external
        view
        returns (ReferenceData memory);

    /// Similar to getReferenceData, but with multiple base/quote pairs at once.
    function getReferenceDataBulk(string[] memory _bases, string[] memory _quotes)
        external
        view
        returns (ReferenceData[] memory);
}
```
Ensuite, nous pouvons utiliser le script `DemoOracle` Il fournit quatre fonctions:

 - getPrice: une fonction _view_ qui interroge une seule base. Dans cet exemple, le prix du `BTC` coté en `USD`
 - getMultiPrices: une fonction _view_ qui interroge plusieurs bases. Dans cet exemple, le prix de `BTC` et `ETH`, tous deux cotés en `USD`
 - savePrice: une fonction _public_ qui interroge la paire _base/quote_ . Chaque élément est fourni sous forme de chaînes distinctes, par exemple `_base = "BTC", _quotes = "USD"`. Cela envoie une transaction et modifie la variable `price` stockée dans le contrat
 - saveMultiPrices: une fonction _public_  qui interroge chaque paire _base/quote_ . Chaque élément est fourni sous forme de tableau de chaînes. Par exemple `_bases = ["BTC","ETH"], _quotes = ["USD","USD"]`. Cela envoie une transaction et modifie le tableau `prices` stocké dans le contrat, qui contiendra le prix de chaque paire dans le même ordre que spécifié dans l'entrée

 Lorsqu'elle est déployée, la fonction constructeur a besoin de l'adresse du contrat d'agrégation pour le réseau cible.

```solidity
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

import "./IStdReference.sol";

contract DemoOracle {
    IStdReference ref;
    
    uint256 public price;
    uint256[] public pricesArr;

    constructor(IStdReference _ref) public {
        ref = _ref; // Aggregator Contract Address
                    // Moonbase Alpha 0xDA7a001b254CD22e46d3eAB04d937489c93174C3

    }

    function getPrice(string memory _base, string memory _quote) external view returns (uint256){
        IStdReference.ReferenceData memory data = ref.getReferenceData(_base,_quote);
        return data.rate;
    }

    function getMultiPrices(string[] memory _bases, string[] memory _quotes) external view returns (uint256[] memory){
        IStdReference.ReferenceData[] memory data = ref.getReferenceDataBulk(_bases,_quotes);

        uint256 len = _bases.length;
        uint256[] memory prices = new uint256[](len);
        for (uint256 i = 0; i < len; i++) {
            prices[i] = data[i].rate;
        }

        return prices;
    }
    
    function savePrice(string memory _base, string memory _quote) external {
        IStdReference.ReferenceData memory data = ref.getReferenceData(_base,_quote);
        price = data.rate;
    }
    
    function saveMultiPrices(
        string[] memory _bases,
        string[] memory _quotes
    ) public {
        require(_bases.length == _quotes.length, "BAD_INPUT_LENGTH");
        uint256 len = _bases.length;
        IStdReference.ReferenceData[] memory data = ref.getReferenceDataBulk(_bases,_quotes);
        delete pricesArr;
        for (uint256 i = 0; i < len; i++) {
            pricesArr.push(data[i].rate);
        }
        
    }
}
```

### Essayez-le dans Moonbase Alpha {: #try-it-in-moonbase alpha } 

Nous avons déployé un contrat disponible dans Moonbase Alpha TestNet (à l'adresse `0xf15c870344c1c02f5939a5C4926b7cDb90dEc655`) afin que vous puissiez facilement vérifier les informations fournies par l'oracle de Band Protocol. Pour ce faire, vous avez besoin du contrat d'interface suivant:

```solidity
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

interface TestInterface {
    function getPrice(string memory _base, string memory _quote) external view returns (uint256);

    function getMultiPrices(string[] memory _bases, string[] memory _quotes) external view returns (uint256[] memory);
}
```

Avec lui, vous aurez deux fonctions d'affichage disponibles - très similaires à nos exemples précédents:

 - getPrice: fournit le flux de prix pour une seule paire base/quote qui est donnée en entrée à la fonction, c'est-à-dire "BTC", "USD"
 - getMultiPrices: fournit le flux de prix pour plusieurs paires base/quote pairs qui sont données en entrée de la fonction, c'est-à-dire, ["BTC", "ETH", "ETH"], ["USD", "USD", "EUR"]

Par exemple, en utilisant [Remix](/integrations/remix/), nous pouvons facilement interroger la paire de prix `BTC/USD` en utilisant cette interface.

Après avoir créé le fichier et compiler le contrat, dirigez-vous vers l'onglet "Deploy and Run Transactions" , entrez l'adresse du contrat (`0xf15c870344c1c02f5939a5C4926b7cDb90dEc655`) et cliquez sur "At Address." Assurez-vous d'avoir réglé "Environment" sur "Injected Web3" pour être connecté à Moonbase Alpha. 

![Band Protocol Remix deploy](/images/band/band-demo1.png)

Cela créera une instance du contrat de démonstration avec laquelle vous pourrez interagir. Utilisez les fonctions `getPrice()` et `getMultiPrices()` pour interroger les données de la paire correspondante.

![Band Protocol Remix check price](/images/band/band-demo2.png)

## Bibliothèque JavaScript d'aide BandChain.js {: #bandchainjs-javascript-helper-library } 

La bibliothèque d'assistance prend également en charge une fonction similaire `getReferenceData` . Pour commencer, la bibliothèque doit être installée:

```
npm install @bandprotocol/bandchain.js
```

La bibliothèque fournit une fonction de constructeur qui nécessite un point de terminaison vers lequel pointer. Cela renvoie une instance qui active ensuite toutes les méthodes nécessaires, telles que la fonction `getReferenceData` .  Lors de la recherche d'informations, la fonction accepte un tableau où chaque élément est la paire _base/quote_  nécessaire. Par example:

```
getReferenceData(['BTC/USD', 'BTC/ETH', 'ETH/EUR'])
```

Ensuite, il renvoie un objet tableau avec la structure suivante:

```js
[
  {
    pair: 'BTC/USD',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  },
  {
    pair: 'BTC/ETH',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  },
  {
    pair: 'ETH/EUR',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  }
]
```
Ou `lastUpdatedBase` et `lastUpdatedQuote` sont la dernière fois où les prix de base et de cotation ont été mis à jour respectivement (depuis l'époque UNIX).

### Exemple d'utilisation {: #example-usage } 

Le script Javascript suivant fournit un exemple simple de la fonction `getReferenceData` .

```js
const BandChain = require('@bandprotocol/bandchain.js');

const queryData = async () => {
  const endpoint = 'https://poa-api.bandchain.org';

  const bandchain = new BandChain(endpoint);
  const dataQuery = await bandchain.getReferenceData(['BTC/USD', 'BTC/ETH', 'ETH/EUR']);
  console.log(dataQuery);
};

queryData();
```

Nous pouvons exécuter ce code avec un nœud, et la sortie suivante `dataQuery` devrait ressembler à ceci:

![Band Protocol JavaScript Library](/images/band/band-console.png)

Notez que par rapport à la demande effectuée via des contrats intelligents, le résultat est donné directement dans les bonnes unités.
