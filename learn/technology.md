---
title: La technologie
description: Moonbeam est construit à l'aide de Rust et du framework Substrate, permettant des outils riches pour la mise en œuvre, mais permettant également la spécialisation et l'optimisation.
---

# La technologie

## La pile de développement Moonbeam

Moonbeam est une plate-forme blockchain de contrats intelligents construite en langage de programmation Rust et utilisant le framework Substrate.  

### Langage de programmation Rust

Rust est un bon langage pour implémenter une blockchain, car il est très performant comme C et C ++, mais possède des fonctionnalités de sécurité de la mémoire intégrées qui sont appliquées au moment de la compilation, ce qui évite de nombreux bogues courants et problèmes de sécurité pouvant survenir dans les implémentations en C et C ++.

### Substrate Framework

Substrate fournit un ensemble complet d'outils pour créer des chaînes de blocs, y compris un environnement d'exécution qui active une fonction de transition d'état générique et un ensemble de modules enfichables qui fournissent des implémentations de divers sous-systèmes de chaînes de blocs.

Moonbeam s'appuie sur plusieurs palettes cadres de substrate existantes pour fournir des services et des fonctionnalités de blockchain clés, y compris les structures de données de blockchain de base, la mise en réseau peer-to-peer, les mécanismes de consensus, les comptes, les actifs et les soldes.  Les palettes personnalisées et la logique du runtime implémentent le comportement et les fonctionnalités spécifiques à Moonbeam, tels que l'intégration de jetons inter-chaînes. Pour les palettes à effet de levier, Moonbeam s'efforcera de rester aussi proche que possible de la base de code de Substrate en amont et d'incorporer en permanence des corrections de bogues, des améliorations et de nouvelles fonctionnalités de Substrate.

## Durée d'exécution de la blockchain

L'environnement d'exécution principal de Moonbeam spécifie la fonction de transition d'état et le comportement de la blockchain Moonbeam. Le runtime Moonbeam est construit à l'aide de [FRAME](/resources/glossary/#substrate-frame-pallets). Il comprend plusieurs palettes standard ainsi que plusieurs palettes personnalisées. Le runtime est compilé en binaire [WebAssembly (Wasm)](/resources/glossary/#webassemblywasm) et natif. Ces versions compilées seront exécutées dans les environnements de nœuds Polkadot Relay Chain et Moonbeam.  

!!! remarque
    Les palettes cadre de substrate sont une collection de modules basés sur Rust fournissant les fonctionnalités requises lors de la création d'une blockchain. WebAssembly est une norme ouverte qui définit un format de code binaire portable. Il est pris en charge par différents langages de programmation, compilateurs et navigateurs. Trouvez plus de définitions [dans notre glossaire](/resources/glossary/).

Certaines des palettes cadre clés de substrate utilisées dans le runtime Moonbeam comprennent:

 - **Soldes** — Prise en charge des comptes, des soldes et des transferts.
 - **EVM** — Implémentation EVM complète basée sur Rust basée sur SputnikVM. Fournit la logique de transition d'état pour les contrats intelligents basés sur Moonbeam.
 - **Ethereum** — Fournit une émulation du traitement des blocs Ethereum pour l'EVM.
 - **RPC-Ethereum** — Implémentation Web3 RPC dans Substrate.
 - **Conseil** — Comprend les mécanismes de gouvernance autour du conseil et des propositions.
 - **Démocratie** — Fonctionnalité pour le vote public des détenteurs de jetons pondérés par les enjeux.
 - **Exécutif** — Couche d'orchestration qui distribue les appels à d'autres modules d'exécution.
 - **Indices** — Prise en charge de noms courts conviviaux pour les adresses de compte.
 - **Systeme** — Fournit des types de bas niveau, des fonctions de stockage et de blockchain.
 - **Trésorerie** — Trésorerie en chaîne qui peut être utilisée pour financer des biens publics tels qu'une parachain.

Moonbeam utilise également la bibliothèque Cumulus pour fournir une intégration à la chaîne de relais Polkadot.

En plus de ces palettes cadre de substrate, nous implémenterons des modules avec des fonctionnalités spécifiques à Moonbeam, y compris la mécanique et les récompenses de l'assembleuse et d'autres blocs de construction pour les développeurs.

## Architecture de compatibilité Ethereum

Les contrats intelligents sur Moonbeam peuvent être mis en œuvre en utilisant Solidity, Vyper et tout autre langage capable de compiler des contrats intelligents en bytecode compatible EVM. Moonbeam vise à fournir un environnement sécurisé et à faible friction pour le développement, les tests et l'exécution de contrats intelligents compatible avec la chaîne d'outils existante des développeurs Ethereum.  

Le comportement d'exécution et la sémantique des contrats intelligents basés sur Moonbeam s'efforceront d'être aussi proches que possible d'Ethereum Layer 1. Moonbeam est un seul fragment, donc les appels inter-contrats ont la même sémantique d'exécution synchrone que sur Ethereum Layer 1.

![Diagram showing the interactions made possible through Moonbeam's Ethereum compatibility](/images/technology-diagram.png)

Un flux d'interaction de haut niveau est illustré ci-dessus. Un appel Web3 RPC d'un DApp ou d'un outil de développement Ethereum existant, tel que Truffle, est reçu par un nœud Moonbeam. Le nœud aura à la fois des RPC Web3 et des RPC de substrate disponibles, ce qui signifie que vous pouvez utiliser les outils Ethereum ou Substrate lors de l'interaction avec un nœud Moonbeam. Ces appels RPC sont gérés par les fonctions d'exécution Substrate associées. Le runtime Substrate vérifie les signatures et gère tous les extrinsèques. Les appels de contrat intelligent sont finalement transmis à l'EVM pour exécuter les transitions d'état.

En basant notre implémentation EVM sur la  Pallet-EVM Substrate, nous obtenons une implémentation EVM complète basée sur Rust et le support de l'équipe d'ingénierie de Parity.

