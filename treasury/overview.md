---
title: Trésorerie
description: En tant que parachain Polkadot, Moonbeam utilisera une trésorerie en chaîne contrôlée par les membres du conseil, qui permet aux parties prenantes de soumettre des propositions pour faire avancer le réseau.
---

# La Trésorerie dans Moonbeam

![Treasury Moonbeam Banner](/images/learn/features/treasury/treasury-overview-banner.png)

## Introduction {: #introduction } 

Une trésorerie est une collection de fonds gérée en chaîne. Moonbeam disposera d'une trésorerie communautaire pour soutenir les initiatives du réseau afin de faire avancer le réseau. Cette trésorerie sera alimentée par un pourcentage des frais de transaction du réseau et sera gérée par le Conseil.

Chaque réseau basé sur Moonbeam aura sa propre trésorerie. En d'autres termes, Moonbase Alpha TestNet, Moonshadow sur Westend, Moonriver sur Kusama et Moonbeam sur Polkadot auront chacun leur propre trésorerie respective. 

## Définitions générales {: #general-definitions } 

Quelques termes importants à comprendre en ce qui concerne les trésoreries:

- **Conseil** — un groupe d'individus élus qui contrôlent la manière dont les fonds de la trésorerie seront dépensés
- **Proposition** — un plan ou une suggestion pour faire avancer le réseau qui est mis en avant par les parties prenantes pour être approuvé par le conseil
- **Caution de proposition** — un acompte égal à un pourcentage du montant total des dépenses de la proposition
- **Caution de proposition minimum** — montant minimum pour un cautionnement de proposition. Ce montant doit être payé comme caution s'il est supérieur au pourcentage de dépôt
- **Période de dépenses** — le nombre de jours, par blocs, pendant lesquels la trésorerie finance le plus de propositions possible sans dépasser le maximum
- **Nombre maximal de propositions approuvées** — le nombre maximum de propositions pouvant attendre dans la file d'attente des dépenses

Currently, the Treasury values are as follows:

=== "Moonbase Alpha"
    |             Variable             |     |                                                             Valeur                                                      |
    | :------------------------------: | :-: | :--------------------------------------------------------------------------------------------------------------------: |
    |           Caution de proposition          |     |                            {{ networks.moonbase.treasury.proposal_bond }}% of the proposed spend                       |
    |       Caution de proposition minimum      |     |                                  {{ networks.moonbase.treasury.proposal_bond_min }} DEV                              |
    |           Période de dépenses           |     |  {{ networks.moonbase.treasury.spend_period_blocks }} blocks ({{ networks.moonbase.treasury.spend_period_days}} days)  |
    |     Nombre maximal de propositions approuvées   |     |                                  {{ networks.moonbase.treasury.max_approved_proposals }}                               |
    |     % des frais de transaction alloués   |     |                                  {{ networks.moonbase.treasury.tx_fees_allocated }}                               |

=== "Moonriver"
    |             Variable             |     |                                                             Valeur                                                      |
    | :------------------------------: | :-: | :--------------------------------------------------------------------------------------------------------------------: |
    |           Caution de proposition          |     |                            {{ networks.moonriver.treasury.proposal_bond }}% of the proposed spend                       |
    |       Caution de proposition minimum      |     |                                  {{ networks.moonriver.treasury.proposal_bond_min }} MOVR                              |
    |           Période de dépenses           |     |  {{ networks.moonriver.treasury.spend_period_blocks }} blocks ({{ networks.moonriver.treasury.spend_period_days}} days)  |
    |     Nombre maximal de propositions approuvées   |     |                                  {{ networks.moonriver.treasury.max_approved_proposals }}                               |
     |     % des frais de transaction alloués   |     |                                  {{ networks.moonriver.treasury.tx_fees_allocated }}                               |

## Trésorerie de la Communauté {: #community-treasury } 

Pour financer la Trésorerie, un pourcentage des frais de transaction de chaque bloc lui sera alloué. Le pourcentage restant des frais est brûlé (consultez le tableau ci-dessus). La Trésorerie permet aux parties prenantes de soumettre des propositions de dépenses pour examen et voté par le Conseil. Ces propositions de dépenses devraient inclure des initiatives pour faire avancer le réseau ou stimuler l'engagement du réseau. Certaines initiatives de réseau pourraient inclure des intégrations ou des collaborations de financement, des événements communautaires, la sensibilisation du réseau, etc. 

Pour dissuader le spam, les propositions doivent être soumises avec un dépôt, également connu sous le nom de caution de proposition. La caution de proposition doit être supérieure au montant minimum, connu sous le nom de caution de proposition minimum, qui peut être modifié par une proposition de gouvernance. Ainsi, tout détenteur de token disposant de suffisamment de tokens pour couvrir le dépôt peut soumettre une proposition. Si le proposant n'a pas assez de fonds pour couvrir le dépôt, l'extrinsèque échouera en raison de fonds insuffisants, mais les frais de transaction seront toujours déduits. 

Une fois qu'une proposition a été soumise, elle est soumise à la gouvernance, et le conseil vote sur elle. Si la proposition est rejetée, le dépôt sera perdu et transféré dans le pot de trésorerie. Si elle est approuvée par le conseil, la proposition entre dans une file d'attente pour être placée dans une période de dépenses. Si la file d'attente des dépenses contient le nombre maximal de propositions approuvées, la soumission de la proposition échouera de la même manière que si le solde du proposant était trop bas.

Une fois que la proposition est dans une période de dépenses, les fonds seront distribués au bénéficiaire et le dépôt original sera retourné au proposant. Si la Trésorerie manque de fonds, les propositions approuvées restantes resteront en stock jusqu'à la prochaine période de dépenses, lorsque la Trésorerie disposera à nouveau de suffisamment de fonds.

Le chemin heureux pour une proposition de trésorerie est illustré dans le diagramme suivant:

![Treasury Proposal Happy Path Diagram](/images/learn/features/treasury/treasury-proposal-roadmap.png)
