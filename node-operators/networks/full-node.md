---
title: Executer un Noeud
description: Comment exécuter un nœud Parachain complet pour que le réseau Moonbeam ait votre point de terminaison RPC ou produise des blocs
---

# Exécuter un nœud sur Moonbeam

![Full Node Moonbeam Banner](/images/fullnode/fullnode-banner.png)

## Introduction {: #introduction } 

L'exécution d'un nœud complet sur un réseau basé sur Moonbeam vous permet de vous connecter au réseau, de vous synchroniser avec un nœud de démarrage, d'obtenir un accès local aux points de terminaison RPC, de créer des blocs sur la parachain, etc.

Il existe plusieurs déploiements de Moonbeam, y compris Moonbase Alpha TestNet, Moonriver sur Kusama, et éventuellement il y aura Moonbeam sur Polkadot. Voici comment ces environnements sont nommés et leurs noms correspondants [fichier de spécification de chaîne](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec) :

|    Réseau     |     | Hébergé par |     |   Nom de la chaine    |
| :------------: | :-: | :-------: | :-: | :-------------: |
| Moonbase Alpha |     | PureStake |     |    {{ networks.moonbase.chain_spec }}     |
|   Moonriver    |     |  Kusama   |     |    {{ networks.moonriver.chain_spec }}    |
|    Moonbeam    |     | Polkadot  |     | _not available_ |

Ce guide est destiné aux personnes ayant de l'expérience dans la compilation de nœuds blockchain basés sur [Substrate](https://substrate.dev/). Un nœud parachain est similaire à un nœud typique de substrate, mais il existe quelques différences. Un nœud parachain de substrat aura un plus grand build car il contient du code pour exécuter la parachain elle-même, ainsi que du code pour synchroniser la chaîne de relais et faciliter la communication entre les deux. En tant que tel, cette version est assez volumineuse et peut prendre plus de 30 minutes et nécessiter 32 Go de mémoire.

!!! remarque
    Moonbase Alpha est toujours considéré comme un Alphanet, et en tant que tel _n'aura pas_ une disponibilité de 100 %. La parachain sera purgée de temps en temps. Lors du développement de votre application, veillez à mettre en place une méthode pour redéployer rapidement vos contrats et vos comptes vers une nouvelle parachaine. Les purges en chaîne seront annoncées via notre [canal Discord](https://discord.gg/PfpUATX) au moins 24 heures à l'avance.

## Conditions {: #requirements } 

Les spécifications minimales recommandées pour exécuter un nœud sont indiquées dans le tableau suivant. Pour nos déploiements MainNet sur Kusama et Polkadot, les besoins en disques augmenteront à mesure que le réseau se développera.

=== "Moonbase Alpha"
    |  Composant   |     | Conditions                                                                                                                |
    | :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------- |
    |   **CPU**    |     | 8 Cores (La vitesse la plus rapide par cœur)                                                                      |
    |   **RAM**    |     | 16 GB                                                                         |
    |   **SSD**    |     | 50 GB (pour commencer)                                                                                            |
    | **Firewall** |     | Port P2P doit être ouvert pour le trafic entrant:<br>&nbsp; &nbsp; - Source: Any<br>&nbsp; &nbsp; - Destination: 30333, 30334 TCP |

=== "Moonriver"
    |  Composant   |     | Conditions                                                                                                                |
    | :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------- |
    |   **CPU**    |     | 8 Cores (La vitesse la plus rapide par cœur)                                                                      |
    |   **RAM**    |     | 16 GB                                                                         |
    |   **SSD**    |     | 300 GB (pour commencer)                                                                              |
    | **Firewall** |     | Port P2P doit être ouvert pour le trafic entrant:<br>&nbsp; &nbsp; - Source: Any<br>&nbsp; &nbsp; - Destination: 30333, 30334 TCP |


!!! remarque
    Si vous ne voyez pas de message `Importé` (sans la balise `[Relaychain]`) lors de l'exécution d'un nœud, vous devrez peut-être revérifier la configuration de votre port.

