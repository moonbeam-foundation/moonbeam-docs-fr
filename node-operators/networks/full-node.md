---
title: Exécuter un nœud sur Moonbeam
description: Comment exécuter un nœud Parachain complet pour le réseau Moonbeam afin d'avoir votre point de terminaison RPC ou de produire des blocs.
---

# Exécuter un nœud sur Moonbeam

![Full Node Moonbeam Banner](/images/fullnode/fullnode-banner.png)

## Introduction

Avec la sortie de Moonbase Alpha v6, vous pouvez activer un nœud qui se connecte au Moonbase Alpha TestNet, se synchronise avec un nœud de démarrage, fournit un accès local à vos points de terminaison RPC et même crée des blocs sur la parachaine.

Dans notre TestNet, la chaîne de relais est hébergée et gérée par PureStake. Mais à mesure que le développement progresse, il y aura également des déploiements à Kusama puis à Polkadot. Voici comment ces environnements à venir seront nommés et le nom de leurs [fichiers de spécification de chaîne](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec) correspondants:


|    Réseau      |     |  Hébergé par  |     |   Nom de la chaîne    |
| :------------: | :-: | :-----------: | :-: | :-------------------: |
| Moonbase Alpha |     |   PureStake   |     |       alphanet        |
|   Moonriver    |     |    Kusama     |     |    _not available_    |
|    Moonbeam    |     |   Polkadot    |     |    _not available_    |

