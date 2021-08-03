---
title: Compiler le code binaire
description: Comment compiler le code binaire Moonbeam pour exécuter un nœud Parachain complet, accéder aux points de terminaison RPC et produire des blocs pour le réseau Moonbeam.
---

# Compiler le code Binaire

![Full Node Moonbeam Banner](/images/fullnode/compile-binary-banner.png)

## Introduction {: #introduction } 

Il existe plusieurs façons de commencer à exécuter un nœud complet sur le réseau Moonbeam. Ce guide passe en revue le processus de compilation du code binaire Moonbeam à partir du code source de Rust. Pour un aperçu plus général des nœuds en cours d'exécution, ou pour démarrer avec Docker, consultez la page [Exécuter un nœud](/node-operators/networks/full-node) de notre documentation.

Ce guide est destiné aux personnes ayant de l'expérience dans la compilation de nœuds blockchain basés sur [Substrate](https://substrate.dev/). Un nœud de parachain est similaire à un nœud de substrate typique, mais il existe quelques différences. Un nœud de parachain de substrate aura un plus grand build car il contient du code pour exécuter la parachain elle-même, ainsi que du code pour synchroniser la chaîne de relais et faciliter la communication entre les deux. En tant que tel, cette version est assez volumineuse et peut prendre plus de 30 minutes et nécessiter 32 Go de mémoire.

## Compiler le code Binaire {: #compiling-the-binary } 

Les commandes suivantes construiront la dernière version de la parachain Moonbeam.

Tout d'abord, commençons par cloner le repo Moonbeam.

```
git clone https://github.com/PureStake/moonbeam
cd moonbeam
```

Vérifions la dernière version:

```
git checkout tags/$(git tag | tail -1)
```

Ensuite, installez un environnement de développement Substrate, comprenant Rust, en exécutant:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Enfin, le code binaire de la parachain:

```
cargo build --release
```

![Compiler le code Binaire](/images/fullnode/compile-binary1.png)

Si _cargo not found error_ apparaît dans le terminal, ajoutez manuellement Rust à votre chemin système ou redémarrez votre système:

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

Maintenant vous pouvez utiliser le code Binaire Moonbeam pour [exécuter un service systemd](/node-operators/networks/full-node/#running-the-systemd-service).