## Ports en cours d'exécution {: #running-ports } 

Comme indiqué précédemment, les nœuds relais/parachain écouteront sur plusieurs ports. Les ports de substrate par défaut sont utilisés dans la parachain, tandis que la chaîne de relais écoutera sur le prochain port supérieur.

Les seuls ports qui doivent être ouverts pour le trafic entrant sont ceux désignés pour le P2P.

### Ports par défaut pour un nœud complet Parachain {: #default-ports-for-a-parachain-fullnode } 

|  Description   |     |                Port                 |
| :------------: | :-: | :---------------------------------: |
|    **P2P**     |     | {{ networks.parachain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.parachain.rpc }}     |
|     **WS**     |     |     {{ networks.parachain.ws }}     |
| **Prometheus** |     | {{ networks.parachain.prometheus }} |

### Ports par défaut de la Chaîne de relais intégrée {: #default-ports-of-embedded-relay-chain } 

|  Description   |     |                 Port                  |
| :------------: | :-: | :-----------------------------------: |
|    **P2P**     |     | {{ networks.relay_chain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.relay_chain.rpc }}     |
|     **WS**     |     |     {{ networks.relay_chain.ws }}     |
| **Prometheus** |     | {{ networks.relay_chain.prometheus }} |

## Instructions d'installation - Docker {: #installation-instructions-docker } 

Un nœud Moonbeam peut être lancé rapidement à l'aide de Docker. Pour plus d'informations sur l'installation de Docker, veuillez visiter [cette page](https://docs.docker.com/get-docker/). Au moment de la rédaction, la version Docker utilisée était la 19.03.6. Lors de la connexion à Moonriver sur Kusama, il faudra quelques jours pour synchroniser complètement la chaîne de relais Kusama intégrée. Assurez-vous que votre système répond aux [exigences](#exigences).

Créez un répertoire local pour stocker les données de la chaîne :

=== "Moonbase Alpha"
    ```
    mkdir {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    mkdir {{ networks.moonriver.node_directory }}
    ```

