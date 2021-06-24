---
title: Télémétrie
description: Comment exécuter la télémétrie pour un nœud complet de Parachain pour le réseau Moonbeam
---

# Télémétrie pour un Noeud Complet

![Telemetry Moonbeam Banner](/images/fullnode/telemetry-banner.png)

## Introduction

Avec la sortie de Moonbase Alpha v6, vous pouvez créer un nœud qui se connecte au Moonbase Alpha TestNet. Vous pouvez vérifier ces étapes dans ce didacticiel.

Ce guide fournira les étapes nécessaires pour activer le serveur de télémétrie pour votre nœud Moonbase Alpha.

!!! note
    Les étapes décrites dans ce guide concernent une instance de télémétrie différente de la télémétrie Polkadot standard activée par défaut (vous pouvez exécuter des nœuds sans télémétrie à l’aide de l’indicateur `—no-telemetry`). Les étapes décrites dans ce guide sont obligatoires uniquement pour les nœuds d’assembleur.

## Récapitulatif de l’Éxportateur de Télémétrie

Moonbeam exécutera un serveur de télémétrie qui collecte les métriques Prometheus de tous les nœuds de parachain Moonbeam sur le réseau. L’exécuter nous sera d’une grande aide pendant notre phase de développement.

L’exportateur de métriques peut s’exécuter soit en tant que side-car Kubernetes, soit en tant que binaire local si vous exécutez une machine virtuelle. Il transmettra les données à nos serveurs, vous n’avez donc pas à activer de ports entrants pour ce service.

