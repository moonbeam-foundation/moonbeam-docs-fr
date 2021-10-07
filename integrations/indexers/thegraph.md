---
title: The Graph
description: Construire des API à l'aide du protocole d'indexation The Graph sur Moonbeam
---

# Utiliser The Graph sur Moonbeam

![The Graph on Moonbeam](/images/builders/integrations/indexers/the-graph/the-graph-banner.png)

## Introduction {: #introduction } 

Les protocoles d'indexation organisent les informations de manière à ce que les applications puissent y accéder plus efficacement. Par exemple, Google indexe l'intégralité d'Internet pour fournir des informations rapidement lorsque vous recherchez quelque chose.

The Graph est un protocole d'indexation décentralisé et open source pour interroger des réseaux comme Ethereum. En bref, il fournit un moyen de stocker efficacement les données émises par les événements des contrats intelligents afin que d'autres projets ou dApps puissent y accéder facilement.

De plus, les développeurs peuvent créer des API, appelées subgraphs. Les utilisateurs ou d'autres développeurs peuvent utiliser des subgraphs pour interroger des données spécifiques à un ensemble de contrats intelligents. Les données sont récupérées avec une API GraphQL standard. Vous pouvez visiter [leur documentation](https://thegraph.com/docs/introduction#what-the-graph-is) pour en savoir plus sur le protocole The Graph.

Avec l'introduction des modules de traçage Ethereum dans [Moonbase Alpha v7](https://github.com/PureStake/moonbeam/releases/tag/v0.7.0), The Graph est capable d'indexer les données de la blockchain dans Moonbeam.

Ce guide vous guide à travers la création d'un subgraph simple pour un contrat de loterie sur Moonbase Alpha.

## Vérification des prérequis {: #checking-prerequisites } 

Pour utiliser The Graph sur Moonbase Alpha, vous avez deux options:

 - Exécutez un nœud graph sur Moonbase Alpha et pointez votre sous-graphe dessus. Pour ce faire, vous pouvez suivre [ce tutoriel](/node-operators/indexers/thegraph-node/)
 - Pointez votre subgraph vers l'API Graph via le [site Graph Explorer ](https://thegraph.com/explorer/). Pour ce faire, vous devez créer un compte et disposer d'un jeton d'accès
 
## Le contrat de loterie {: #the-lottery-contract } 

