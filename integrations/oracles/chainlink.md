---
title: Chainlink
description: Comment utiliser les données de demande d'un Oracle Chainlink dans votre Dapp Ethereum Moonbeam  à l'aide de contrats intelligents ou de Javascript
---

# Oracle Chainlink 

![Chainlink Moonbeam Banner](/images/builders/integrations/oracles/chainlink/chainlink-banner.png)

## Introduction {: #introduction } 

Les développeurs peuvent désormais utiliser [le réseau Oracle décentralisé de Chainlink](https://chain.link/)  pour récupérer des données dans Moonbase Alpha TestNet. Ce tutoriel présente deux manières différentes d'utiliser les oracles Chainlink:

 - [Modèle de demande Basique](https://docs.chain.link/docs/architecture-request-model), dans lequel l'utilisateur final envoie une demande à un fournisseur Oracle, qui récupère les données via une API et répond à la demande en stockant ces données en chaîne
 - [Les flux de prix](https://docs.chain.link/docs/architecture-decentralized-model), où les données sont continuellement mises à jour par les opérateurs Oracle dans un contrat intelligent afin que d'autres contrats intelligents puissent les récupérer

## Modèle de requêtes basique {: #basic-request-model } 

Avant d'aller chercher les données elles-mêmes, il est important de comprendre les bases du "modèle de requêtes basique."

--8<-- 'text/chainlink/chainlink-brm.md'

### Le contrat client {: #the-client-contract } 

Le contrat Client est l'élément qui démarre la communication avec l'Oracle en envoyant une demande. Comme indiqué dans le diagramme, il appelle la méthode _transferAndCall_  à partir du contrat de jeton LINK, mais un traitement supplémentaire est nécessaire pour suivre la demande vers l’oracle. Pour cet exemple, vous pouvez utiliser le code de [ce fichier](/snippets/code/chainlink/Client.sol), et le déployer sur [Remix](/integrations/remix/) pour l'essayer. Examinons les fonctions essentielles du contrat:

 - _constructor_: s'exécute lorsque le contrat est déployé. Il définit l'adresse du jeton LINK et le propriétaire du contrat
 - _requestPrice_: a besoin de l'adresse du contrat Oracle, de l'ID du travail et des jetons de paiement (en LINK) pour répondre à la demande. Génère la nouvelle demande envoyée à l'aide de la fonction _sendChainlinkRequestTo_ à partir de l' importation _ChainlinkClient.sol_ 
 - _fulfill_: rappel utilisé par le nœud Oracle pour répondre à la demande en stockant les informations demandées dans le contrat

```solidity
pragma solidity ^0.6.6;

import "https://github.com/smartcontractkit/chainlink/evm-contracts/src/v0.6/ChainlinkClient.sol";

contract Client is ChainlinkClient {
  //... there is mode code here

  constructor(address _link) public {
    // Set the address for the LINK token for the network
    setChainlinkToken(_link);
    owner = msg.sender;
  }

  function requestPrice(address _oracle, string memory _jobId, uint256 _payment)
    public
    onlyOwner
  {
    // newRequest takes a JobID, a callback address, and callback function as input
    Chainlink.Request memory req = buildChainlinkRequest(stringToBytes32(_jobId), address(this), this.fulfill.selector);
    // Sends the request with the amount of payment specified to the oracle
    sendChainlinkRequestTo(_oracle, req, _payment);
  }

  function fulfill(bytes32 _requestId, uint256 _price)
    public
    recordChainlinkFulfillment(_requestId)
  {
    currentPrice = _price;
  }

  //... there is more code here
}
```

Notez que le contrat Client doit avoir un solde de tokens LINK pour pouvoir payer cette demande. Toutefois, si vous déployez votre configuration, vous pouvez définir la valeur LINK sur 0 dans votre contrat `ChainlinkClient.sol` , mais vous devez toujours avoir le contrat de jeton LINK déployé.

### Essayez-le sur Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Si vous souhaitez éviter les obstacles liés au déploiement de tous les contrats, à la configuration de votre nœud Oracle, à la création d'ID de travail, etc., nous avons tout prévu.

Un contrat client personnalisé sur Moonbase Alpha qui fait toutes les demandes à notre contrat Oracle, avec un paiement de jeton de 0 LINK, est disponible. Ces demandes sont satisfaites par un nœud Oracle que nous exécutons. Vous pouvez l'essayer avec le contrat d'interface suivant et le contrat client personnalisé déployé sur `{{ networks.moonbase.chainlink.client_contract }}`:

```solidity
pragma solidity ^0.6.6;

/**
 * @title Simple Interface to interact with Universal Client Contract
 * @notice Client Address {{ networks.moonbase.chainlink.client_contract }}
 */
interface ChainlinkInterface {

  /**
   * @notice Creates a Chainlink request with the job specification ID,
   * @notice and sends it to the Oracle.
   * @notice _oracle The address of the Oracle contract fixed top
   * @notice _payment For this example the PAYMENT is set to zero
   * @param _jobId The job spec ID that we want to call in string format
   */
    function requestPrice(string calldata _jobId) external;

    function currentPrice() external view returns (uint);

}
```

Cela fournit deux fonctions. `requestPrice()` n'a besoin que de l'ID de tâche des données que vous souhaitez interroger. Cette fonction démarre la chaîne d'événements expliquée précédemment. `currentPrice()` est une fonction de vue qui renvoie le dernier prix stocké dans le contrat.

Actuellement, le nœud Oracle dispose d'un ensemble d'ID de tâche pour différentes données de prix pour les paires suivantes:

|  Base/Quote  |     |                 Job ID Reference                  |
| :----------: | --- | :-----------------------------------------------: |
|  BTC à USD  |     |  {{ networks.moonbase.chainlink.basic.btc_usd }}  |
|  ETH à USD  |     |  {{ networks.moonbase.chainlink.basic.eth_usd }}  |
|  DOT à USD  |     |  {{ networks.moonbase.chainlink.basic.dot_usd }}  |
|  KSM à USD  |     |  {{ networks.moonbase.chainlink.basic.ksm_usd }}  |
| AAVE à USD  |     | {{ networks.moonbase.chainlink.basic.aave_usd }}  |
| ALGO à USD  |     | {{ networks.moonbase.chainlink.basic.algo_usd }}  |
| BAND à USD  |     | {{ networks.moonbase.chainlink.basic.band_usd }}  |
| LINK à USD  |     | {{ networks.moonbase.chainlink.basic.link_usd }}  |
| SUSHI à USD |     | {{ networks.moonbase.chainlink.basic.sushi_usd }} |
|  UNI à USD  |     |  {{ networks.moonbase.chainlink.basic.uni_usd }}  |

Allons-y et utilisons le contrat d'interface avec le jobID `BTC to USD` dans [Remix](/integrations/remix/).

Après avoir créé le fichier et compilé le contrat, dirigez-vous vers l'onglet "Déployer et exécuter les transactions" , entrez l'adresse du contrat du client et cliquez sur "À l'adresse." Assurez-vous d'avoir réglé "Environnement" sur "Injected Web3" pour être connecté à Moonbase Alpha. Cela créera une instance du contrat client avec laquelle vous pourrez interagir. Utilisez la fonction `requestPrice()` pour interroger les données de l'ID de tâche correspondant. Une fois la transaction confirmée, nous devons attendre que tout le processus expliqué auparavant se produise. Nous pouvons vérifier le prix en utilisant la fonction de visualisation `currentPrice()`.

![Chainlink Basic Request on Moonbase Alpha](/images/builders/integrations/oracles/chainlink/chainlink-1.png)

S'il y a une paire spécifique que vous souhaitez que nous incluions, n'hésitez pas à nous contacter via notre [Serveur Discord](https://discord.com/invite/PfpUATX).

### Exécutez votre contrat client {: #run-your-client-contract } 

Si vous souhaitez exécuter votre contrat Client mais utiliser notre nœud Oracle, vous pouvez le faire avec les informations suivantes :

|  Type de contrat  |     |                      Addresse                      |
| :-------------: | --- | :-----------------------------------------------: |
| Contrat Oracle |     | {{ networks.moonbase.chainlink.oracle_contract }} |
|   Jeton LINK    |     |  {{ networks.moonbase.chainlink.link_contract }}  |

N'oubliez pas que le paiement du jeton LINK est défini sur zéro.

### Autres demandes {: #other-requests } 

Les oracles de Chainlink peuvent provisoirement fournir de nombreux types différents de flux de données à l'aide d'adaptateurs externes. Cependant, pour plus de simplicité, notre nœud Oracle est configuré pour ne fournir que des flux de prix.

Si vous souhaitez exécuter votre propre nœud Oracle dans Moonbeam, veuillez consulter [ce guide](/node-operators/oracles/node-chainlink/). Nous vous recommandons également de consulter  [le site de documentation Chainlink](https://docs.chain.link/docs).

## Flux de prix {: #price-feeds } 

Avant d'aller chercher les données elles-mêmes, il est important de comprendre les bases des flux de prix.

Dans une configuration standard, chaque flux de prix est mis à jour par un réseau Oracle décentralisé. Chaque nœud Oracle est récompensé pour la publication des données de prix dans le contrat Aggregator. Cependant, les informations ne sont mises à jour que si un nombre minimum de réponses des nœuds Oracle est reçu (lors d'un cycle d'agrégation).

L'utilisateur final peut récupérer les flux de prix avec des opérations en lecture seule via un contrat consommateur, en référençant la bonne interface d'agrégateur (contrat proxy). Le proxy agit comme un middleware pour fournir au consommateur l'agrégateur le plus à jour pour un flux de prix particulier.

![Price Feed Diagram](/images/builders/integrations/oracles/chainlink/chainlink-price-feed.png)

### Essayez-le sur Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Si vous souhaitez éviter la complexité liée au déploiement de tous les contrats, à la configuration de votre nœud Oracle, à la création d'ID de tâche, etc., nous avons tout prévu.

Nous avons déployé tous les contrats nécessaires sur Moonbase Alpha pour simplifier le processus de demande de flux de prix. Dans notre configuration actuelle, nous n'exécutons qu'un seul nœud Oracle qui récupère le prix à partir d'une seule source d'API. Les données de prix sont vérifiées toutes les minutes et mises à jour dans les contrats intelligents toutes les heures, sauf en cas d'écart de prix de 1%.

Les données vivent dans une série de contrats intelligents (un par flux de prix) et peuvent être récupérées avec l'interface suivante:

```solidity
pragma solidity ^0.6.6;

interface ConsumerV3Interface {
    /**
     * Returns the latest price
     */
    function getLatestPrice() external view returns (int);

    /**
     * Returns the decimals to offset on the getLatestPrice call
     */
    function decimals() external view returns (uint8);

    /**
     * Returns the description of the underlying price feed aggregator
     */
    function description() external view returns (string memory);
}
```

Cela fournit trois fonctions. `getLatestPrice()` lira les dernières données de prix disponibles dans le contrat d'agrégation via le proxy. Nous avons ajouté la fonction `decimals()` , qui renvoie le nombre de décimales des données et la fonction `description()` , qui renvoie une brève description de l'alimentation de prix disponible dans le contrat d'agrégateur interrogé.

Actuellement, il existe un contrat consommateur pour les paires de prix suivantes:

|  Base/Quote  |     |                     Consumer Contract                     |
| :----------: | --- | :-------------------------------------------------------: |
|  BTC à USD  |     |  {{ networks.moonbase.chainlink.feed.consumer.btc_usd }}  |
|  ETH à USD  |     |  {{ networks.moonbase.chainlink.feed.consumer.eth_usd }}  |
|  DOT à USD  |     |  {{ networks.moonbase.chainlink.feed.consumer.dot_usd }}  |
|  KSM à USD  |     |  {{ networks.moonbase.chainlink.feed.consumer.ksm_usd }}  |
| AAVE à USD  |     | {{ networks.moonbase.chainlink.feed.consumer.aave_usd }}  |
| ALGO à USD  |     | {{ networks.moonbase.chainlink.feed.consumer.algo_usd }}  |
| BAND à USD  |     | {{ networks.moonbase.chainlink.feed.consumer.band_usd }}  |
| LINK à USD  |     | {{ networks.moonbase.chainlink.feed.consumer.link_usd }}  |
| SUSHI à USD |     | {{ networks.moonbase.chainlink.feed.consumer.sushi_usd }} |
|  UNI à USD  |     |  {{ networks.moonbase.chainlink.feed.consumer.uni_usd }}  |

Allons-y et utilisons le contrat d'interface pour récupérer le flux de prix de `BTC à USD` en utilisant [Remix](/integrations/remix/).

Après avoir créé le fichier et compilé le contrat, dirigez-vous vers l'onglet "Déployer et exécuter les transactions" , entrez l'adresse du contrat consommateur correspondant à `BTC à USD`, puis cliquez sur "À l'adresse." Assurez-vous d'avoir réglé "Environnement" sur "Injected Web3" pour être connecté à Moonbase Alpha.

Cela créera une instance du contrat consommateur avec laquelle vous pourrez interagir. Utilisez la fonction `getLatestPrice()` pour interroger les données du flux de prix correspondant.

![Chainlink Price Feeds on Moonbase Alpha](/images/builders/integrations/oracles/chainlink/chainlink-2.png)

Notez que pour obtenir le prix réel, vous devez tenir compte des décimales du flux de prix, disponible avec la méthode `decimals()` .

S'il y a une paire spécifique que vous souhaitez que nous incluions, n'hésitez pas à nous contacter via notre [Serveur Discord](https://discord.com/invite/PfpUATX).