Ensuite, assurez-vous de définir la propriété et les autorisations en conséquence pour le répertoire local qui stocke les données de la chaîne. Dans ce cas, définissez les autorisations nécessaires pour un utilisateur spécifique ou actuel (remplacez `DOCKER_USER` par l'utilisateur réel qui exécutera la commande `docker`):

=== "Moonbase Alpha"
    ```
    # chown to a specific user
    chown DOCKER_USER {{ networks.moonbase.node_directory }}

    # chown to current user
    sudo chown -R $(id -u):$(id -g) {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
     # chown to a specific user
    chown DOCKER_USER {{ networks.moonriver.node_directory }}

    # chown to current user
    sudo chown -R $(id -u):$(id -g) {{ networks.moonriver.node_directory }}
    ```

Maintenant, exécutez la commande docker run. Si vous configurez un nœud d'assemblage, assurez-vous de suivre les extraits de code pour « Assembleur ». Notez que vous devez remplacer `YOUR-NODE-NAME` à deux endroits différents.

### Noeud complet {: #full-node } 

=== "Moonbase Alpha"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonbase.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "Moonriver"
    ```
    docker run --network="host" -v "{{ networks.moonriver.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonriver.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonriver.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

### Collecteur {: #collator } 

=== "Moonbase Alpha"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonbase.chain_spec }} \
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

=== "Moonriver"
    ```
    docker run --network="host" -v "{{ networks.moonriver.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonriver.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonriver.chain_spec }} \
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

Si vous utilisez MacOS, vous pouvez trouver tous les extraits de code [ici](/snippets/text/full-node/macos-node/).

Une fois que Docker a extrait les images nécessaires, votre nœud Moonbeam (ou Moonriver) complet démarrera, affichant de nombreuses informations, telles que la spécification de la chaîne, le nom du nœud, le rôle, l'état de genèse, et plus encore :

![Démarrage du nœud complet](/images/fullnode/fullnode-docker1.png)

!!! remarque
    Si vous souhaitez exécuter un point de terminaison RPC, vous connecter à polkadot.js.org ou exécuter votre propre application, utilisez les indicateurs `--unsafe-rpc-external` et/ou `--unsafe-ws-external` pour exécuter le nœud complet avec accès externe aux ports RPC. Plus de détails sont disponibles en exécutant `moonbeam --help`.

!!! remarque
    Vous pouvez spécifier un port Prometheus personnalisé avec le drapeau `--prometheus-port XXXX` (en remplaçant `XXXX` par le numéro de port réel). Ceci est possible à la fois pour la parachain et la chaîne de relais embarquée.

La commande ci-dessus activera tous les ports exposés requis pour le fonctionnement de base, y compris les ports P2P et Prometheus (télémétrie). Cette commande est compatible avec la télémétrie Gantree Node Watchdog. Si vous souhaitez exposer des ports spécifiques, activez-les sur la ligne de commande d'exécution de Docker, comme indiqué ci-dessous. Cependant, cela empêchera le conteneur Gantree Node Watchdog (télémétrie) d'accéder au conteneur moonbeam, alors ne le faites pas lors de l'exécution d'un assembleur à moins que vous ne compreniez [docker networking] (https://docs.docker.com/network/ ).

```
docker run -p {{ networks.relay_chain.p2p }}:{{ networks.relay_chain.p2p }} -p {{ networks.parachain.p2p }}:{{ networks.parachain.p2p }} -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} #rest of code goes here
```

Pendant le processus de synchronisation, vous verrez des messages à la fois de la chaîne de relais intégrée et de la parachain (sans balise). Ces messages affichent un bloc cible (état du réseau en direct) et un meilleur bloc (état synchronisé du nœud local).

![Démarrage du nœud complet](/images/fullnode/fullnode-docker2.png)

!!! remarque
    Il faudra quelques jours pour synchroniser complètement la chaîne de relais Kusama intégrée. Assurez-vous que votre système répond aux [exigences](#exigences).

Si vous avez suivi les instructions d'installation de Moonbase Alpha, une fois synchronisé, vous aurez un nœud de Moonbase Alpha TestNet fonctionnant localement !

Si vous avez suivi les instructions d'installation de Moonriver, une fois synchronisé, vous serez connecté à des pairs et verrez des blocs se produire sur le réseau Moonriver ! Notez que dans ce cas, vous devez également vous synchroniser avec la chaîne de relais Kusama, ce qui peut prendre quelques jours.

## Instructions d'installation - Binaire {: #installation-instructions-binary } 

Cette section décrit le processus d'utilisation du binaire de version et d'exécution d'un nœud complet Moonbeam en tant que service systemd. Les étapes suivantes ont été testées sur une installation Ubuntu 18.04. Moonbeam peut fonctionner avec d'autres versions de Linux, mais Ubuntu est actuellement la seule version testée.

Pour créer manuellement les binaires vous-même, consultez le guide [Compile Moonbeam Binary](/node-operators/networks/compile-binary).

### Utiliser la version binaire {: #use-the-release-binary } 

Il existe plusieurs façons de commencer avec le binaire Moonbeam. Vous pouvez compiler le binaire vous-même, mais l'ensemble du processus peut prendre environ 30 minutes pour installer les dépendances et construire le binaire. Si vous êtes intéressé par cette voie, consultez la page [Compiler le binaire](/) de notre documentation.

Ou vous pouvez utiliser le [release binary](https://github.com/PureStake/moonbeam/releases) pour commencer tout de suite.

Utilisez `wget` pour récupérer la dernière version du binaire :


=== "Moonbase Alpha"
    ```
    wget https://github.com/PureStake/moonbeam/releases/download/{{ networks.moonbase.parachain_release_tag }}/moonbeam
    ```

=== "Moonriver"
    ```
    wget https://github.com/PureStake/moonbeam/releases/download/{{ networks.moonriver.parachain_release_tag }}/moonbeam
    ``` 

To verify that you have downloaded the correct version, you can run `sha256sum moonbeam` in your terminal, you should receive the following output:

=== "Moonbase Alpha"
    ```
    {{ networks.moonbase.parachain_sha256sum }}
    ```

=== "Moonriver"
    ```
    {{ networks.moonriver.parachain_sha256sum }}
    ```

Une fois que vous avez récupéré le binaire, vous pouvez l'utiliser pour exécuter le service systemd.

### Exécution du service Systemd {: #running-the-systemd-service } 

Les commandes suivantes configureront tout ce qui concerne l'exécution du service.

Tout d'abord, créons un compte de service pour exécuter le service :

=== "Moonbase Alpha"
    ```
    adduser moonbase_service --system --no-create-home
    ```

=== "Moonriver"
    ```
    adduser moonriver_service --system --no-create-home
    ```

Ensuite, créez un répertoire pour stocker le binaire et les données. Assurez-vous de définir la propriété et les autorisations en conséquence pour le répertoire local qui stocke les données de la chaîne. :

=== "Moonbase Alpha"
    ```
    mkdir {{ networks.moonbase.node_directory }}
    chown moonbase_service {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    mkdir {{ networks.moonriver.node_directory }}
    chown moonriver_service {{ networks.moonriver.node_directory }}
    ```

Maintenant, copiez le binaire construit dans la dernière section dans le dossier créé. Si vous [compilé le binaire](/node-operators/networks/compile-binary/) vous-même, vous devrez copier le binaire dans le répertoire cible (`./target/release/{{ networks.moonbase.binary_name }}`). Sinon, copiez le binaire Moonbeam à la racine :

=== "Moonbase Alpha"
    ```
    cp ./{{ networks.moonbase.binary_name }} {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    cp ./{{ networks.moonriver.binary_name }} {{ networks.moonriver.node_directory }}
    ```

L'étape suivante consiste à créer le fichier de configuration systemd. Si vous configurez un nœud d'assemblage, assurez-vous de suivre les extraits de code pour « Assembleur ». Notez que vous devez :

  - Remplacez `YOUR-NODE-NAME` à deux endroits différents
  - Vérifiez que le binaire est dans le bon chemin comme décrit ci-dessous (_ExecStart_)
  - Vérifiez le chemin de base si vous avez utilisé un autre répertoire
  - Nommez le fichier `/etc/systemd/system/moonbeam.service`

#### Nœud complet {: #full-node } 

=== "Moonbase Alpha"
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
         --chain {{ networks.moonbase.chain_spec }} \
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

=== "Moonriver"
    ```
    [Unit]
    Description="Moonriver systemd service"
    After=network.target
    StartLimitIntervalSec=0
    
    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonriver_service
    SyslogIdentifier=moonriver
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonriver.node_directory }}/{{ networks.moonriver.binary_name }} \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonriver.node_directory }} \
         --chain {{ networks.moonriver.chain_spec }} \
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
#### Collecteur {: #collator } 

=== "Moonbase Alpha"
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
         --chain {{ networks.moonbase.chain_spec }} \
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

=== "Moonriver"
    ```
    [Unit]
    Description="Moonriver systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonriver_service
    SyslogIdentifier=moonriver
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonriver.node_directory }}/{{ networks.moonriver.binary_name }} \
         --validator \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonriver.node_directory }} \
         --chain {{ networks.moonriver.chain_spec }} \
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

