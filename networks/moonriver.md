---
title: Moonriver
description: Une vue d'ensemble sur l'actuelle configuration Moonriver, le réseau principal de Moonbeam déployé sur Kusama, ainsi que des informations sur la façon de commencer de cette base à le construire avec Solidy.
---

# Moonriver

_Updated July 29th, 2021_

## Objectif {: #goal } 

En juin 2021, Moonriver a été lancé pour la première fois en tant que parachain sur le réseau Kusama. Moonriver est un réseau jumeau de Moonbeam, et fournit un environnement pour tester de nouveaux codes dans des conditions économiques réelles. Les développeurs peuvent désormais commencer à expérimenter et à construire sur un réseau canari incitatif connecté à Kusama. Les développeurs peuvent désormais commencer à expérimenter et à construire sur incentivized canary network connecté à Kusama. 

Afin de recueillir le plus de commentaires possibles et de fournir une résolution rapide des problèmes, nous avons mis en place un [Discord avec un canal Moonriver dédié](https://discord.gg/5TaUvbRvgM).

## Configurations initiales {: #initial-configurations } 

Il est prévu que Moonriver suive un [processus de lancement en 5 phases](https://moonbeam.network/networks/moonriver/launch/). Actuellement, Moonriver est en phase 3 du processus de lancement et présente les configurations suivantes :

- Fonctionne comme une parachaine connectée à la relay chain de Kusama
- - A un ensemble actif de {{ networks.moonriver.staking.max_collators }} collateurs
- - Il y a deux points d'accès RPC (hébergés par PureStake). Vous pouvez exécuter des nœuds complets pour accéder à leurs propres points d'accès RPC privés.

![Diagramme de Moonriver](/images/moonriver/moonriver-diagram.png)

Voici quelques variables/configurations importantes à noter :

=== "Général"
    |       Variable        |                                    Valeur                                     |
    |:---------------------:|:----------------------------------------------------------------------------:|
    |   Prix minimum du gas   |                 {{ networks.moonriver.min_gas_price }} Gwei*                 |
    |   Temps de blocage cible   |  {{ networks.moonriver.block_time }} secondes (expected to be 6     seconds)  |
    |    Limite de blocage gas     | {{ networks.moonriver.gas_block }} (expected to increase by at     least 4x) |
    | Limite de transaction de gas  |  {{ networks.moonriver.gas_tx }} (expected to increase by at     least 4x)   |
    |     Point d'accès RPC      |                    {{ networks.moonriver.rpc_url }}    }                     |
    |     Point d'accès WSS      |                       {{ networks.moonriver.wss_url }}                       |

=== "Governance"
    |         Variable         |                                                             Valeur                                                              |
    |:------------------------:|:------------------------------------------------------------------------------------------------------------------------------:|
    |      Période de vote       |      {{ networks.moonriver.democracy.vote_period.blocks}} blocks ({{networks.moonriver.democracy.vote_period.days}} jours)      |
    | Période de vote en accéléré | {{ networks.moonriver.democracy.fast_vote_period.blocks}} blocks ({{networks.moonriver.democracy.fast_vote_period.days}} jours) |
    |     Période de promulgation     |     {{ networks.moonriver.democracy.enact_period.blocks}} blocks ({{networks.moonriver.democracy.enact_period.days}} jours)      |
    |     Période de réflexion      |      {{ networks.moonriver.democracy.cool_period.blocks}} blocks ({{networks.moonriver.democracy.cool_period.days}} jours)      |
    |     Dépôt minimum      |                                    {{ networks.moonriver.democracy.    min_deposit }} MOVR                                     |
    |      Votes maximums       |                                        {{ networks.moonriver.    democracy.max_votes }}                                        |
    |    Propositions maximales     |                                      {{ networks.moonriver.democracy.    max_proposals }}                                      |

=== "Staking"
    |             Variable             |                                                     Valeur                                                     |
    |:--------------------------------:|:-------------------------------------------------------------------------------------------------------------:|
    |     Participation minimale à la nomination     |                           {{ networks.moonriver.staking.    min_nom_stake }} tokens                           |
    |        Nomination minimale        |                           {{ networks.moonriver.staking.    min_nom_amount}} tokens                           |
    | Nombre maximum de nominateurs par collators |                             {{ networks.moonriver.staking.    max_nom_per_col }}                              |
    | Maximum de collators par nominateur  |                             {{ networks.moonriver.staking.    max_col_per_nom }}                              |
    |              Round               | {{ networks.moonriver.staking.round_blocks }} blocks ({{     networks.moonriver.staking.round_hours }} heures) |
    |          Durée des obligations           |                             {{ networks.moonriver.staking.    bond_lock }} rounds                             |

_*Plus d'informations sur [dénominations des tokens](#token-denominations)_

## Commencez maintenant {: #get-started } 

--8<-- 'text/moonriver/connect.md'

## Télémétrie {: #telemetry } 

Vous pouvez consulter les informations télémétriques actuelles de Moonriver en visitant [ce lien](https://telemetry.polkadot.io/#list/Moonriver).

## Tokens {: #tokens } 

Consultez le site de la Fondation Moonbeam pour plus d'informations sur le [token Moonriver](https://moonbeam.foundation/moonriver-token/). 

### Dénominations des tokens {: #token-denominations } 

La plus petite unité de Moonriver, tout comme l'Ethereum, est un Wei. Il faut 10^18 Wei pour fabriquer un Moonriver. Les dénominations sont les suivantes :

|      Unité      |   Moonriver (MOVR)   |              Wei              |
|:--------------:|:--------------------:|:-----------------------------:|
|      Wei       | 0.000000000000000001 |               1               |
|    Kilowei     |  0.000000000000001   |             1,000             |
|    Megawei     |    0.000000000001    |           1,000,000           |
|    Gigawei     |     0.000000001      |         1,000,000,000         |
| Micromoonriver |       0.000001       |       1,000,000,000,000       |
| Millimoonriver |        0.001         |     1,000,000,000,000,000     |
|   Moonriver    |          1           |   1,000,000,000,000,000,000   |
| Kilomoonriver  |        1,000         | 1,000,000,000,000,000,000,000 |

## Proof of Stake {: #proof-of-stake } 

Au cours des 5 phases du lancement de Moonriver, le réseau sera progressivement mis à jour pour devenir un réseau Proof of Stake entièrement décentralisé. Pour savoir ce qui se passera au cours de chaque phase, consultez la page [Network Launch Status](https://moonbeam.network/networks/moonriver/launch/) page.

Dans la phase 1, il y a eu une première élection de collateurs pour remplir l'ensemble des collateurs actifs avec des parties extérieures à l'équipe Moonbeam. Le nombre de collateurs dans l'ensemble actif sera soumis à la gouvernance. L'ensemble actif sera composé des principaux collateurs par enjeu, y compris les nominations.

## Limitations {: #limitations } 

Certains [precompiles](https://docs.klaytn.com/smart-contract/precompiled-contracts) ne sont pas encore inclus. Vous pouvez consulter la liste des précompilations supportées [ici](/integrations/precompiles/). Cependant, toutes les fonctions intégrées sont disponibles.