Pour cet exemple, un simple contrat de loterie est utilisé. Vous pouvez trouver le fichier Solidity dans [ce lien](https://github.com/PureStake/moonlotto-subgraph/blob/main/contracts/MoonLotto.sol). 

TLe contrat organise une loterie où les joueurs peuvent acheter un billet pour eux-mêmes ou en offrir un à un autre utilisateur. Lorsque 1 heure s'est écoulée, s'il y a 10 participants, le prochain joueur qui se joint à la loterie exécutera une fonction qui choisit le gagnant. Tous les fonds stockés dans le contrat sont envoyés au gagnant, après quoi un nouveau tour commence.

Les principales fonctions du contrat sont les suivantes:

 - **joinLottery** — pas d'entrées. Fonction pour entrer dans le tour en cours de la loterie, la valeur (quantité de jetons) envoyée au contrat doit être égale au prix du billet
 - **giftTicket** —  une entrée : l'adresse du destinataire du ticket. Semblable à `joinLottery` mais le propriétaire du ticket peut être défini sur une adresse différente
 - **enterLottery** — une entrée : l'adresse du propriétaire du ticket. Une fonction interne qui gère la logique des billets de loterie. Si une heure s'est écoulée et qu'il y a au moins 10 participants, il appelle la fonction `pickWinner`
 - **pickWinner** — pas d'entrées. Une fonction interne qui sélectionne le gagnant de la loterie avec un générateur de nombres pseudo-aléatoires (pas sûr, uniquement à des fins de démonstration). Il gère la logique de transfert de fonds et de réinitialisation de la variable pour le prochain tour de loterie

### Événements du contrat de loterie {: #events-of-the-lottery-contract } 

The Graph utilise les événements émis par le contrat pour indexer les données. Le contrat de loterie n'émet que deux événements:

 - **PlayerJoined** — Dans la fonction `enterLottery` . Il fournit des informations relatives à la dernière entrée de loterie, telles que l'adresse du joueur, le tour de loterie en cours, si le billet a été offert et le montant du prix du tour en cours
 - **LotteryResult** — Dans la fonction `pickWinner`. Il fournit des informations sur le tirage d'un tour en cours, telles que l'adresse du gagnant, le tour de loterie en cours, si le billet gagnant était un cadeau, le montant du prix et l'horodatage du tirage.

## Créer un Subgraph {: #creating-a-subgraph } 

Cette section décrit le processus de création d'un subgraph. Pour le subgraph de loterie, un [repository GitHub](https://github.com/PureStake/moonlotto-subgraph) a été préparé avec tout ce dont vous avez besoin pour vous aider à démarrer. Le repo comprend également le contrat de loterie, ainsi qu'un fichier de configuration Hardhat et un script de déploiement. Si vous ne le connaissez pas, vous pouvez consulter notre [guide d'integration Hardhat ](/integrations/hardhat/). 

Pour commencer, clonez d'abord le repo et installez les dépendances:

```
git clone https://github.com/PureStake/moonlotto-subgraph \
&& cd moonlotto-subgraph && yarn
```

Maintenant, vous pouvez créer les types TypeScript pour The Graph en exécutant:

```
npx graph codegen --output-dir src/types/
```

!!! remarque
    La création des types nécessite que vous ayez les fichiers ABI spécifiés dans le fichier `subgraph.yaml` . Cet exemple de repo contient déjà le fichier, mais celui-ci est généralement obtenu après la compilation du contrat. Vérifier le [Moonlotto repo](https://github.com/PureStake/moonlotto-subgraph) pour plus d'informations.

La commande `codegen` peut également être exécuté en utilisant `yarn codegen`.

Pour cet exemple, le contrat a été déployé à `{{ networks.moonbase.thegraph.lotto_contract }}`. Vous pouvez trouver plus d'informations sur la façon de déployer un contrat avec Hardhat dans notre [tutoriel d'integrations](/integrations/hardhat/). De plus, le fichier "README" dans le [Moonloto repository](https://github.com/PureStake/moonlotto-subgraph) a les étapes nécessaires pour compiler et déployer le contrat si nécessaire.

### Structure de base des Subgraphs {: #subgraphs-core-structure } 

En termes généraux, les subgraphs définissent les données que The Graph indexera à partir de la blockchain et la façon dont elles sont stockées. Les subgraphs ont tendance à avoir certains des fichiers suivants:

 - **subgraph.yaml** — est un fichier YAML qui contient le [manifeste du Subgraph](https://thegraph.com/docs/define-a-subgraph#the-subgraph-manifest), c'est-à-dire les informations relatives aux contrats intelligents indexés par le subraph
 - **schema.graphql** — est un fichier [schema GraphQL](https://thegraph.com/docs/define-a-subgraph#the-graphql-schema) qui définit le magasin de données pour le sous-graphe en cours de création et sa structure. Il s'écrit en utilisant le [schema d'interface de définition GraphQL](https://graphql.org/learn/schema/#type-language)
 - **AssemblyScript mappings** — coder en TypeScript (puis compilé en [AssemblyScript](https://github.com/AssemblyScript/assemblyscript)) qui est utilisé pour traduire les données d'événement du contrat vers les entités définies dans le schéma

Il n'y a pas d'ordre particulier à suivre lors de la modification des fichiers pour créer un Subgraph.

### Schema.graphql {: #schemagraphql } 

Il est important de préciser quelles données doivent être extraites des événements du contrat avant de modifier les `schema.graphql`. Les schémas doivent être définis en tenant compte des exigences de la dApp elle-même. Pour cet exemple, bien qu'il n'y ait pas de dApp associée à la loterie, quatre entités sont définies:

 - **Round** — fait référence à un tour de loterie. Il stocke un index du tour, le prix attribué, l'horodatage du début du tour, l'horodatage du tirage au sort du gagnant et des informations concernant les billets participants, qui sont dérivées de l'entité `Ticket`
 - **Player** — désigne un joueur qui a participé à au moins un tour. Il stocke son adresse et les informations de tous ses billets participants, qui sont dérivées de l'entité `Ticket`
 - **Ticket** — fait référence à un ticket pour participer à un tour de loterie. Il mémorise si le billet a été offert, l'adresse du propriétaire, la manche à partir de laquelle le billet est valable, et s'il s'agissait d'un billet gagnant

En bref, `schema.graphql` devrait ressembler à l'extrait suivant:

```
type Round @entity {
  id: ID!
  index: BigInt!
  prize: BigInt! 
  timestampInit: BigInt!
  timestampEnd: BigInt
  tickets: [Ticket!] @derivedFrom(field: "round")
}

type Player @entity {
  id: ID!
  address: Bytes!
  tickets: [Ticket!] @derivedFrom(field: "player")
}

type Ticket @entity {
  id: ID!
  isGifted: Boolean!
  player: Player!
  round: Round!
  isWinner: Boolean!
}
```

### Manifeste Subgraph {: #subgraph-manifest }

Le fichier `subgraph.yaml`, ou manifeste du subgraph, contient les informations relatives au contrat intelligent en cours d'indexation, y compris les événements pour lesquels les données doivent être mappées. Ces données sont ensuite stockées par les nœuds Graph, permettant aux applications de les interroger.

Certains des paramètres les plus importants dans le fichier `subgraph.yaml` sont:

 - **repository** — fait référence au repo Github du subgraph
 - **schema/file** — fait référence à l'emplacement du fichier `schema.graphql`
 - **dataSources/name** — fait référence au nom du Subgraph
 - **network** — fait référence au nom du réseau. Cette valeur **doit** être définie sur `mbase` pour tout subgraph déployé sur Moonbase Alpha
 - **dataSources/source/address** — fait référence à l'adresse du contrat d'intérêt
 - **dataSources/source/abi** — fait référence à l'endroit où l'interface du contrat est stockée dans le dossier `types` créé avec la commande `codegen`
 - **dataSources/source/startBlock** — fait référence au bloc de départ à partir duquel l'indexation commencera. Idéalement, cette valeur devrait être proche du bloc où le contrat a été créé. Vous puvez utiliser [Blockscout](https://moonbase-blockscout.testnet.moonbeam.network/) pour obtenir ces informations en fournissant l'adresse du contrat. Pour cet exemple, le contrat a été créé au bloc `{{ networks.moonbase.thegraph.block_number }}`
 - **dataSources/mapping/file** — fait référence à l'emplacement du fichier de mappage
 - **dataSources/mapping/entities** — renvoie aux définitions des entités dans le fichier `schema.graphql`
 - **dataSources/abis/name** — fait référence à l'endroit où l'interface du contrat est stockée à l'intérieur de `types/dataSources/name`
 - **dataSources/abis/file** — fait référence à l'emplacement où le fichier `.json` avec l'ABI du contrat est stocké
 - **dataSources/eventHandlers** — aucune valeur n'a besoin d'être définie ici, mais cette section fait référence à tous les événements que The Graph indexera
 - **dataSources/eventHandlers/event** — fait référence à la structure d'un événement à suivre à l'intérieur du contrat. Vous devez fournir le nom de l'événement et son type de variables
 - **dataSources/eventHandlers/handler** — fait référence au nom de la fonction à l'intérieur du fichier `mapping.ts` qui gère les données d'événement
 
En bref, le `subgraph.yaml` devrait ressembler à l'extrait suivant:

```
specVersion: 0.0.2
description: Moonbeam lottery subgraph tutorial
repository: https://github.com/PureStake/moonlotto-subgraph
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: MoonLotto
    network: mbase
    source:
      address: '{{ networks.moonbase.thegraph.lotto_contract }}'
      abi: MoonLotto
      startBlock: {{ networks.moonbase.thegraph.block_number }}
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.4
      language: wasm/assemblyscript
      file: ./src/mapping.ts
      entities:
        - Player
        - Round
        - Ticket
        - Winner
      abis:
        - name: MoonLotto
          file: ./artifacts/contracts/MoonLotto.sol/MoonLotto.json
      eventHandlers:
        - event: PlayerJoined(uint256,address,uint256,bool,uint256)
          handler: handlePlayerJoined
        - event: LotteryResult(uint256,address,uint256,bool,uint256,uint256)
          handler: handleLotteryResult
```

### Mappages {: #mappings } 

Les fichiers de mappages sont ce qui transforme les données de la blockchain en entités définies dans le fichier de schéma. Chaque gestionnaire d'événement à l'intérieur du fichier `subgraph.yaml` doit avoir une fonction ultérieure dans le mappage.

Le fichier de mappage utilisé pour l'exemple de loterie se trouve dans [ce lien](https://github.com/PureStake/moonlotto-subgraph/blob/main/src/mapping.ts).

En général, la stratégie de chaque fonction de gestionnaire consiste à charger les données d'événement, à vérifier si une entrée existe déjà, à organiser les données comme vous le souhaitez et à enregistrer l'entrée. Par exemple, la fonction de gestion de l'événement `PlayerJoined` est la suivante:

```
export function handlePlayerJoined(event: PlayerJoined): void {
  // ID for the round:
  // round number
  let roundId = event.params.round.toString();
  // try to load Round from a previous player
  let round = Round.load(roundId);
  // if round doesn't exists, it's the first player in the round -> create round
  if (round == null) {
    round = new Round(roundId);
    round.timestampInit = event.block.timestamp;
  }
  round.index = event.params.round;
  round.prize = event.params.prizeAmount;

  round.save();

  // ID for the player:
  // issuer address
  let playerId = event.params.player.toHex();
  // try to load Player from previous rounds
  let player = Player.load(playerId);
  // if player doesn't exists, create it
  if (player == null) {
    player = new Player(playerId);
  }
  player.address = event.params.player;

  player.save();

  // ID for the ticket (round - player_address - ticket_index_round):
  // "round_number" + "-" + "player_address" + "-" + "ticket_index_per_round"
  let nextTicketIndex = event.params.ticketIndex.toString();
  let ticketId = roundId + "-" + playerId + "-" + nextTicketIndex;

  let ticket = new Ticket(ticketId);
  ticket.round = roundId;
  ticket.player = playerId;
  ticket.isGifted = event.params.isGifted;
  ticket.isWinner = false;

  ticket.save();  
}
```

## Déployer un Subgraph {: #deploying-a-subgraph } 

Si vous comptez utiliser The Graph API (service hébergé), vous devez:

 - Créer un compte [Graph Explorer](https://thegraph.com/explorer/) , vous aurez besoin d'un compte Github
 - Go to your dashboard and write down the access token
 - Créez votre subgraph via le bouton "Add Subgraph" dans le site Graph Explorer. Notez le nom du Subgraph

!!! remarque
    Toutes les étapes se trouvent dans [ce lien](https://thegraph.com/docs/deploy-a-subgraph).
 
Si vous utilisez un nœud graph local, vous pouvez créer votre subgraph en exécutant le code suivant:

```
npx graph create <username>/<subgraphName> --node <graph-node>
```

ou:

 - **username** — fait référence au nom d'utilisateur lié au subgraph en cours de création
 - **subgraphName** — fait référence au nom du subgraph
 - **graph-node** — fait référence à l'URL du service hébergé à utiliser. Qui Typiquement, pour un nœud graph local est `http://127.0.0.1:8020`

Une fois créé, vous pouvez déployer votre Subgraph en exécutant la commande suivante avec les mêmes paramètres qu'avant:

```
npx graph deploy <username>/<subgraphName> \
--ipfs <ipfs-url> \
--node <graph-node> \
--access-token <access-token>
```

Ou:

 - **username** — fait référence au nom d'utilisateur utilisé lors de la création du subgraph
 - **subraphName** — fait référence au nom du subgraph défini lors de la création du subgraph
 - **ifps-url**  — fait référence à l'URL de l'IFPS. Si vous utilisez l'API Graph, vous pouvez utiliser `https://api.thegraph.com/ipfs/`. Pour votre nœud graph local, la valeur par défaut est `http://localhost:5001`
 - **graph-node** — fait référence à l'URL du service hébergé à utiliser. Si vous utilisez l'API Graph, vous pouvez utiliser `https://api.thegraph.com/deploy/`. Pour votre nœud graph local, la valeur par défaut est `http://localhost:8020`
 - **access-token** — fait référence au jeton d'accès pour utiliser l'API Graph. Si vous utilisez un nœud graph local, ce paramètre n'est pas nécessaire

Les journaux de la commande précédente devraient ressembler à:

![The Graph deployed](/images/builders/integrations/indexers/the-graph/the-graph-1.png)

Les DApps peuvent désormais utiliser les points de terminaison Subgraph pour récupérer les données indexées par le protocole The Graph.