!!! remarque
     Vous pouvez spécifier un port Prometheus personnalisé avec le drapeau `--prometheus-port XXXX` (en remplaçant `XXXX` par le numéro de port réel). Ceci est possible à la fois pour la parachain et la chaîne de relais embarquée.

Vous y êtes presque! Enregistrez-vous et démarrez le service en exécutant :

```
systemctl enable moonbeam.service
systemctl start moonbeam.service
```

Et finalement, vérifiez que le service fonctionne:

```
systemctl status moonbeam.service
```

![Service Status](/images/fullnode/fullnode-binary1.png)

Vous pouvez également vérifier les journaux en exécutant :

```
journalctl -f -u moonbeam.service
```

![Service Logs](/images/fullnode/fullnode-binary2.png)

## Drapeaux avancés et Options {: #advanced-flags-and-options } 

--8<-- 'text/setting-up-node/advanced-flags.md'

## Mise à jour du client {: #updating-the-client } 

Au fur et à mesure que le développement de Moonbeam se poursuit, il sera parfois nécessaire de mettre à niveau le logiciel de votre nœud. Les opérateurs de nœuds seront informés sur notre [canal Discord](https://discord.gg/PfpUATX) lorsque des mises à niveau sont disponibles et si elles sont nécessaires (certaines mises à niveau client sont facultatives). Le processus de mise à niveau est simple et est le même pour un nœud complet ou un assembleur.

Tout d'abord, arrêtez le conteneur docker ou le service systemd :

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Ensuite, installez la nouvelle version en répétant les étapes décrites précédemment, en vous assurant que vous utilisez la dernière balise disponible. Après la mise à jour, vous pouvez redémarrer le service.

### Purge de la chaîne {: #purging-the-chain } 

Parfois, Moonbase Alpha peut être purgé et réinitialisé lors de mises à niveau majeures. Comme toujours, les opérateurs de nœuds seront avertis à l'avance (via notre [canal Discord](https://discord.gg/PfpUATX)) si cette mise à jour s'accompagne d'une purge. Vous pouvez également purger votre nœud si votre répertoire de données individuel est corrompu.

Pour ce faire, arrêtez d'abord le conteneur docker ou le service systemd :

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Ensuite, supprimez le contenu du dossier où sont stockées les données de la chaîne (à la fois pour la parachain et la chaîne de relais) :

```
sudo rm -rf {{ networks.moonbase.node_directory }}/*
```

Enfin, installez la version la plus récente en répétant les étapes décrites précédemment, en vous assurant d'utiliser la dernière balise disponible. Si tel est le cas, vous pouvez démarrer un nouveau nœud avec un nouveau répertoire de données.

## Télémétrie {: #telemetry } 

Pour activer le serveur de télémétrie de votre nœud Moonbase Alpha ou Moonriver, vous pouvez suivre [ce tutoriel](/node-operators/networks/telemetry/).

L'exécution de la télémétrie sur un nœud complet n'est pas nécessaire. Cependant, il est obligatoire de le faire pour les assembleurs.

Vous pouvez également consulter la [télémétrie Alpha de Moonbase] (https://telemetry.polkadot.io/#list/Moonbase%20Alpha) et la [télémétrie Moonriver] (https://telemetry.polkadot.io/#list/Moonriver ) Les données.

## Journaux et dépannage {: #logs-and-troubleshooting } 

Vous verrez les journaux de la chaîne de relais ainsi que de la parachain. La chaîne de relais sera préfixée par `[Relaychain]`, tandis que la parachain n'a pas de préfixe.

### Ports P2P non ouverts {: #p2p-ports-not-open } 

Si vous ne voyez pas de message `Importé` (sans la balise `[Relaychain]`), vous devez vérifier la configuration du port P2P. Le port P2P doit être ouvert au trafic entrant.

### En synchronisation {: #in-sync } 

Les deux chaînes doivent être synchronisées à tout moment, et vous devriez voir les messages « Importé » ou « Idle » et avoir des pairs connectés.

### Inadéquation de la genèse {: #genesis-mismatching } 

Le Moonbase Alpha TestNet est souvent mis à niveau. Par conséquent, vous pouvez voir le message suivant :

```
DATE [Relaychain] Bootnode with peer id `ID` is on a different
chain (our genesis: GENESIS_ID theirs: OTHER_GENESIS_ID)
```

Cela signifie généralement que vous exécutez une ancienne version et que vous devrez effectuer une mise à niveau.

Nous annonçons les mises à niveau (et la purge de la chaîne correspondante) via notre [canal Discord] (https://discord.gg/PfpUATX) au moins 24 heures à l'avance.