Nous utilisons un service appelé [Gantree Node Watchdog](https://github.com/gantree-io/gantree-node-watchdog) pour télécharger automatiquement la télémétrie. Une fois que vous avez activé la télémétrie, vous pouvez également accéder à un serveur Prometheus / Grafana à partir de [l’ application Gantree App](https://app.gantree.io/) . Il existe des instructions détaillées sur le référentiel GitHub. Si vous avez besoin de plus d’informations, voici un démarrage rapide.

Pour l’instant, nous devons exécuter deux chiens de garde de nœuds, un pour la parachain et un pour la chaîne de relais. Cela sera mis à jour dans une prochaine version.

Pour obtenir de l’aide, contactez notre [serveur Discord](https://discord.gg/FQXm74UQ7V) ou le Gantree Discord.

## Vérification des prérequis

Avant de suivre ce didacticiel, vous devez:

1. Connectez-vous à [https://app.gantree.io](https://app.gantree.io) et créez un compte. Accédez aux clés API et copiez votre clé API.
2. Demandez une clé PCK sur notre [serveur Discord](https://discord.gg/FQXm74UQ7V)

## Exportateur de télémétrie avec Docker

Nous exécuterons deux instances du chien de garde du nœud Gantree à l’aide de Docker: une pour la parachain et une pour la chaîne de relais.

### Informations de configuration requises
 - GANTREE_NODE_WATCHDOG_API_KEY
 - GANTREE_NODE_WATCHDOG_PROJECT_ID
 - GANTREE_NODE_WATCHDOG_CLIENT_ID
 - GANTREE_NODE_WATCHDOG_PCKRC
 - GANTREE_NODE_WATCHDOG_METRICS_HOST
 
### Instructions

Tout d’abord, clonez le référentiel du client de surveillance de l’instance et créez l’image docker:

```
git clone https://github.com/gantree-io/gantree-node-watchdog
cd gantree-node-watchdog
# checkout latest release
git checkout tags/$(git tag | tail -1)
docker build .  
# get the IMAGE-NAME for use below
docker images
```

Ensuite, exécutons le conteneur docker (parachain Gantree node watchdog). Notez que vous devez remplacer les champs suivants:

 - `IMAGE-NAME` avec celle récupérée à l’étape précédente
 - `YOUR-API-KEY` avec celui fourni par https://app.gantree.io
 - `YOUR-SERVER-NAME`
 - `YOUR-PCK-KEY` avec celui demandé sur notre serveur Discord

```
docker run -it --network="host" \
-e GANTREE_NODE_WATCHDOG_API_KEY="YOUR-API-KEY" \
-e GANTREE_NODE_WATCHDOG_PROJECT_ID="moonbase-alpha" \
-e GANTREE_NODE_WATCHDOG_CLIENT_ID="YOUR-SERVER-NAME-parachain" \
-e GANTREE_NODE_WATCHDOG_PCKRC="YOUR-PCK-KEY" \
-e GANTREE_NODE_WATCHDOG_METRICS_HOST="http://127.0.0.1:9615" \
--name gantree_watchdog_parachain IMAGE-NAME
```

Maintenant, nous devons exécuter le chien de garde du nœud de relais Gantree. Notez que vous devez remplacer les mêmes informations qu’à l’étape précédente.

```
docker run -it --network="host" \
-e GANTREE_NODE_WATCHDOG_API_KEY="YOUR-API-KEY" \
-e GANTREE_NODE_WATCHDOG_PROJECT_ID="moonbase-alpha" \
-e GANTREE_NODE_WATCHDOG_CLIENT_ID="YOUR-SERVER-NAME-relay" \
-e GANTREE_NODE_WATCHDOG_PCKRC="YOUR-PCK-KEY" \
-e GANTREE_NODE_WATCHDOG_METRICS_HOST="http://127.0.0.1:9616" \
--name gantree_watchdog_relay IMAGE-NAME
```


Vous devriez voir l’attente de l’approvisionnement dans les journaux. Une fois terminé, vous pouvez vous connecter à [https://app.gantree.io](https://app.gantree.io) et sélectionner les réseaux. Vous verrez un lien `View Monitoring` Dashboard vers votre tableau de bord Prometheus / Grafana personnalisé que vous pouvez personnaliser selon vos besoins.

Une fois que tout fonctionne bien, vous pouvez mettre à jour les commandes pour qu’elles s’exécutent en mode démon. Supprimez `-it` et ajoutez `-d` à la commande ci-dessus.

## Exportateur de télémétrie avec Systemd

Nous allons exécuter deux instances du chien de garde du nœud Gantree: une pour la parachain et une pour la chaîne de relais.

### Informations de configuration requises

 - GANTREE_NODE_WATCHDOG_API_KEY
 - GANTREE_NODE_WATCHDOG_PROJECT_ID
 - GANTREE_NODE_WATCHDOG_CLIENT_ID
 - GANTREE_NODE_WATCHDOG_PCKRC
 - GANTREE_NODE_WATCHDOG_METRICS_HOST


### Instructions

Tout d’ abord, nous avons besoin de télécharger le fichier binaire de surveillance de nœud gantree de la [page de diffusion](https://github.com/gantree-io/gantree-node-watchdog/releases), et l’ extraire dans un dossier, par exemple, `/usr/local/bin`.

Ensuite, créons deux dossiers pour les fichiers de configuration:


```
mkdir -p /var/lib/gantree/parachain
mkdir -p /var/lib/gantree/relay
```

Maintenant, nous devons générer les fichiers de configuration, placer chacun dans les dossiers créés à l’étape précédente. Notez que vous devez remplacer les champs suivants:

 - `YOUR-API-KEY` avec celui fourni par [https://app.gantree.io](https://app.gantree.io)
 - `YOUR-SERVER-NAME`
 - `YOUR-PCK-KEY` avec celui demandé sur notre serveur Discord

Parachain:

```
# Contents of /var/lib/gantree/parachain/.gnw_config.json
{
  "api_key": "YOUR-API-KEY",
  "project_id": "moonbase-alpha",
  "client_id": "YOUR-SERVER-NAME-parachain",
  "pckrc": "YOUR-PCK-KEY",
  "metrics_host": "http://127.0.0.1:9615"
}
```

Chaîne de relais intégrée:

```
# Contents of /var/lib/gantree/relay/.gnw_config.json
{
  "api_key": "YOUR-API-KEY",
  "project_id": "moonbase-alpha",
  "client_id": "YOUR-SERVER-NAME-relay",
  "pckrc": "YOUR-PCK-KEY",
  "metrics_host": "http://127.0.0.1:9616"
}
```

L’étape suivante consiste à générer votre fichier de configuration systemd.

Parachain:

```
# Contents of /etc/systemd/system/gantree-parachain.service

[Unit]
Description=Gantree Node Watchdog Parachain
After=network.target

[Service]
WorkingDirectory=/var/lib/gantree/parachain
Type=simple
Restart=always
ExecStart=/usr/local/bin/gantree_node_watchdog

[Install]
WantedBy=multi-user.target
```

Chaîne de relais intégrée:

```
# Contents of /etc/systemd/system/gantree-relay.service

[Unit]
Description=Gantree Node Watchdog Relay
After=network.target

[Service]
WorkingDirectory=/var/lib/gantree/relay
Type=simple
Restart=always
ExecStart=/usr/local/bin/gantree_node_watchdog

[Install]
WantedBy=multi-user.target
```

Nous y sommes presque! Maintenant, activons et démarrons les services systemd, surveillons les journaux pour les erreurs:

```
sudo systemctl enable gantree-parachain
sudo systemctl start gantree-parachain && journalctl -f -u gantree-parachain

sudo systemctl enable gantree-relay
sudo systemctl start gantree-relay && journalctl -f -u gantree-relay
```

Vous devriez voir l’attente de l’approvisionnement dans les journaux. Une fois terminé, vous pouvez vous connecter à [https://app.gantree.io](https://app.gantree.io) et sélectionner les réseaux. Vous verrez un lien `View Monitoring` Dashboard vers votre tableau de bord Prometheus / Grafana personnalisé que vous pouvez personnaliser selon vos besoins.

## À propos de Moonbeam

Moonbeam est une plate-forme de contrat intelligent compatible Ethereum sur le réseau Polkadot qui facilite la création d’applications nativement interopérables. Cette compatibilité Ethereum permet aux développeurs de déployer des contrats intelligents Solidity existants et des frontends DApp sur Moonbeam avec des changements minimes. En tant que parachain sur le réseau Polkadot, Moonbeam bénéficiera de la sécurité partagée de la chaîne de relais Polkadot et des intégrations avec d’autres chaînes connectées à Polkadot. Actuellement, en développement actif par PureStake, Moonbeam devrait atteindre MainNet d’ici la mi-2021. En savoir plus: https://moonbeam.network/ .

## Nous contacter

Si vous avez des commentaires concernant l’exécution d’un nœud complet avec télémétrie, ou tout autre sujet lié à Moonbeam, n’hésitez pas à contacter notre serveur de développement officiel Discord .