---
title: Nœud Graph
description: Construire des API à l'aide du protocole d'indexation The Graph sur Moonbeam
---

# Exécuter un nœud Graph sur Moonbeam

![The Graph Node on Moonbeam](/images/node-operators/indexer-nodes/the-graph/the-graph-node-banner.png)

## Introduction {: #introduction } 

Un nœud Graph source des événements à partir d'une blockchain pour mettre à jour de manière déterministe un magasin de données qui peut ensuite être interrogé via un point de terminaison GraphQL.

Vous pouvez configurer un nœud Graph de deux manières : vous pouvez utiliser Docker pour exécuter une image tout-en-un, ou vous pouvez exécuter leur [implementation Rust](https://github.com/graphprotocol/graph-node). Les étapes décrites dans ce guide ne couvriront que l'alternative Docker, car elle est plus pratique et vous pourrez configurer un nœud de graph très rapidement.

!!! remarque
    Les étapes décrites dans ce guide ont été testées dans les environnements Ubuntu 18.04 et MacOs, et elles devront être adaptées en conséquence pour d'autres systèmes.

## Vérification des prérequis {: #checking-prerequisites } 

Avant de plonger dans la configuration d'un nœud graph, vous devez avoir installé les éléments suivants sur votre système :

 - [Docker](https://docs.docker.com/get-docker/)
 - [Docker Compose](https://docs.docker.com/compose/install/)
 - [JQ](https://stedolan.github.io/jq/download/)

De plus, vous devez avoir un nœud fonctionnant avec l'option `--ethapi=trace` activée. Actuellement, vous pouvez créer deux types de nœuds différents :

 - **Noeud de développement Moonbeam** — exécutez votre propre instance Moonbeam dans votre environnement privé. Pour ce faire, vous pouvez suivre [ce guide](/getting-started/local-node/setting-up-a-node/). Assurez vous de consulter la [section des flags avancés](/getting-started/local-node/setting-up-a-node/#advanced-flags-and-options)
 - **Nœud Moonbase Alpha** — exécutez un nœud complet du TestNet et accédez à vos propres points de terminaison privés. Pour ce faire, vous pouvez suivre [ce guide](/node-operators/networks/full-node/). Assurez vous de consulter la [section des flags avancés](/node-operators/networks/full-node/#advanced-flags-and-options)

Dans ce guide, un nœud graph s'exécute sur un nœud complet Moonbase Alpha avec le flag `--ethapi=trace` .

## Exécuter un nœud graph {: #running-a-graph-node } 

La première étape consiste à cloner le [repository du noeud Graph](https://github.com/graphprotocol/graph-node/):

```
git clone https://github.com/graphprotocol/graph-node/ \
&& cd graph-node/docker
```

Ensuite, exécutez le fichier `setup.sh`. Cela extraira toutes les images Docker nécessaires et écrira les informations nécessaires dans le fichier `docker-compose.yml`.

```
./setup.sh
```

La fin des journaux de la commande précédente devrait ressembler à :

![Graph Node setup](/images/node-operators/indexer-nodes/the-graph/the-graph-node-1.png)

Une fois que tout est configuré, vous devez modifier l "environnement Ethereum" à l'interieur du fichier `docker-compose.yml`, afin qu'il pointe vers le point de terminaison du nœud sur lequel vous exécutez ce nœud graph. Notez que le fichier `setup.sh` détectel' `Host IP` et écrit sa valeur, vous devrez donc la modifier en conséquence.

```
ethereum: 'mbase:http://localhost:9933'
```

L'ensemble du fichier `docker-compose.yml` devrait ressembler à quelque chose comme:

```
version: '3'
services:
  graph-node:
    image: graphprotocol/graph-node
    ports:
      - '8000:8000'
      - '8001:8001'
      - '8020:8020'
      - '8030:8030'
      - '8040:8040'
    depends_on:
      - ipfs
      - postgres
    environment:
      postgres_host: postgres
      postgres_user: graph-node
      postgres_pass: let-me-in
      postgres_db: graph-node
      ipfs: 'ipfs:5001'
      ethereum: 'mbase:http://localhost:9933'
      RUST_LOG: info
  ipfs:
    image: ipfs/go-ipfs:v0.4.23
    ports:
      - '5001:5001'
    volumes:
      - ./data/ipfs:/data/ipfs
  postgres:
    image: postgres
    ports:
      - '5432:5432'
    command: ["postgres", "-cshared_preload_libraries=pg_stat_statements"]
    environment:
      POSTGRES_USER: graph-node
      POSTGRES_PASSWORD: let-me-in
      POSTGRES_DB: graph-node
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
```

Enfin, pour exécuter le nœud graph, exécutez simplement la commande suivante:

```
docker-compose up
```

![Graph Node compose up](/images/node-operators/indexer-nodes/the-graph/the-graph-node-2.png)

Après un certain temps, vous devriez voir les journaux liés à la synchronisation du nœud graph avec le dernier bloc disponible sur le réseau :

![Graph Node logs](/images/node-operators/indexer-nodes/the-graph/the-graph-node-3.png)

Et c'est tout ! Vous avez un nœud graph exécuté sur le Moonbase Alpha TestNet. N'hésitez pas à adapter également cet exemple à un nœud de développement Moonbeam.
