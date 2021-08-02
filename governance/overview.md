---
title: Aperçu
description: En tant que parachain Polkadot, Moonbeam utilisera un système de gouvernance en chaîne, permettant un vote pondéré en fonction des enjeux lors des référendums publics.
---

# La Gouvernance dans Moonbeam

![Governance Moonbeam Banner](/images/governance/governance-overview-banner.png)

## Introduction {: #introduction } 

Moonbeam est un réseau décentralisé qui sera régi par une communauté de détenteurs de jetons, comprenant les développeurs principaux, les développeurs d'applications, les collators, les utilisateurs et d'autres contributeurs. 

Nous visons à faciliter l'engagement des détenteurs de jetons de chacune de ces catégories dans le cadre du lancement du réseau.

## Définitions générales {: #general-definitions } 

Un grand pouvoir implique de grandes responsabilités. Certains paramètres importants à comprendre avant de s'engager dans la gouvernance de Moonbeam comprennent:

 - **Propositions** — actions ou éléments proposés par les détenteurs de jetons. Ces propositions doivent être appuyées par d'autres utilisateurs afin de passer au référendum
 - **Referendum** — lorsque la proposition la plus appuyée est votée par la communauté. Il peut y avoir un maximum de cinq référendums actifs à la fois, à moins qu'il y ai un référendum d'urgence en cours
 - **Période de lancement** — à quelle fréquence de nouveaux référendums publics sont lancés
 - **Période de vote** — les détenteurs de jetons doivent voter pour un référendum (durée d'un référendum)
 - **Période de vote accéléré** — durée du vote pour les propositions d'urgence qui abordent des questions critiques
- **Vote** — Les référendums sont votés par les détenteurs de jetons en fonction des enjeux et des convictions de chacun. Les convictions font référence au temps pendant lequel les détenteurs de jetons souhaitent verrouiller leurs jetons lors du vote: plus ils sont verrouillés dans le temps, plus leur vote a du poids. Les référendums qui réussissent sont sujets à une promulgation retardée afin que les personnes qui ne sont pas d'accord avec la direction de la décision aient le temps de quitter le réseau
 - **Période de promulgation** — temps entre l'approbation et la promulgation d'une proposition (faire la loi). C'est aussi le délai minimum nécessaire pour bloquer les fonds pour proposer une action
 - **Période de verrouillage** — durée (après la promulgation de la proposition) pendant laquelle les jetons des électeurs gagnants sont verrouillés. Les utilisateurs peuvent toujours utiliser ces jetons pour le jalonnement ou le vote
 - **Periode calme** - La durée d'un veto du comité technique avant que la proposition puisse être soumise à nouveau 
 - **Delegation** — acte de transfert de votre droit de vote sur un autre compte jusqu'à une certaine conviction

## Principes {: #principles } 

Les principes directeurs "souples" pour l'engagement avec le processus de gouvernance de Moonbeam comprennent:

 - Être inclusif envers les détenteurs de jetons qui souhaitent s'engager avec Moonbeam et qui sont affectés par les décisions de gouvernance.
 - Favoriser l'engagement des détenteurs de jetons, même avec des opinions contraires aux nôtres, par rapport à un manque d'engagement.
 - Un engagement d'ouverture et de transparence dans le processus décisionnel.
 - Travailler pour garder le plus grand bien du réseau avant tout gain personnel.  
 - Agir à tout moment comme un agent moral qui considère les conséquences de l'action (ou de l'inaction) d'un point de vue moral.
 - Être patient et généreux dans nos interactions avec d'autres détenteurs de jetons, mais ne pas tolérer un langage, des actions et des comportements abusifs ou destructeurs.

