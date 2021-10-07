---
title: Abonnement aux événements
description: Utilisez la fonctionnalité de publication-abonnement de type Ethereum pour vous abonner à des événements spécifiques sur la chaîne compatible Ethereum de Moonbeam.
---

# Abonnez-vous aux événements dans Moonbase Alpha

## Introduction {: #introduction } 
La possibilité de s'abonner à des événements de style Ethereum a été ajoutée avec la [sortie de Moonbase Alpha v2](https://moonbeam.network/announcements/testnet-upgrade-moonbase-alpha-v2/). Dans ce guide, nous décrirons les types d'abonnement disponibles et les limitations actuelles.

## Vérification des prérequis {: #checking-prerequisites } 
Les exemples de ce guide sont basés sur un environnement Ubuntu 18.04. Vous aurez également besoin des éléments suivants:

 - Avoir MetaMask installé et [connecté à Moonbase](/getting-started/testnet/metamask/)
 - Avoir un compte avec des fonds. Vous pouvez l'obtenir auprès de [Mission Control](/getting-started/testnet/faucet/)
 - Déployez votre propre jeton ERC-20 sur Moonbase. Vous pouvez suivre [notre tutoriel Remix](/getting-started/local-node/using-remix/), en pointant d'abord MetaMask vers Moonbase

--8<-- 'text/common/install-nodejs.md'

Au moment de la rédaction de ce guide, les versions utilisées étaient respectivement 14.6.0 et 6.14.6. Nous devrons également installer le package Web3 en exécutant :

```
npm install --save web3
```

Pour vérifier la version installée de Web3, vous pouvez utiliser la commande `ls`:

```
npm ls web3
```

Au moment de la rédaction de ce guide, la version utilisée était la 1.3.0. 

## Abonnement aux journaux d'événements dans Moonbase Alpha {: #subscribing-to-event-logs-in-moonbase-alpha } 
Tout contrat qui suit le standard de jeton ERC-20 émet un événement lié à un transfert de jetons, c'est-à-dire, `event Transfer(address indexed from, address indexed to, uint256 value)`. Pour cet exemple, nous nous abonnerons aux journaux de tels événements. En utilisant la bibliothèque web3.js, nous avons besoin du morceau de code suivant:

```js
const Web3 = require('web3');
const web3 = new Web3('wss://wss.testnet.moonbeam.network');

web3.eth.subscribe('logs', {
    address: 'ContractAddress',
    topics: ['0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef']
}, (error, result) => {
    if (error)
        console.error(error);
})
    .on("connected", function (subscriptionId) {
        console.log(subscriptionId);
    })
    .on("data", function (log) {
        console.log(log);
    });
```

