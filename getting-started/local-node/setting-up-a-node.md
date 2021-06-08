---
title: Configuration d'un nœud
description: Suivez ce tutoriel pour apprendre à configurer votre premier nœud Moonbeam. Vous apprendrez également à le connecter et à le contrôler avec l'interface graphique Polkadot JS.
---

# Configuration d'un nœud Moonbeam et connexion à l'interface graphique de Polkadot JS

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//p_0OAHSlHNM' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Vous pouvez trouver tout le code pertinent pour ce tutoriel sur <a href="{{ config.site_url }}resources/code-snippets/">code snippets page</a></div>

## Introduction

Ce guide décrit les étapes nécessaires pour créer un nœud de développement pour tester les fonctionnalités de compatibilité Ethereum de Moonbeam.

!!! note
    Ce tutoriel a été créé à l'aide du tag tutorial-v7 de {{ networks.development.build_tag }}  de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.development.build_tag }}). La plate-forme Moonbeam et les composants [Frontier](https://github.com/paritytech/frontier) sur lesquels elle s'appuie pour la compatibilité Ethereum basée sur substrate sont toujours en cours de développement.
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Un nœud de développement Moonbeam est votre propre environnement de développement personnel pour créer et tester des applications sur Moonbeam. Pour les développeurs Ethereum, il est comparable à Ganache. Il vous permet de démarrer rapidement et facilement sans les frais généraux d'une chaîne de relais. Vous pouvez faire tourner votre nœud avec l’option `--sealing` qui permet de créer des blocs instantanément, manuellement ou à un intervalle personnalisé après la réception des transactions. Par défaut, un bloc sera créé lors de la réception d'une transaction, ce qui est similaire à la fonction instamine de Ganache. 

Si vous allez jusqu'à la fin de ce guide, vous aurez un nœud de développement Moonbeam fonctionnant dans votre environnement local, avec 10 [comptes préfinancés](#pre-funded-development-accounts), et pourrez le connecter à l'interface graphique par défaut de Polkadot JS.