Ces points ont été fortement inspirés par les écrits de Vlad Zamfir sur la gouvernance. Reportez-vous à ses articles,  [en particulier celui-ci](https://medium.com/@Vlad_Zamfir/how-to-participate-in-blockchain-governance-in-good-faith-and-with-good-manners-bd4e16846434).

## Mécanique de gouvernance en chaîne {: #on-chain-governance-mechanics } 

Le processus de gouvernance "dur" pour Moonbeam sera piloté par un processus en chaîne et tirera parti de la démocratie, du Conseil et de la Trésorerie [Substrate frame pallets](/resources/glossary/#substrate-frame-pallets), de la même manière que Kusama et la chaîne de relais Polkadot sont gouvernées. L'intention générale de ces modules est de permettre à la majorité des jetons sur le réseau de déterminer les résultats des décisions clés autour du réseau. Ces points de décision prennent la forme d'un vote pondéré par les enjeux des référendums proposés.

Certaines des principales composantes de ce modèle de gouvernance comprennent:

 - **Referendum** — une proposition de modification du système Moonbeam comprenant des valeurs pour les paramètres clés, des mises à niveau de code ou des modifications du système de gouvernance lui-même
 - **Vote** — Les référendums sont votés par les détenteurs de jetons sur une base pondérée par les enjeux. Les référendums qui réussissent sont sujets à une promulgation retardée, de sorte que les personnes qui ne sont pas d'accord avec la direction de la décision aient le temps de quitter le réseau
 - **Conseil** — un groupe d'individus élus qui ont des droits de vote spéciaux au sein du système. Les membres du Conseil sont censés proposer des référendums pour le vote et ont la possibilité d'opposer leur veto aux référendums publics. Il y a des élections tournantes pour les membres du conseil où les titulaires de GLMR voteront sur les nouveaux membres ou existants du conseil. Le Conseil est également chargé d'élire le comité technique
 - **Comité technique** — un groupe d'individus élus par le Conseil qui ont des droits de vote spéciaux. De même que pour Polkadot et Kusama, le Comité technique a la capacité (avec le Conseil) d'accélérer le vote et la mise en œuvre des référendums d'urgence dans des circonstances urgentes. Un référendum accéléré peut être créé parallèlement aux référendums actifs existants. C'est-à-dire qu'un référendum d'urgence ne remplace pas les référendums actuellement en cours
 - **Trésorerie** — Une collection de fonds qui peut être dépensée en soumettant une proposition avec un dépôt. Les propositions de dépenses doivent être approuvées par le conseil. Les propositions rejetées entraîneront la perte du dépôt par le proposant

Voir [cet aperçu sur le site Web de Polkadot](https://polkadot.network/a-walkthrough-of-polkadots-governance/) et [cet article wiki](https://wiki.polkadot.network/docs/en/learn-governance) pour plus de détails sur la façon dont ces palettes Substrate implémentent la gouvernance en chaîne.

## Droits de vote du Conseil et du Comité technique {: #voting-rights-of-the-council-and-the-technical-committee } 

Cette section couvre quelques informations de base sur le vote et décrit les paramètres de vote du protocole tels qu'ils sont définis actuellement. Il y a une limite au temps en blocs dont disposent le comité technique et le conseil pour voter sur les motions. Les motions peuvent se terminer en moins de blocs s'il y a déjà suffisamment de votes soumis pour déterminer le résultat. Un maximum de {{ networks.moonbase.democracy.max_proposals}} propositions peuvent être ouvertes chacune en comité technique et en conseil.

Les paramètres de vote sont actuellement définis comme suit :

|             Variable             |     |                         Valeur                         |
| :------------------------------: | :-: | :---------------------------------------------------: |
|     Période de vote     |     |     {{ networks.moonbase.democracy.vote_period.blocks}} blocks ({{ networks.moonbase.democracy.vote_period.days}} days)     |
|        Période de vote accéléré        |     |     {{ networks.moonbase.democracy.fast_vote_period.blocks}} blocks ({{ networks.moonbase.democracy.fast_vote_period.days}} day)     | | 
|          Periode de promulgation           |     |     {{ networks.moonbase.democracy.enact_period.blocks}} blocks ({{ networks.moonbase.democracy.enact_period.days}} day)  |
| Periode calme |     |     {{ networks.moonbase.democracy.cool_period.blocks}} blocks ({{ networks.moonbase.democracy.cool_period.days}} days)  |
|              Dépôt minimum               |     | {{ networks.moonbase.democracy.min_deposit }} GLMR |

**Droits de vote à annuler:**

 * Le comité technique ne peut annuler une proposition avant qu'elle n'ait été adoptée qu'à l'unanimité
 * Un seul membre du comité technique peut opposer son veto à une proposition de conseil entrante, cependant, il ne peut y opposer son veto qu'une seule fois, et cela ne dure que le temps de la periode calme ({{ networks.moonbase.democracy.cool_period.days}} days)

## Essayez-le sur Moonbase Alpha  {: #try-it-on-moonbase-alpha } 

Actuellement, dans notre TestNet Moonbase Alpha , les détenteurs de jetons peuvent soumettre des propositions et voter sur des référendums. Pour ce faire, consultez les guides suivants :

 - [Soumettre une proposition](/governance/proposals/)
 - [Voter sur une proposition](/governance/voting/)

La composante Trésorerie n'a pas encore été mise en œuvre.
