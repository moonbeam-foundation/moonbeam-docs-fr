---
title: Extraits de code
description: Afin de faciliter la prise en main de Moonbeam, voici des extraits de code pour chacun des tutoriels que nous avons créés.
---

# Extraits de code

## Configuration d'un nœud Moonbeam local {: #setting-up-a-local-moonbeam-node } 

**Cloner le repo moonbeam-tutorials:**

```
git clone -b {{ networks.development.build_tag }} https://github.com/PureStake/moonbeam
cd moonbeam
```

**Installez substrate et ses pré-requis:**

```
--8<-- 'code/setting-up-node/substrate.md'
```

**Ajoutez Rust au chemin du système:**

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

**Créez le nœud de développement:**

```
--8<-- 'code/setting-up-node/build.md'
```

**Exécutez le nœud en mode développement:**

```
--8<-- 'code/setting-up-node/runnode.md'
```

**Purgez la chaîne, nettoyez toutes les anciennes données de l'exécution d'un nœud 'dev' dans le passé:**

```
./target/release/moonbeam-development purge-chain --dev
```

**Exécutez le nœud en mode développement et en supprimant les informations de bloc mais en imprimant les erreurs dans la console:**

```
./target/release/moonbeam-development --dev -lerror
```

## Compte Genèse {: #genesis-account } 

--8<-- 'text/metamask-local/dev-account.md'

## Comptes de développement {: #development-accounts } 

--8<-- 'code/setting-up-node/dev-accounts.md'

--8<-- 'code/setting-up-node/dev-testing-account.md'

## MetaMask {: #metamask } 

**Détails du nœud de développement Moonbeam:**

--8<-- 'text/metamask-local/development-node-details.md'

**TestNet Moonbase Alpha:**

=== "Moonbeam Development Node"

    - Nom du réseau: `Moonbeam Dev`
    - Nouvelle URL de RPC: `{{ networks.development.rpc_url }}`
    - ID de chaîne: `{{ networks.development.chain_id }}`
    - Symbole (facultatif): `DEV`
    - URL de l'explorateur de blocs (facultatif): `{{ networks.development.block_explorer }}`

=== "Moonbase Alpha TestNet"

    - Nom du réseau: `Moonbase Alpha`
    - Nouvelle URL de RPC: `{{ networks.moonbase.rpc_url }}`
    - ID de chaîne: `{{ networks.moonbase.chain_id }}`
    - Symbole (facultatif): `DEV`
    - URL de l'explorateur de blocs (facultatif): `{{ networks.moonbase.block_explorer }}`

=== "Moonriver"

    - Nom du réseau: `Moonriv
    er`
    - Nouvelle URL de RPC: `{{ networks.moonriver.rpc_url }}`
    - ID de chaîne: `{{ networks.moonriver.chain_id }}`
    - Symbole (facultatif): `MOVR`
    - URL de l'explorateur de blocs (facultatif): `{{ networks.moonriver.block_explorer }}`