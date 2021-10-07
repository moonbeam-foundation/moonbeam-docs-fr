---
title: Déboguer et tracer
description:  Apprenez à tirer parti de l'API de débogage Geth et du module de tracage OpenEthereum sur Moonbeam
---

# API de débogage et module de tracage

![Full Node Moonbeam Banner](/images/builders/tools/debug-trace/debug-trace-banner.png)

## Introduction {: #introduction } 

L'API de débogage de Geth et le module de tracage d'OpenEthereum fournissent des méthodes RPC non standard pour obtenir un aperçu plus approfondi du traitement des transactions.

Avec la sortie de Moonbase Alpha v7, dans le cadre de l'objectif de Moonbeam de fournir une expérience Ethereum transparente aux développeurs, les deux méthodes RPC `debug_traceTransaction` et `trace_filter` sont maintenant disponibles.

La prise en charge des deux méthodes RPC est une étape importante car de nombreux projets, tels que [The Graph](https://thegraph.com/) ou [Blockscout](https://docs.blockscout.com/), compte sur eux pour indexer les données de la blockchain.

Les deux appels sont assez lourds du côté du nœud. Par conséquent, il est nécessaire d'effectuer ce RPC sur un nœud exécuté localement avec le flag `--ethapi=debug` pour `debug_traceTransaction`, et/ou le flag `--ethapi=trace` pour `trace_filter`. Actuellement, vous pouvez créer deux types de nœuds différents:

 - **Noeud de développement Moonbeam** — exécutez votre propre instance Moonbeam dans votre environnement privé. Pour ce faire, vous pouvez suivre [ce guide](/getting-started/local-node/setting-up-a-node/). Assurez-vous de consulter la [section des flags avancés](/getting-started/local-node/setting-up-a-node/#advanced-flags-and-options)
 - **Nœud Moonbase Alpha** — exécutez un nœud complet du TestNet et accédez à vos propres points de terminaison privés. Pour ce faire, vous pouvez suivre [ce guide](/node-operators/networks/full-node/). Assurez-vous de consulter la [section des flags avancés](/node-operators/networks/full-node/#advanced-flags-and-options)

## API de débogage Geth {: #geth-debug-api } 

L'implémentation RPC `debug_traceTransaction` suit les [Directives de l'API de débogage de Geth](https://geth.ethereum.org/docs/rpc/ns-debug#debug_tracetransaction).

La méthode RPC nécessite l'exécution du hachage de la transaction. En tant que paramètres facultatifs, vous pouvez fournir les éléments suivants:

 - **disableStorage** — une entrée : booléen (par défaut: _false_). Définir cette valeur sur true désactive la capture de stockage
 - **disableMemory** — une entrée : booléen (par défaut: _false_). Définir ceci sur true désactive la capture de mémoire
 - **disableStack** — une entrée : booléen (default: _false_). Définir ce paramètre sur true désactive la capture de la pile

Le traçage des transactions basé sur JavaScript n'est pas pris en charge pour le moment.

## Module de Tracage {: #trace-module } 

L' implémentations RPC `trace_filter`suit les [Directives du module de tracage d'OpenEthereum](https://openethereum.github.io/JSONRPC-trace-module#trace_filter).

La méthode RPC requiert l'un des paramètres facultatifs suivants:

 - **fromBlock** — une entrée : soit le numéro de bloc (`hex`), `earliest`qui est le bloc de genèse ou `latest` (par défaut) le meilleur bloc disponible. Tracer le bloc de départ
 - **toBlock** — une entrée : soit le numéro de bloc (`hex`), `earliest` qui est le bloc de genèse ou `latest` meilleur bloc disponible. Tracer le bloc de fin
 - **fromAddress** — une entrée : tableau d'adresses. Filtrez les transactions effectuées à partir de ces adresses uniquement. Si un tableau vide est fourni, aucun filtrage n'est effectué avec ce champ
 - **toAddress** — une entrée : tableau d'adresses. Filtrez les transactions effectuées à partir de ces adresses uniquement. Si un tableau vide est fourni, aucun filtrage n'est effectué avec ce champ
 - **after** — une entrée : offset (`uint`), la valeur par défaut est `0`. Numéro de décalage (ou de début) de traçage
 - **count** — une entrée : nombre de tracages (`uint`). nombre de tracages à afficher dans un lot

## Essayez-le sur Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Comme mentionné précédemment, pour utiliser les deux fonctionnalités, vous devez disposer d'un nœud exécuté avec les flags `debug` et `trace` . Pour cet exemple, un nœud complet Moonbase Alpha local est utilisé, avec le point de terminaison HTTP RPC à `http://127.0.0.1:9933`. Si vous avez un nœud en cours d'exécution, vous devriez voir un journal de terminal similaire:

![Debug API](/images/builders/tools/debug-trace/debug-trace-1.png)

Par exemple, pour l'appel `debug_traceTransaction` vous pouvez effectuer la requête JSON RPC suivante dans votre terminal (dans ce cas, pour le hachage de transaction `0x04978f83e778d715eb074352091b2159c0689b5ae2da2554e8fe8e609ab463bf`):

```
curl http://localhost:9933 -H "Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"debug_traceTransaction",
    "params": ["0x04978f83e778d715eb074352091b2159c0689b5ae2da2554e8fe8e609ab463bf"]
  }'
```

Le nœud répond avec les informations de transaction répétées étape par étape (la réponse a été recadrée car elle est assez longue):

![Trace Debug Node Running](/images/builders/tools/debug-trace/debug-trace-2.png)

Pour l'appel `trace_filter` vous pouvez effectuer la requête JSON RPC suivante dans votre terminal (dans ce cas, le filtre est du bloc 20000 à 25000, uniquement pour les transactions où se trouve le destinataire  `0x4E0078423a39EfBC1F8B5104540aC2650a756577`, il commencera par un offset zéro et fournira les 20 premières tracages):

```
curl http://localhost:9933 -H "Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"trace_filter", "params":[{"fromBlock":"0x4E20","toBlock":"0x5014","toAddress":["0x4E0078423a39EfBC1F8B5104540aC2650a756577"],"after":0,"count":20}]
  }'
```

Le nœud répond avec les informations de tracage correspondant au filtre (la réponse a été rognée car elle est assez longue).

![Trace Filter Node Running](/images/builders/tools/debug-trace/debug-trace-3.png)

