---
title: Aperçu
description: Moonbeam fournit des fonctionnalités de staking où les détenteurs de jetons nomment des collators avec leurs jetons et gagnent des récompenses
---

# Staking dans Moonbeam

![Staking Moonbeam Banner](/images/staking/staking-overview-banner.png)

## Introduction {: #introduction } 

Moonbeam utilise un mécanisme de production de blocs basé sur [le modèle Proof-of-Stake de Polkadot ](https://wiki.polkadot.network/docs/en/learn-consensus) où il y a des collators et des validateurs. [Les collators](https://wiki.polkadot.network/docs/en/learn-collator) maintiennent les parachains (dans ce cas, Moonbeam) en collectant les transactions des utilisateurs et en produisant des preuves de transition d'état pour les [validateurs](https://wiki.polkadot.network/docs/en/learn-validator)de la chaine de relais.

L'ensemble des collators (nœuds qui produisent des blocs) est sélectionné en fonction de leur participation dans le réseau. Et c'est là que le staking entre en jeu.

Les collators (et les détenteurs de jetons s'ils les nomment) ont un intérêt dans le réseau dans lequel ils sont pénalisés s'ils se comportent mal. Par conséquent, plus l'enjeu est élevé, plus la sécurité du réseau est élevée. Plus l'enjeu est élevé, plus il est probable que le collator soit sélectionné pour produire un bloc et gagner des récompenses, qu'il partage avec ses nominateurs. De cette manière, les membres du réseau sont incités à staker des jetons pour améliorer la sécurité globale.

## Définitions générales {: #general-definitions } 

--8<-- 'text/staking/staking-definitions.md'

=== "Moonbase Alpha"

  |             Variable                          |     |                                                  Valeur                                                 |
  | :------------------------------:              | :-: | :-----------------------------------------------------------------------------------------------------: |
  |   Mise de nomination minimale                 |     |                          {{ networks.moonbase.staking.min_nom_stake }} tokens                           |
  |   Nomination minimale                         |     |                          {{ networks.moonbase.staking.min_nom_amount}} tokens                           |
  | Nominateurs maximum par collator            |     |                             {{ networks.moonbase.staking.max_nom_per_col }}                             |
  | Nombre maximal de collators par nominateur  |     |                             {{ networks.moonbase.staking.max_col_per_nom }}                             |
  |              Tour                             |     | {{ networks.moonbase.staking.round_blocks }} blocks ({{ networks.moonbase.staking.round_hours }} hours) |
  | Durée de l'obligation                         |     |                            {{ networks.moonbase.staking.bond_lock }} rounds                             |

=== "Moonriver"

    |             Variable             |  |                                                   Value                                                   |
    |:--------------------------------:|::|:---------------------------------------------------------------------------------------------------------:|
    |     Minimum nomination stake     |  |                           {{ networks.moonriver.staking.min_nom_stake }} MOVR                             |
    |        Minimum nomination        |  |                           {{ networks.moonriver.staking.min_nom_amount}} MOVR                             |
    | Maximum nominators per collators |  |                             {{ networks.moonriver.staking.max_nom_per_col }}                              |
    | Maximum collators per nominator  |  |                             {{ networks.moonriver.staking.max_col_per_nom }}                              |
    |              Round               |  | {{ networks.moonriver.staking.round_blocks }} blocks ({{ networks.moonriver.staking.round_hours }} hours) |
    |          Bond duration           |  |                             {{ networks.moonriver.staking.bond_lock }} rounds 
## Distribution des récompenses {: #reward-distribution } 

A la fin de chaque tour ({{ networks.moonbase.staking.round_blocks }} blocks), les collators sont récompensés pour les 2 tours précédents. {{ networks.moonbase.staking.bond_lock }} .

Lorsque les collators se joignent à l'ensemble des collators, ils établissent une commission pour facturer leurs nominateurs pour le service qu'ils fournissent. Par conséquent, la distribution des récompenses se déroule comme suit:

 - La commission est prélevée sur la récompense à distribuer
 - Le collator reçoit les récompenses correspondant à sa participation dans le réseau, plus la commission
 - Le reste des récompenses est réparti entre les nominateurs par stake

Mathématiquement parlant, pour les collators, la récompense ressemblerait à ceci:

![Staking Collator Reward](/images/staking/staking-overview-1.png)

Où la mise correspond au montant de jetons cautionnés par le collator par rapport à la mise totale de ce collator (nominations comptables).

Pour chaque nominateur, la récompense ressemblerait à ceci :

![Staking Nominator Reward](/images/staking/staking-overview-2.png)

Où la mise correspond au montant de jetons cautionnés par chaque nominateur par rapport à la mise totale de ce collator.

## Try it out {: #try-it-out } 

Dans Moonbase Alpha TestNet, les détenteurs de jetons peuvent miser et gagner des récompenses (pour se familiariser avec le système car le jeton n'a aucune valeur réelle).

Pour ce faire, vous pouvez consulter [ce guide](/staking/stake/).

--8<-- 'text/moonriver-launch/staking-phase-4.md'
