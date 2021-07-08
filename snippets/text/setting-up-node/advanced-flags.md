Avec la sortie de Moonbase Alpha v7, les nœuds donnent également accès à certaines méthodes RPC non standard, qui permettent aux développeurs d'inspecter et de déboguer les transactions pendant l'exécution. Actuellement, deux fonctionnalités sont disponibles :

 - API de débogage Geth : plus précisément, la méthode `debug_traceTransaction`. Cela tentera d'exécuter la transaction de la même manière qu'elle a été exécutée. Vous pouvez en savoir plus sur cette méthode RPC dans [ce lien](https://geth.ethereum.org/docs/rpc/ns-debug#debug_tracetransaction)
 - Module de tracage OpenEthereum : plus précisément, la méthode `trace_filter`. Cela renvoie la trace correspondant à un filtre spécifique fourni en entrée de l'appel RPC. Vous pouvez en savoir plus sur cette méthode RPC dans [ce lien](https://openethereum.github.io/JSONRPC-trace-module#trace_filter)

Les fonctionnalités mentionnées ci-dessus peuvent être activées à l'aide des flags suivants:

 - `--ethapi=debug`: active l'API de débogage Geth pour l'appel RPC `debug_traceTransaction` 
 - `--ethapi=trace`: active le module de tracage OpenEthereum pour l'appel RPC `trace_filter`

!!! remarque
    Les fonctionnalités de débogage/trace sont toujours activement développées. Comme ces requêtes sont très gourmandes en CPU, il est recommandé d'exécuter le nœud avec le flag `--execution=Native` . Cela utilisera le runtime natif inclus dans l'exécutable du nœud au lieu du binaire Wasm stocké en chaîne.

Vous pouvez combiner les deux flags lors de l'exécution d'un nœud. 

Par défaut, le nombre maximum d'entrées de tracage qu'une seule requête de `trace_filter` est autorisé à retourné est `500`. Une requête dépassant cette limite renverra une erreur. Vous pouvez définir une limite maximale différente avec le flag suivant:

 - `--ethapi-trace-max-count <uint>`: définit le nombre maximum d'entrées de tracage à renvoyer par le nœud

Les blocs traités par les requêtes sont temporairement stockés dans le cache pendant un certain temps (la valeur par défaut est `300` secondes), après quoi ils sont supprimés. Vous pouvez définir une heure différente pour la suppression avec le flag suivant:

 - `-ethapi-trace-cache-duration <uint>`: définit la durée (en secondes) après laquelle le cache de `trace_filter,` pour un bloc donné, est supprimé
