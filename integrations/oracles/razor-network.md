---
title: Razor Network
description: Comment utiliser les données de demande d'un Oracle Razor Network dans votre DApp Ethereum Moonbeam à l'aide de contrats intelligents
---
# Oracle Razor Network

![Razor Network Moonbeam Diagram](/images/razor/razor-banner.png)

## Introduction {: #introduction } 

Les développeurs peuvent désormais récupérer les prix de l'oracle de Razor Network en utilisant un contrat Bridge déployé sur Moonbase Alpha TestNet. Ce Bridge agit comme un middleware et les événements émis par celui-ci sont récupérés par l'infrastructure oracle du Razor Network, envoyant les prix au contrat Bridge.

Pour accéder à ces flux de prix, nous devons interagir avec l'adresse du contrat Bridge, qui se trouve dans le tableau suivant:

|     Réseau    | |         Addresss du contrat        |
|:--------------:|-|:------------------------------------------:|
| Moonbase Alpha | | 0x53f7660Ea48289B5DA42f1d79Eb9d4F5eB83D3BE |

## Emplois {: #jobs } 

Chaque flux de données est associé à un ID de tâche. Par exemple:

|    Job ID    | |    Prix sous-jacent [USD]  |
|:------------:|-|:--------------------------:|
|       1      | |            ETH             |
|       2      | |            BTC             |
|       3      | |      Actions Microsoft      |

Vous pouvez vérifier les jobs ID pour chaque flux de données sur ce [lien](https://razorscan.io/#/custom). Les flux de prix sont mis à jour toutes les 5 minutes. Pour plus d'informations, consultez le [site Web de documentation de Razor][https://docs.razor.network/].

## Obtenir les données du contrat Bridge {: #get-data-from-bridge-contract } 

Les contrats peuvent interroger des données en chaîne, telles que les prix des jetons, à partir de l'oracle de Razor Network en implémentant l'interface du contrat Bridge, qui expose les fonctions `getResult` et `getJob` .

```solidity
pragma solidity 0.6.11;

interface Razor {
    
    function getResult(uint256 id) external view returns (uint256);
    
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}
```

La première fonction, `getResult`, prend le jobID  associé au flux de données et récupère le prix. Par exemple, si nous transmettons `1`, nous recevrons le prix du flux de données lié au Job ID.

La deuxième fonction, `getJob`, prend le jobID associé au flux de données et récupère les informations générales concernant le flux de données, telles que le nom du flux de données, le prix et l'URL utilisée pour récupérer les prix.

### Exemple de contrat {: #example-contract } 

Nous avons déployé le contrat Bridge dans le Moonbase Alpha TestNet (à l'adresse `{{ networks.moonbase.razor.bridge_address }}`) afin que vous puissiez vérifier rapidement les informations fournies par l'oracle de Razor Network. 

La seule exigence est l'interface Bridge, qui définit la structure `getResult` et met les fonctions à la disposition du contrat pour les requêtes.


Nous pouvons utiliser le script `Demo` suivant . Il offre diverses fonctions:

 - fetchPrice: une fonction _view_ qui interroge un seul jobID . Par exemple, pour récupérer le prix de `ETH` en `USD`, nous devrons envoyer le Job ID `1`
 - fetchMultiPrices: une fonction _view_ qui interroge plusieurs jobIDs. Par exemple, pour récupérer le prix de `ETH` et `BTC` en `USD`, Nous devrons envoyer les Job IDs `[1,2]`
 - savePrice: une fonction _public_ qui interroge un seul jobID . Cela envoie une transaction et modifie la variable `price` stockée dans le contrat.
 - saveMultiPrices: une fonction _public_ qui interroge plusieurs jobIDs. Par exemple, pour récupérer le prix de `ETH` et `BTC` en `USD`, nous devrons envoyer les Job IDs `[1,2]`. Cela envoie une transaction et modifie le tableau `pricesArr` stocké dans le contrat, qui contiendra le prix de chaque paire dans le même ordre que spécifié dans l'entrée

```solidity
pragma solidity 0.6.11;

interface Razor {
    function getResult(uint256 id) external view returns (uint256);
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}

contract Demo {
    // Interface
    Razor internal razor;
    
    // Variables
    uint256 public price;
    uint256[] public pricesArr;

    constructor(address _bridgeAddress) public {
        razor = Razor(_bridgeAddress); // Bridge Contract Address
                                       // Moonbase Alpha {{ networks.moonbase.razor.bridge_address }}
    }

    function fetchPrice(uint256 _jobID) public view returns (uint256){
        return razor.getResult(_jobID);
    }
    
    function fetchMultiPrices(uint256[] memory jobs) external view returns(uint256[] memory){
        uint256[] memory prices = new uint256[](jobs.length);
        for(uint256 i=0;i<jobs.length;i++){
            prices[i] = razor.getResult(jobs[i]);
        }
        return prices;
    }
    
    function savePrice(uint _jobID) public {
        price = razor.getResult(_jobID);
    }

    function saveMultiPrices(uint[] calldata _jobIDs) public {
        delete pricesArr;
        
        for (uint256 i = 0; i < _jobIDs.length; i++) {
            pricesArr.push(razor.getResult(_jobIDs[i]));
        }

    }
}
```

### Essayez-le sur Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Le moyen le plus simple d'essayer l’implémentation Oracle consiste à pointer l'interface vers le contrat Bridge déployé à l'adresse `{{ networks.moonbase.razor.bridge_address }}`:

```sol
pragma solidity 0.6.11;

interface Razor {
    function getResult(uint256 id) external view returns (uint256);
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}
```

Avec lui, vous aurez deux fonctions d'affichage disponibles, très similaires à nos exemples précédents:

 - getPrice: fournit le flux de prix pour un seul jobID donné en entrée de la fonction. Par exemple, pour récupérer le prix de `ETH` en `USD`,  nous devrons envoyer le Job ID `1`
 - getMultiPrices: fournit les prix pour plusieurs jobIDs donnés en entrée de tableau pour la fonction. Par exemple, pour récupérer le prix de `ETH` et `BTC` en `USD`, nous devrons envoyer les ID de travail `[1,2]`

Utilisons [Remix](/integrations/remix/) pour récupérer le prix `BTC` en `USD`.

Après avoir créé le fichier et compiler le contrat, dirigez-vous vers l'onglet "Déployer et exécuter les transactions" , entrez l'adresse du contrat (`{{ networks.moonbase.razor.bridge_address }}`), puis cliquez sur "À l'adresse." Assurez-vous que vous avez réglé "Environnement" sur "Web3 injecté" afin que vous soyez connecté à Moonbase Alpha (via le fournisseur Web3 du portefeuille). 

![Razor Remix deploy](/images/razor/razor-demo1.png)

Cela créera une instance du contrat de démonstration avec laquelle vous pourrez interagir. Utilisez les fonctions `getPrice()` et `getMultiPrices()` pour interroger les données de la paire correspondante.

![Razor check price](/images/razor/razor-demo2.png)

## Nous contacter {: #contact-us } 
Si vous avez des commentaires concernant la mise en œuvre de l'Oracle Razor Network  sur votre projet ou tout autre sujet lié à Moonbeam, n'hésitez pas à nous contacter via le canal developpement officiel de notre. [Serveur Discord](https://discord.com/invite/PfpUATX).