Il existe deux façons de commencer à exécuter un nœud Moonbeam: vous pouvez utiliser [docker pour exécuter un binaire pré-construit](#getting-started-with-docker) ou vous pouvez [compiler le binaire localement](#getting-started-with-the-binary-file)  et configurer un nœud de développement vous-même.  L'utilisation de Docker est un moyen rapide et pratique de commencer car vous n'aurez pas à installer Substrate et toutes ses dépendances, et vous pouvez également ignorer la création du processus de nœud. Cela vous oblige à [installer Docker](https://docs.docker.com/get-docker/). D'un autre côté, si vous décidez que vous souhaitez passer par le processus de création de votre propre nœud de développement, cela peut prendre environ plus ou moins 30 minutes en fonction de votre matériel.

## Premiers pas avec Docker

L'utilisation de Docker vous permet de faire tourner un nœud en quelques secondes. Une fois Docker installé, vous pouvez exécuter la commande suivante pour télécharger l'image correspondante:

```
docker pull purestake/moonbeam:{{ networks.development.build_tag }}
```

La fin des logs de la console devrait ressembler à ceci:

![Docker - imaged pulled](/images/setting-up-a-node/setting-up-node-1.png)

Une fois l'image Docker téléchargée, l'étape suivante consiste à exécuter l'image.

Vous pouvez exécuter l'image Docker en utilisant les éléments suivants:

=== "Ubuntu"
    ```
    docker run --rm --name {{ networks.development.container_name }} --network host \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev
    ```

=== "MacOS"
    ```
    docker run --rm --name {{ networks.development.container_name }} -p 9944:9944 -p 9933:9933 \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev --ws-external --rpc-external
    ```

=== "Windows"
    ```
    docker run --rm --name {{ networks.development.container_name }} -p 9944:9944 -p 9933:9933 \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev --ws-external --rpc-external
    ```

Cela devrait faire tourner un nœud de développement Moonbeam en mode ‘instant seal’ pour les tests en local, de sorte que les blocs soient créés instantanément à mesure que les transactions sont reçues. En cas de succès, vous devriez voir une sortie montrant un état inactif en attente de création de blocs:

![Docker - output shows blocks being produced](/images/setting-up-a-node/setting-up-node-2.png)

Pour plus d'informations sur certains des indicateurs et options utilisés dans l'exemple, consultez [Common Flags and Options](#common-flags-and-options). Si vous souhaitez voir une liste complète de tous les indicateurs, options et sous-commandes, ouvrez le menu d'aide en exécutant:

```
docker run --rm --name {{ networks.development.container_name }} \
purestake/moonbeam \
--help
```

Pour continuer avec le didacticiel, la section suivante n'est pas nécessaire car vous avez déjà créé un nœud avec Docker. Vous pouvez passer à la [connexion des applications JS de Polkadot à un nœud local Moonbeam](#connecting-polkadot-js-apps-to-a-local-moonbeam-node).

## Premiers pas avec le fichier binaire

!!! note
    Si vous savez ce que vous faites, vous pouvez directement télécharger les binaires précompilés attachés à chaque version sur la [page des sorties Moonbeam](https://github.com/PureStake/moonbeam/releases). Ceux-ci ne fonctionneront pas dans tous les systèmes. Par exemple, les binaires ne fonctionnent qu'avec Linux x86-64 avec des versions spécifiques de dépendances. Le moyen le plus sûr d'assurer la compatibilité est de compiler le binaire dans le système à partir duquel il sera exécuté.

Premièrement, commençons par cloner une balise spécifique du repo Moonbeam que vous pouvez trouver ici:

[https://github.com/PureStake/moonbeam/](https://github.com/PureStake/moonbeam/)

```
git clone -b {{ networks.development.build_tag }} https://github.com/PureStake/moonbeam
cd moonbeam
```

Ensuite, installez Substrate et tous ses prérequis (y compris Rust) en exécutant:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Une fois que vous avez suivi toutes les procédures ci-dessus, il est temps de créer le nœud de développement en exécutant:

```
--8<-- 'code/setting-up-node/build.md'
```

Si une erreur _cargo not found error_ apparaît dans le terminal, ajoutez manuellement Rust à votre chemin système (ou redémarrez votre système):

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

!!! note
    La construction initiale prendra un certain temps. En fonction de votre matériel, vous devez vous attendre à environ 30 minutes pour que le processus de construction se termine.

Voici à quoi devrait ressembler le build en fin de sortie:

![End of build output](/images/setting-up-a-node/setting-up-node-3.png)

Ensuite, vous voudrez exécuter le nœud en mode dev à l'aide de la commande suivante:

```
--8<-- 'code/setting-up-node/runnode.md'
```

!!! note
    Pour les personnes qui ne sont pas familiarisées avec Substrate, le flag `--dev` est un moyen d'exécuter un nœud basé sur Substrate dans une configuration de développeur à nœud unique à des fins de test. Vous pouvez en savoir plus sur `--dev` dans [ce tutoriel Substrate](https://substrate.dev/docs/en/tutorials/create-your-first-substrate-chain/interact).

Vous devriez voir en sortie ce qui suit, montrant un état inactif en attente de la production de blocs:

![Output shows blocks being produced](/images/setting-up-a-node/setting-up-node-4.png)

Pour plus d'informations sur certains des indicateurs et options utilisés dans l'exemple, consultez [Common Flags and Options](#common-flags-and-options). Si vous souhaitez voir une liste complète de tous les indicateurs, options et sous-commandes, ouvrez le menu d'aide en exécutant:

```
./target/release/moonbeam --help
```
## Connexion des applications JS de Polkadot à un nœud Moonbeam local

Le nœud de développement est un nœud basé sur un substrat, nous pouvons donc interagir avec lui à l'aide des outils de substrat standard. Les deux points de terminaison RPC fournis sont:

 - HTTP: `http://127.0.0.1:9933`
 - WS: `ws://127.0.0.1:9944` 

Commençons par nous y connecter avec Polkadot JS Apps. Ouvrez un navigateur sur: [https://polkadot.js.org/apps/#/explorer](https://polkadot.js.org/apps/#/explorer). Cela ouvrira Polkadot JS Apps, qui se connecte automatiquement à Polkadot MainNet.

![Polkadot JS Apps](/images/setting-up-a-node/setting-up-node-5.png)

Cliquez sur le coin supérieur gauche pour ouvrir le menu de configuration des réseaux, puis naviguez vers le bas pour ouvrir le sous-menu Développement. Là-dedans, vous voudrez activer l'option "Local Node", qui pointe Polkadot JS Apps sur `ws://127.0.0.1:9944`. Ensuite, sélectionnez le bouton Switch, et le site devrait se connecter à votre nœud de développement Moonbeam.

![Select Local Node](/images/setting-up-a-node/setting-up-node-6.png)

Avec Polkadot JS Apps connecté, vous verrez le nœud de développement Moonbeam attendre l'arrivée des transactions pour commencer à produire des blocs.

![Select Local Node](/images/setting-up-a-node/setting-up-node-7.png)

## Interroger l'état du compte

Avec la sortie de [Moonbase Alpha v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/), Moonbeam fonctionne désormais sous un format de compte unique, qui est le H160 de style Ethereum et est désormais également pris en charge dans Polkadot JS Apps. Pour vérifier le solde d'une adresse, vous pouvez simplement importer votre compte dans l'onglet Comptes. Vous pouvez trouver plus d'informations dans la section [comptes unifiés](/learn/unified-accounts/).
 
Néanmoins, en tirant parti des capacités RPC complètes d'Ethereum de Moonbeam, vous pouvez également utiliser [MetaMask](/getting-started/local-node/using-metamask/) pour vérifier le solde de cette adresse. En outre, vous pouvez également utiliser d'autres outils de développement, tels que [Remix](/getting-started/local-node/using-remix/) et [Truffle](/getting-started/local-node/using-truffle/).

## commandes courantes, Flags et Options

### Purger la chaîne

Lors de l'exécution d'un nœud via le fichier binaire, les données sont stockées dans un répertoire local généralement situé dans `~/.local/shared/moonbeam/chains/development/db`. Si vous souhaitez démarrer une nouvelle instance du nœud, vous pouvez soit supprimer le contenu du dossier, soit exécuter la commande suivante dans le dossier `moonbeam` :

```
./target/release/moonbeam purge-chain --dev -y
```

Cela supprimera le dossier de données, notez que toutes les données de la chaîne sont maintenant perdues.

Si vous avez utilisé Docker, le dossier de données est lié au conteneur Docker lui-même.
### Flags de noeuds

Les flags ne prennent pas d'argument. Pour utiliser un flag, ajoutez-le à la fin d'une commande. Par example:

```
--8<-- 'code/setting-up-node/runnode.md'
```

- `--dev`: Spécifie la chaîne de développement
- `--no-telemetry`: Désactivez la connexion au serveur de télémétrie Substrate. Pour les chaînes globales, la télémétrie est activée par défaut. La télémétrie n'est pas disponible si vous exécutez un noeud de développement (`--dev`) .
- `--tmp`: Exécute un nœud temporaire dans lequel toute la configuration sera supprimée à la fin du processus
- `--rpc-external`: Écoutez toutes les interfaces RPC
- `--ws-external`: Écoutez toutes les interfaces Websocket

### Options de noeud

Les options acceptent un argument à droite de l'option. Par example:

```
--8<-- 'code/setting-up-node/runnodewithsealinginterval.md'
```

- `-l <log pattern>` ou `--log <log pattern>`: Définit un filtre de journalisation personnalisé. La syntaxe du modèle de journal est `<target>=<level>`. Par exemple, pour imprimer tous les journaux RPC, la commande ressemblerait à ceci : `-l rpc=trace`.
- `--sealing <interval>`: Quand les blocs doivent être scellés dans le service de développement. Arguments acceptés pour l'intervalle : `instant`, `manual`, ou un nombre représentant l'intervalle de temps en millisecondes (par exemple, `6000` fera que le nœud produira des blocs toutes les 6 secondes). La valeur par défaut est `instant`.
- `--rpc-port <port>`: Définit le port TCP du serveur HTTP RPC. Accepte un port comme argument.
- `--ws-port <port>`: Définit le port TCP du serveur WebSockets RPC. Accepte un port comme argument.

Pour une liste complète des flags et des options, lancez votre nœud de développement Moonbeam avec `--help` ajouté à la fin de la commande.

## Flags et options avancées

--8<-- 'text/setting-up-node/advanced-flags.md'

Par exemple, lors de l'exécution du binaire :

```
./target/release/moonbeam --dev --execution=Native --ethapi=debug,trace
```

## Comptes de développement préfinancés

Votre nœud de développement Moonbeam est livré avec dix comptes préfinancés pour le développement. Les adresses sont dérivées de la mnémonique de développement canonique de Substrate: 

```
bottom drive obey lake curtain smoke basket hold race lonely fit walk
```

--8<-- 'code/setting-up-node/dev-accounts.md'

Consultez la section [Utiliser MetaMask](/getting-started/local-node/using-metamask/) pour commencer à interagir avec vos comptes.

Le nœud de développement comprend également un compte préfinancé utilisé à des fins de test :

--8<-- 'code/setting-up-node/dev-testing-account.md'