Notez que nous nous connectons au point de terminaison WebSocket de Moonbase Alpha. Nous utilisons la méthode `web3.eth.subscribe(‘logs’,  options [, callback])` pour nous abonner aux journaux, filtrés par les options données. Dans notre cas, les options sont l'adresse du contrat d'où les événements sont émis et les rubriques utilisées pour décrire l'événement. Plus d'informations sur les sujets peuvent être trouvées dans ce [cet article Medium](https://medium.com/mycrypto/understanding-event-logs-on-the-ethereum-blockchain-f4ae7ba50378). Si aucun sujet n'est inclus, vous vous abonnez à tous les événements émis par le contrat. Afin de filtrer uniquement l'événement de transfert, nous devons inclure la signature de l'événement, calculée comme suit:

```js
EventSignature = keccak256(Transfer(address,address,uint256))
```

Le résultat du calcul est affiché dans l'extrait de code précédent. Nous reviendrons sur le filtrage par thèmes plus tard. Le reste du code gère la fonction de rappel. Une fois que nous exécutons ce code, nous obtiendrons un identifiant d'abonnement et le terminal attendra tout événement via cet abonnement:

![Subscription ID](/images/builders/tools/pubsub/pubsub-1.png)

Ensuite, un transfert de jeton ERC-20 sera envoyé avec les paramètres suivants:

 - From address: 0x44236223aB4291b93EEd10E4B511B37a398DEE55
 - To address: 0x8841701Dba3639B254D9CEe712E49D188A1e941e
 - Value (tokens): 1000000000000000000 - soit 1 avec 18 zéros

Une fois la transaction envoyée, le journal de l'événement émis par la transaction apparaîtra dans le terminal:

![Log of the transfer event](/images/builders/tools/pubsub/pubsub-2.png)

Décomposons la réponse reçue. Notre événement cible envoie deux informations indexées: les adresses `from` et `to` (dans cet ordre), qui sont traitées comme des sujets. L'autre donnée partagée par notre événement est le nombre de jetons, qui n'est pas indexé. Par conséquent, il y a un total de trois sujets (le maximum est de quatre), qui correspondent à l'opcode LOG3:

![Description of LOG3](/images/builders/tools/pubsub/pubsub-3.png)

Par conséquent, vous pouvez voir que les adresses `from` et `to` sont contenues dans les rubriques renvoyées par les journaux. Les adresses Ethereum ont une longueur de 40 caractères hexadécimaux (1 caractère hexadécimal équivaut à 4 bits, donc 160 bits ou format H160). Ainsi, les 24 zéros supplémentaires sont nécessaires pour combler le vide avec H256, qui comporte 64 caractères hexadécimaux. 

Les données non indexées sont renvoyées dans le champ `data` des journaux, mais elles sont codées en octets32 / hex. Pour le décoder, nous pouvons utiliser, par exemple, cet [outil en ligne](https://web3-type-converter.onbrn.com/),  et vérifier que la `data` est en fait 1 (plus 18 zéros). 

Si l'événement renvoie plusieurs valeurs non indexées, elles seront ajoutées l'une après l'autre dans le même ordre que l'événement les émet. Par conséquent, chaque valeur est ensuite obtenue en déconstruisant les données en morceaux séparés de 32 octets (ou 64 caractères hexadécimaux).

### Utilisation de caractères génériques et de mise en forme conditionnelle {: #using-wildcards-and-conditional-formatting } 
Dans la version v2 qui a introduit la fonctionnalité d'abonnement aux journaux, il y avait certaines limitations concernant l'utilisation de caractères génériques et la mise en forme conditionnelle pour les rubriques. Néanmoins, avec la sortie de [Moonbase Alpha v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/), c'est désormais possible.

En utilisant le même exemple que dans la section précédente, abonnons-nous aux événements du contrat de jeton avec le code suivant:

```js
const Web3 = require('web3');
const web3 = new Web3('wss://wss.testnet.moonbeam.network');

web3.eth
   .subscribe(
      'logs',
      {
         address: 'ContractAddress',
         topics: [
            null,
            [
               '0x00000000000000000000000044236223aB4291b93EEd10E4B511B37a398DEE55',
               '0x0000000000000000000000008841701Dba3639B254D9CEe712E49D188A1e941e',
            ],
         ],
      },
      (error, result) => {
         if (error) console.error(error);
      }
   )
   .on('connected', function (subscriptionId) {
      console.log(subscriptionId);
   })
   .on('data', function (log) {
      console.log(log);
   });
```

Ici, en utilisant le caractères générique null  en place pour la signature d'événement, nous filtrons pour écouter tous les événements émis par le contrat auquel nous avons souscrit. Mais avec cette configuration, nous pouvons également utiliser un deuxième champ de saisie (`topic_1`) pour définir un filtre par adresse comme mentionné précédemment. Dans le cas de notre abonnement, nous vous informons que nous souhaitons recevoir uniquement les événements où `topic_1` est l'une des adresses que nous fournissons. Notez que les adresses doivent être au format H256. Par exemple, l'adresse `0x44236223aB4291b93EEd10E4B511B37a398DEE55` doit être saisie sous la forme `0x00000000000000000000000044236223aB4291b93EEd10E4B511B37a398DEE55`. Comme précédemment, la sortie de cet abonnement affichera la signature de l'événement `topic_0` pour nous indiquer quel événement a été émis par le contrat.

![Conditional Subscription](/images/builders/tools/pubsub/pubsub-4.png)

Comme indiqué, après avoir fourni les deux adresses avec un formatage conditionnel, nous avons reçu deux journaux avec le même ID d'abonnement. Les événements émis par les transactions à partir d'adresses différentes ne généreront aucun journal pour cet abonnement.

Cet exemple a montré comment nous pourrions nous abonner uniquement aux journaux d'événements d'un contrat spécifique, mais la bibliothèque web3.js fournit d'autres types d'abonnement que nous examinerons dans les sections suivantes.

## Abonnez-vous aux transactions entrantes en attente {: #subscribe-to-incoming-pending-transactions } 
Afin de souscrire aux transactions en attente, nous pouvons utiliser la méthode `web3.eth.subscribe(‘pendingTransactions’, [, callback])` , implémentant la même fonction de rappel pour vérifier la réponse. Ceci est beaucoup plus simple que notre exemple précédent et renvoie le hachage de transaction des transactions en attente.

![Subscribe pending transactions response](/images/builders/tools/pubsub/pubsub-5.png)

Nous pouvons vérifier que ce hachage de transaction est le même que celui affiché dans MetaMask (ou Remix).

## Abonnez-vous aux en-têtes de bloc entrants {: #subscribe-to-incoming-block-headers } 
Un autre type disponible dans la bibliothèque Web3.js consiste à s'abonner à de nouveaux en-têtes de bloc. Pour ce faire, nous utilisons la méthode `web3.eth.subscribe('newBlockHeaders' [, callback])` , implémentant la même fonction de rappel pour vérifier la réponse. Cet abonnement fournit des en-têtes de bloc entrants et peut être utilisé pour suivre les changements dans la blockchain.

![Subscribe to block headers response](/images/builders/tools/pubsub/pubsub-6.png)

Notez qu'un seul en-tête de bloc est affiché dans l'image. Ces messages sont affichés pour chaque bloc produit afin qu'ils puissent remplir le terminal assez rapidement.

## Vérifiez si un nœud est synchronisé avec le réseau {: #check-if-a-node-is-synchronized-with-the-network } 
Avec pub / sub, il est également possible de vérifier si un nœud particulier auquel vous êtes abonné est actuellement synchronisé avec le réseau. Pour cela, nous pouvons tirer parti de la méthode `web3.eth.subscribe(‘syncing' [, callback])` , en implémentant la même fonction de rappel pour vérifier la réponse. Cet abonnement renverra un objet lorsque le nœud est synchronisé avec le réseau.

![Subscribe to syncing response](/images/builders/tools/pubsub/pubsub-7.png)

## Limitations actuelles {: #current-limitations } 
L'implémentation pub / sub dans [Frontier](https://github.com/paritytech/frontier) est toujours en développement actif. Cette première version permet aux développeurs DApp (ou aux utilisateurs en général) de s'abonner à des types d'événements spécifiques, mais il existe encore quelques limitations. Vous avez peut-être remarqué dans les exemples précédents que certains des champs n'affichent pas les informations appropriées avec la version actuelle publiée, et c'est parce que certaines propriétés ne sont pas encore prises en charge par Frontier.