Ce guide est destiné aux personnes ayant l'expérience de l'exécution de chaînes basées sur des [Substrate](https://substrate.dev/) . L'exécution d'une parachain est similaire à l'exécution d'un nœud de substrat avec quelques différences. Un nœud de parachain de substrat exécutera deux processus: un pour synchroniser la chaîne de relais et un pour synchroniser la parachain. En tant que tel, de nombreux éléments sont doublés, par exemple le répertoire de la base de données, les ports utilisés, les lignes de journal, etc.


!!! note
    Moonbase Alpha est toujours considéré comme un Alphanet et, en tant que tel, n'aura pas 100% de disponibilité. La parachaine sera purgée de temps en temps. Lors du développement de votre application, assurez-vous de mettre en œuvre une méthode pour redéployer rapidement vos contrats et comptes vers une nouvelle parachain. Les purges de chaînes seront annoncées via notre [ canal discord](https://discord.gg/PfpUATX) au moins 24 heures à l'avance.


## Requirements

Les spécifications minimales recommandées pour exécuter un nœud sont indiquées dans le tableau suivant. Pour nos déploiements MainNet Kusama et Polkadot, les exigences en matière de disque seront plus élevées à mesure que le réseau se développera.


|  Composant   |     | Exigence                                                                                                                               |
| :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------------------- |
|   **CPU**    |     | 8 Coeurs (Phase de développement précose - pas encore optimisé)                                                                        |
|   **RAM**    |     | 16 GB (Phase de développement précose - pas encore optimisé)                                                                           |
|   **SSD**    |     | 50 GB (Pour démarrer dans notre testnet)                                                                                               |
| **Pare-feu** |     | Le port P2P doit être ouvert au trafic entrant:<br>&nbsp; &nbsp; - Source: Quelconque<br>&nbsp; &nbsp; - Destination: 30333, 30334 TCP |

!!! note
    Si vous ne voyez pas de message `Imported` (sans la balise `[Relaychain]`) lors de l'exécution d'un nœud, vous devrez peut-être revérifier la configuration de votre port.
## Ports en Cours d'Exécution

Comme indiqué précédemment, les nœuds de relais / parachain écouteront sur plusieurs ports. Les ports de substrat par défaut sont utilisés dans la parachain, tandis que la chaîne de relais écoutera sur le port supérieur suivant.

Les seuls ports qui doivent être ouverts pour le trafic entrant sont ceux désignés pour le P2P.


### Ports par Défaut pour un Nœud Complet Parachain

|  Description   |     |                Port                 |
| :------------: | :-: | :---------------------------------: |
|    **P2P**     |     | {{ networks.parachain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.parachain.rpc }}     |
|     **WS**     |     |     {{ networks.parachain.ws }}     |
| **Prometheus** |     | {{ networks.parachain.prometheus }} |

### Ports par Défaut de la Chaîne de Relais Intégrée

|  Description   |     |                 Port                  |
| :------------: | :-: | :-----------------------------------: |
|    **P2P**     |     | {{ networks.relay_chain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.relay_chain.rpc }}     |
|     **WS**     |     |     {{ networks.relay_chain.ws }}     |
| **Prometheus** |     | {{ networks.relay_chain.prometheus }} |

## Instructions d'Installation - Docker

Un nœud Moonbase Alpha peut être tourné rapidement à l'aide de Docker. Pour plus d'informations sur l'installation de Docker, veuillez visiter [cette page](https://docs.docker.com/get-docker/) . Au moment de la rédaction de cet article, la version de Docker utilisée était le 19.03.6.

Tout d'abord, créez un répertoire local pour stocker les données de la chaîne:

```
mkdir {{ networks.moonbase.node_directory }}
```

Ensuite, définissez les autorisations nécessaires pour un utilisateur spécifique ou actuel (remplacez-les `DOCKER_USER` par l'utilisateur réel qui exécutera la commande `docker`):
```
# chown to a specific user
chown DOCKER_USER {{ networks.moonbase.node_directory }}

# chown to current user
sudo chown -R $(id -u):$(id -g) {{ networks.moonbase.node_directory }}
```

!!! note
    Assurez-vous de définir la propriété et les autorisations en conséquence pour le répertoire local qui stocke les données de la chaîne.

Maintenant, exécutez la commande docker run. Notez que vous devez: 
 - Remplacez `YOUR-NODE-NAME` à deux endroits différents.
 - Pour les assembleurs, remplacez `PUBLIC_KEY` par l'adresse publique qui sera associée aux activités de collation.

!!! note
    Si vous configurez un nœud d'assemblage, assurez-vous de suivre les extraits de code pour «Collator».

### Node Complet

=== "Ubuntu"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "MacOS"
    ```
    docker run -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

### Collator

=== "Ubuntu"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --validator \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "MacOS"
    ```
    docker run -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --validator \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

Une fois que Docker extrait les images nécessaires, votre nœud complet Moonbase Alpha démarre, affichant de nombreuses informations, telles que la spécification de la chaîne, le nom du nœud, le rôle, l'état de genèse, etc.

![Full Node Starting](/images/fullnode/fullnode-docker1.png)

!!! note
    Si vous rencontrez des problèmes avec la télémétrie par défaut, vous pouvez ajouter l'indicateur `--no-telemetry` pour exécuter le nœud complet sans la télémétrie activée.

!!! note
    Vous pouvez spécifier un port Prometheus personnalisé avec l'indicateur  `--prometheus-port XXXX` (en le remplaçant `XXXX` par le numéro de port réel). Ceci est possible à la fois pour la parachaine et la chaîne de relais intégrée.

La commande ci-dessus activera tous les ports exposés, y compris les ports P2P, RPC et Prometheus (télémétrie). Cette commande est compatible avec la télémétrie Gantree Node Watchdog. Si vous souhaitez exposer des ports spécifiques, activez ceux de la ligne de commande d'exécution Docker comme indiqué ci-dessous. Cependant, cela empêchera le conteneur Gantree Node Watchdog (télémétrie) d'accéder au conteneur Moonbeam, alors ne le faites pas lorsque vous exécutez un assembleur à moins que vous ne compreniez la [mise en réseau du docker](https://docs.docker.com/network/).


```
docker run -p {{ networks.relay_chain.p2p }}:{{ networks.relay_chain.p2p }} -p {{ networks.parachain.p2p }}:{{ networks.parachain.p2p }} -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} #rest of code goes here
```

Pendant le processus de synchronisation, vous verrez les messages de la chaîne de relais intégrée et de la parachain (sans étiquette). Ces messages affichent un bloc cible (TestNet) et un meilleur bloc (état synchronisé du nœud local).


![Full Node Starting](/images/fullnode/fullnode-docker2.png)

Une fois synchronisé, vous avez un nœud du Moonbase Alpha TestNet fonctionnant localement!

## Instructions d'Installation - Binaire

Cette section décrit le processus de compilation du binaire et d'exécution d'un nœud complet Moonbeam en tant que service systemd. Les étapes suivantes ont été testées sur une installation Ubuntu 18.04. Moonbase Alpha peut fonctionner avec d'autres versions de Linux, mais Ubuntu est actuellement la seule version testée.


### Compiler le binaire

Les commandes suivantes créeront la dernière version de la parachaine Moonbeam.

Commençons par cloner le repo moonbeam.

```
git clone https://github.com/PureStake/moonbeam
cd moonbeam
```

Jetons un œil à la dernière version:

```
git checkout tags/$(git tag | tail -1)
```

Ensuite, installez Substrate et tous ses prérequis, y compris Rust, en exécutant:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Enfin, construisez le binaire parachain:

```
cargo build --release
```

![Compiling Binary](/images/fullnode/fullnode-binary1.png)

Si une _erreur de cargaison non trouvée_ apparaît dans le terminal, ajoutez manuellement Rust à votre chemin système ou redémarrez votre système:

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

### Exécution du Dervice Systemd

Les commandes suivantes configureront tout ce qui concerne l'exécution du service.

Commençons par créer un compte de service pour exécuter le service:


```
adduser moonbase_service --system --no-create-home
```

Ensuite, créez un répertoire pour stocker le binaire et les données et définissez les autorisations nécessaires:

```
mkdir {{ networks.moonbase.node_directory }}
chown moonbase_service {{ networks.moonbase.node_directory }}
```

!!! note
    Assurez-vous de définir la propriété et les autorisations en conséquence pour le répertoire local qui stocke les données de la chaîne.


Maintenant, copiez le binaire construit dans la dernière section dans le dossier créé:

```
cp ./target/release/{{ networks.moonbase.binary_name }} {{ networks.moonbase.node_directory }}
```

L'étape suivante consiste à créer le fichier de configuration systemd. Notez que vous devez:

 - Remplacer `YOUR-NODE-NAME` à deux endroits différents
 - Vérifiez que le binaire est dans le bon chemin comme décrit ci-dessous ( _ExecStart_ )
 - Vérifiez le chemin de base si vous avez utilisé un répertoire différent
 - Pour les assembleurs, remplacez `PUBLIC-KEY` par la clé publique de votre adresse H160 Ethereum créée ci-dessus
 - Nommez le fichier `/etc/systemd/system/moonbeam.service`


!!! note
    Si vous configurez un nœud d'assemblage, assurez-vous de suivre les extraits de code pour «Collator».

=== "Noeud Complet"
    ```
    [Unit]
    Description="Moonbase Alpha systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonbase_service
    SyslogIdentifier=moonbase
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonbase.node_directory }}/{{ networks.moonbase.binary_name }} \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain alphanet \
         --name "YOUR-NODE-NAME" \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

=== "Collator"
    ```
    [Unit]
    Description="Moonbase Alpha systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonbase_service
    SyslogIdentifier=moonbase
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonbase.node_directory }}/{{ networks.moonbase.binary_name }} \
         --validator \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain alphanet \
         --name "YOUR-NODE-NAME" \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

!!! note
    Si vous rencontrez des problèmes avec la télémétrie par défaut, vous pouvez ajouter l'indicateur `--no-telemetry` pour exécuter le nœud complet sans la télémétrie activée.

!!! note
Vous pouvez spécifier un port Prometheus personnalisé avec l’indicateur `--prometheus-port XXXX` (en le remplaçant `XXXX` par le numéro de port réel). Ceci est possible à la fois pour la parachaine et la chaîne de relais intégrée.


Presque là! Inscrivez-vous et démarrez le service en exécutant:

```
systemctl enable moonbeam.service
systemctl start moonbeam.service
```
 
Et enfin, vérifiez que le service est en cours d'exécution:
```
systemctl status moonbeam.service
```

![Service Status](/images/fullnode/fullnode-binary2.png)

Vous pouvez également vérifier les journaux en exécutant:

```
journalctl -f -u moonbeam.service
```

![Service Logs](/images/fullnode/fullnode-binary3.png)

## Drapeaux et Options Avancés

Avec la sortie de Moonbase Alpha v7, les nœuds donnent également accès à certaines méthodes RPC non standard, qui permettent aux développeurs d'inspecter et de déboguer les transactions pendant l'exécution. Actuellement, deux fonctionnalités sont disponibles:

 - Geth debug API: plus précisément, la méthode `debug_traceTransaction`. Cela tentera d'exécuter la transaction de la même manière qu'elle a été exécutée. Vous pouvez en savoir plus sur cette méthode RPC dans [ce lien](https://geth.ethereum.org/docs/rpc/ns-debug#debug_tracetransaction).

 - Module de trace OpenEthereum: plus précisément, la méthode `trace_filter`. Cela renvoie la trace correspondant à un filtre spécifique fourni en entrée de l'appel RPC. Vous pouvez en savoir plus sur cette méthode RPC dans [ce lien](https://openethereum.github.io/JSONRPC-trace-module#trace_filter).

 Les fonctionnalités mentionnées ci-dessus peuvent être activées à l'aide des indicateurs suivants:
 - `--ethapi=debug`: active l'API de débogage Geth pour l'appel RPC ` debug_traceTransaction`
 - `--ethapi=trace`: active le module de trace OpenEthereum pour l'appel RPC `trace_filter`

!!! note
    Les fonctionnalités de débogage / traçage sont toujours activement développées. Étant donné que ces demandes sont très exigeantes en termes de processeur, il est recommandé d'exécuter le nœud avec l'indicateur `--execution=Native`. Cela utilisera le runtime natif inclus dans l'exécutable du nœud au lieu du binaire Wasm stocké sur la chaîne.

Vous pouvez combiner les deux indicateurs lors de l'exécution d'un nœud.
Par défaut, le nombre maximum d'entrées de trace qu'une seule requête `trace_filter` est autorisée à renvoyer est 500. Une demande dépassant cette limite renverra une erreur. Vous pouvez définir une limite maximale différente avec l'indicateur suivant:
 - `--ethapi-trace-max-count` <uint>: définit le nombre maximum d'entrées de trace à renvoyer par le nœud

 Les blocs traités par les demandes sont temporairement stockés dans le cache pendant un certain temps (la valeur par défaut est en secondes `300`), après quoi ils sont supprimés. Vous pouvez définir une heure de suppression différente avec l'indicateur suivant:
  - -ethapi-trace-cache-duration <uint>: définit la durée (en secondes) après laquelle le cache d'un bloc donné est supprimé `trace_filter`


--8<-- 'text/setting-up-node/advanced-flags.md'

## Mise à Jour du Client

Au fur et à mesure que le développement de Moonbeam se poursuit, il sera parfois nécessaire de mettre à niveau votre logiciel de nœud. Les opérateurs de nœuds seront informés sur notre [canal Discord](https://discord.gg/PfpUATX) lorsque des mises à niveau sont disponibles et si elles sont nécessaires (certaines mises à niveau des clients sont facultatives). Le processus de mise à niveau est simple et est le même pour un nœud complet ou un assembleur.

Tout d'abord, arrêtez le conteneur Docker ou le service systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Ensuite, installez la nouvelle version en répétant les étapes décrites précédemment, en vous assurant que vous utilisez la dernière balise disponible. Après la mise à jour, vous pouvez redémarrer le service.

### Purger la Chaîne

Parfois, Moonbase Alpha peut être purgé et réinitialisé lors de mises à niveau majeures. Comme toujours, les opérateurs de nœuds seront avertis à l'avance (via notre [canal Discord](https://discord.gg/PfpUATX)) si cette mise à jour est accompagnée d'une purge. Vous pouvez également purger votre nœud si votre répertoire de données individuel est corrompu.

Pour ce faire, arrêtez d'abord le conteneur Docker ou le service systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Ensuite, supprimez le contenu du dossier dans lequel les données de la chaîne sont stockées (à la fois pour la parachain et la chaîne de relais):

```
sudo rm -rf {{ networks.moonbase.node_directory }}/*
```

Enfin, installez la dernière version en répétant les étapes décrites précédemment, en vous assurant que vous utilisez la dernière balise disponible. Si tel est le cas, vous pouvez démarrer un nouveau nœud avec un nouveau répertoire de données.

## Télémétrie

Pour activer le serveur de télémétrie de votre nœud Moonbase Alpha, vous pouvez suivre [ce didacticiel](/node-operators/networks/telemetry/).

L'exécution de la télémétrie sur un nœud complet n'est pas nécessaire. Cependant, il est obligatoire de le faire pour les assembleurs.

En outre, vous pouvez voir les informations de télémétrie actuelles de Moonbase Alpha en visitant [ce lien](https://telemetry.polkadot.io/#list/Moonbase%20Alpha).


## Journaux et Dépannage

Vous verrez les journaux de la chaîne de relais ainsi que de la parachaine. La chaîne de relais sera préfixée par `[Relaychain]`, tandis que la parachaine n'a pas de préfixe.

### Ports P2P Non Ouverts

Si vous ne voyez pas de message `Imported` (sans l'étiquette `[Relaychain]`), vous devez vérifier la configuration du port P2P. Le port P2P doit être ouvert au trafic entrant.

### Synchronisé

Les deux chaînes doivent être synchronisées à tout moment, et vous devriez voir l'un ou l' autre des messages Imported, Idle et avoir des pairs connectés.

### Inadéquation de la Genèse

Le Moonbase Alpha TestNet est souvent mis à jour. Par conséquent, vous pouvez voir le message suivant:

```
DATE [Relaychain] Bootnode with peer id `ID` is on a different
chain (our genesis: GENESIS_ID theirs: OTHER_GENESIS_ID)
```

Cela signifie généralement que vous utilisez une version plus ancienne et que vous devrez effectuer une mise à niveau.

Nous annonçons les mises à niveau (et la purge de la chaîne correspondante) via notre [canal Discord](https://discord.gg/PfpUATX) au moins 24 heures à l'avance.

## Nous voulons de vos nouvelles

Si vous avez des commentaires concernant l'exécution d'un nœud complet ou tout autre sujet lié à Moonbeam, n'hésitez pas à contacter notre serveur de développement officiel [Discord](https://discord.gg/PfpUATX) .