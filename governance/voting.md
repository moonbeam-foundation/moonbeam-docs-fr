---
title: Voter sur une proposition
description: Comment voter sur une proposition pour qu'elle soit adoptée ou rejetée sur Moonbeam via des fonctionnalités de gouvernance
---

# Propositions

![Governance Moonbeam Banner](/images/governance/governance-voting-banner.png)

## Introduction

Une fois qu'une proposition atteint les référendums publics, les détenteurs de jetons peuvent voter dessus en utilisant leurs propres jetons. Deux facteurs définissent le poids d'un vote : le nombre de tokens verrouillés et la durée de verrouillage (appelée condamnation). Il s'agit de s'assurer qu'il y a une adhésion économique au résultat pour empêcher la vente de votes. Par conséquent, plus vous êtes prêt à verrouiller vos jetons longtemps, plus votre vote sera pondéré. Vous avez également la possibilité de ne pas verrouiller du tout les jetons, mais le poids des votes est considérablement réduit.

Les référendums sont des schémas de vote simples, inclusifs et basés sur les enjeux. Chaque référendum est associé à une proposition qui suggère une action à prendre. Ils ont une durée fixe, après laquelle les votes sont comptés, et l'action est décrétée si le vote est approuvé.

Dans Moonbeam, les utilisateurs pourront créer, seconder et voter sur des propositions en utilisant leur adresse H160 et leur clé privée, c'est-à-dire leur compte Ethereum habituel!

Avec la sortie de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), les utilisateurs du réseau peuvent désormais soumettre des propositions de référendums publics et voter. Ce guide explique comment voter sur une proposition qui a fait l'objet d'un référendum public. Vous pouvez trouver un guide sur la façon de soumettre une proposition [ici](/governance/proposals/).

Plus d'informations concernant la [Gouvernance](https://wiki.polkadot.network/docs/en/learn-governance) et [Participer à la démocratie](https://wiki.polkadot.network/docs/en/maintain-guides-democracy) peut être trouvé dans les pages Wiki de Polkadot.

!!! note
    Ce guide a été réalisé avec une version personnalisée de Moonbeam avec de courtes périodes de lancement/adoption à des fins de démonstration uniquement.

## Definitions

Certains des paramètres clés de ce guide sont les suivants :

 - **Période de vote** — the time token holders have to vote for a referendum (duration of a referendum)
 - **Vote** — un outil utilisé par les détenteurs de jetons pour approuver ou rejeter une proposition. Le poids d'un vote est défini par deux facteurs : le nombre de jetons verrouillés et la durée du verrouillage (appelée condamnation)
 - **Taux de participation** — le nombre total de jetons de vote
 - **Électorat** — le nombre total de jetons émis dans le réseau
 - **Nombre maximum de votes** — le nombre maximum de votes par compte
 - **Période d'adoption** — le temps entre l'approbation d'une proposition et son adoption (faire une loi). C'est aussi la période minimale de blocage lors du vote
 - **Période de verrouillage** — le temps (après la promulgation de la proposition) que les jetons des électeurs gagnants sont verrouillés. Les utilisateurs peuvent toujours utiliser ces jetons pour le staking ou le vote
 - **Delegation** — le fait de transférer votre droit de vote sur un autre compte jusqu'à une certaine condamnation

Actuellement, pour Moonbase Alpha:

|        Variable         |     |                                                         Valeur                                                         |
| :---------------------: | :-: | :-------------------------------------------------------------------------------------------------------------------: |
|       Période de vote       |     |  {{ networks.moonbase.democracy.vote_period.blocks}} blocks ({{ networks.moonbase.democracy.vote_period.days}} days)  |
|      Période d'adoption       |     | {{ networks.moonbase.democracy.enact_period.blocks}} blocks ({{ networks.moonbase.democracy.enact_period.days}} days) |
| Nombre maximal de votes |     |                                      {{ networks.moonbase.democracy.max_votes}}                                       |

## Feuille de route d'une proposition

--8<-- 'text/governance/roadmap.md'

## Voter sur un référendum

Cette section passe en revue le processus de vote lors d'un référendum. Le guide suppose qu'il y en a déjà un, en l'occurrence celui créé dans [ce guide](/governance/proposals/).

Pour voter sur une proposition dans le réseau, vous devez utiliser l'interface PolkadotJS Apps. Pour ce faire, vous devez d'abord importer un compte de style Ethereum (adresse H160), ce que vous pouvez faire en suivant [ce guide](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Pour cet exemple, trois comptes ont été importés et nommés avec des noms super originaux : Alice, Bob et Charley.

![Accounts in PolkadotJS](/images/governance/governance-proposal-1.png)

La proposition soumise au vote fixera le solde de Bob à `1500` via gouvernance!

### Comment voter

Voter sur Moonbeam est assez simple. Tout ce qui touche à la gouvernance vit sous l'onglet "Democracy" , ou (dans l'image) vous pouvez noter qu'il y a un `1`, indiquant qu'il y a un point sur la démocratie en attente (soit des propositions, soit des référendums). Une fois là-bas, vous pouvez visualiser les détails du référendum que vous souhaitez voter en cliquant sur la flèche à côté de la description. Le numéro à côté de l'action et de sa description s'appelle l'indice référendaire (dans ce cas, c'est 0). Lorsque vous êtes prêt, cliquez sur le bouton "Vote".

![Vote Button](/images/governance/governance-vote-1.png)

Ici, vous devez fournir les informations suivantes:

 1. Sélectionnez le compte avec lequel vous souhaitez voter
 2. Entrez le nombre de jetons avec lesquels vous souhaitez voter. Ceux-ci seront verrouillés pour la durée spécifiée à l'étape suivante
 3. Fixer la condamnation du vote, qui détermine son poids (`vote_weight = tokens * conviction_multiplier`). Le multiplicateur de condamnation est lié au nombre de périodes d'adoptions pendant lesquelles les jetons seront verrouillés. Par conséquent, plus vous êtes prêt à verrouiller vos jetons longtemps, plus votre vote sera pondéré. Vous avez également la possibilité de ne pas verrouiller les jetons du tout, mais le poids du vote est considérablement réduit (les jetons sont toujours verrouillés pendant la durée du référendum)

   | Périodes de verrouillage |     | Multiplicateur de condamnation |
   | :----------: | :-: | :-------------------: |
   |      0       |     |          0.1          |
   |      1       |     |           1           |
   |      2       |     |           2           |
   |      4       |     |           3           |
   |      8       |     |           4           |
   |      16      |     |           5           |
   |      32      |     |           6           |

 4. Cliquer sur "Vote Aye" pour approuver la proposition ou sur "Vote Nay" pour désapprouver la proposition, puis signer la transaction

![Vote Submission](/images/governance/governance-vote-2.png)

!!! note
    Les périodes de blocage indiquées dans l'image précédente ne sont pas à prendre comme référence. Ce guide a été réalisé avec une version personnalisée de Moonbeam avec de courtes périodes de lancement/adoption à des fins de démonstration uniquement.

Dans ce cas, Alice a décidé de "Vote Aye" sur la proposition avec une condamnation de `6x`. D'autre part, Charley a décidé de "Vote Nay" sur la proposition mais a choisi de ne verrouiller aucun jeton (ses jetons ne sont verrouillés que pendant la durée du référendum), sa condamnation a donc été `0.1x`. Avec de telles distributions de voix, les résultats partiels peuvent être vus dans l'onglet principal "Democracy".

![Vote Information](/images/governance/governance-vote-3.png)

Il y a quelques points clés à retenir du vote:

 - Le vote pondéré d'Alice est de 10800 unités. C'est-à-dire que ses 1800 jetons verrouillés ont multiplié sa conviction par x6
 - Le vote pondéré de Charley est de 80 unités. C'est-à-dire que ses 800 jetons sans période de verrouillage (uniquement pendant le référendum) ont rendu son facteur de conviction x0,1
 - La période de vote restante et le temps avant l'adoption de la proposition (si elle est adoptée) sont affichés à l'écran
 - Le taux de participation global (en pourcentage) n'est que de 0,21 %. Ceci est calculé comme le nombre total de jetons de vote (2600) divisé par le nombre total de jetons dans le réseau (1,22M dans ce cas)
 - Même si le taux de participation est assez faible, la proposition est provisoirement approuvée en raison de l'approbation de la super-majorité. Plus d'informations peuvent être trouvées dans [cette section](/governance/voting/#positive-turnout-bias)
 - Il est important de noter l'index référendaire, car cela est nécessaire pour déverrouiller les jetons plus tard lorsque la période de verrouillage expire. Actuellement, il n'y a aucun moyen de récupérer l'index référendaire une fois qu'il a été adopté.

Après l'expiration de la période de vote, la proposition sera visible sous l'onglet "Dispatch" si approuvéé. Ici, vous pouvez également voir le temps restant jusqu'à ce que la proposition soit adoptée.

![Proposal Enactment](/images/governance/governance-vote-4.png)

Rappelez-vous que, pour cet exemple, la fonction `setBalance` a été utilisé pour régler le solde de Bob à 1500 jetons. Une fois la période d'adoption passée, vous pouvez revenir à l'onglet "Accounts" pour vérifier que la proposition et devenu loi.

![Proposal Result](/images/governance/governance-vote-5.png)

### Déleguer ses votes

Les détenteurs de tokens ont la possibilité de déléguer leur vote à un autre compte dont ils ont confiance en l'opinion. Le compte en cours de délégation n'a pas besoin d'effectuer d'action particulière. Lorsqu'ils votent, le poids du vote (c'est-à-dire le nombre de jetons multiplié par le multiplicateur de conviction choisi par le délégant) est ajouté à son vote.

Pour déléguer votre vote, accédez d'abord au menu "Extrinsics" en dessous de l'onglet "Developers".

![Extrinsics Menu](/images/governance/governance-vote-6.png)

Ici, vous devez fournir les informations suivantes:

 1. Sélectionnez le compte à partir duquel vous souhaitez déléguer votre vote
 2. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `democracy`
 3. Choisissez l'extrinsic à utiliser pour la transaction. Cela déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas c'est l'extrinsic `delegate`
 4. Sélectionnez le compte auquel vous souhaitez déléguer votre vote
 5. Fixer la condamnation du vote, qui détermine son poids (`vote_weight = tokens * conviction_multiplier`). Le multiplicateur de condamnation est lié au nombre de périodes d'adoptions pendant lesquelles les jetons seront verrouillés. Par conséquent, plus vous êtes prêt à verrouiller vos jetons longtemps, plus votre vote sera pondéré. Vous avez également la possibilité de ne pas verrouiller les jetons du tout, mais le poids du vote est considérablement réduit
 6. Définissez le nombre de jetons que vous souhaitez déléguer au compte fourni auparavant
 7. Cliquez sur le bouton "Submit Transaction" et signez la transaction

![Extrinsics Transaction for Delegation](/images/governance/governance-vote-7.png)

Dans cet exemple, Alice a délégué un poids total de 1000 (1000 jetons avec un facteur de conviction x1) à Charley.

!!! note
    Une autre façon de déléguer des votes est sous l'onglet "Accounts". Cliquez sur les trois points du compte à partir duquel vous souhaitez déléguer votre vote et renseignez les informations comme précédemment.

Une fois le compte auquel vous avez délégué votre vote vote, le poids total du vote délégué sera attribué à l'option que le compte a sélectionnée. Pour cet exemple, Charley a décidé de voter en faveur d'une proposition qui est en référendum public. Il a voté avec un poids total de 800 (800 jetons avec un facteur de conviction x1). Mais parce qu'Alice lui a délégué 1000 votes, les votes "Aye" totalisent 1800 unités.

![Total Votes with Delegation](/images/governance/governance-vote-8.png)

Pour supprimer la délégation, répétez le processus décrit précédemment, mais sélectionnez l'extrinsic `undelegate` à l'étape 3.

Il y a quelques points clés à retenir pour la délégation:

 - Si un détenteur de jetons supprimait la délégation de vote lors d'un référendum public où les votes délégués étaient utilisés, ceux-ci seraient supprimés du décompte
 - Un détenteur de jetons qui a délégué des votes a toujours une adhésion économique. Cela signifie que si l'option sélectionnée par le délégant était gagnante, les jetons délégués sont verrouillés pour le nombre de périodes de verrouillage
 - Les jetons délégués pour le vote ne font plus partie du solde disponible du détenteur du jeton. Pour en savoir plus sur les types de soldes, vous pouvez visiter [ce site](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 - Un détenteur de jetons qui a délégué des jetons ne peut pas participer à un référendum public. Premièrement, le détenteur du jeton doit annuler sa délégation de vote
 - Un détenteur de jetons qui a délégué des jetons doit déverrouiller manuellement ses jetons verrouillés une fois la période de verrouillage expirée. Pour cela, il faut connaître l'indice référendaire

### Déverrouillage des jetons verrouillés

Lorsque les détenteurs de jetons votent, les jetons utilisés sont verrouillés et ne peuvent être transférés. Vous pouvez vérifier si vous avez des jetons verrouillés dans l'onglet "Accounts" , en développant les détails du compte de l'adresse à interroger. Là, vous verrez différents types de soldes (vous pouvez lire plus d'informations sur chaque type [ici](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)). Si vous passez la souris sur l'icône à côté de "democracy," un panneau d'information s'affichera pour vous indiquer l'état actuel de votre verouillage. Les différents états de verrouillage incluent:

 - Verrouillé en raison d'un référendum en cours, ce qui signifie que vous avez utilisé vos jetons et que vous devez attendre la fin du référendum, même si vous avez voté avec un facteur de condamnation sans verrouillage
 - Verrouillé en raison du multiplicateur de condamnation sélectionné, affichant le nombre de blocages et le temps restant
 - Le verrouillage a expiré, ce qui signifie que vous pouvez maintenant récupérer vos jetons

![Account Lock Status](/images/governance/governance-vote-9.png)

Une fois le verrouillage expiré, vous pouvez demander à nouveau vos jetons. Pour ce faire, accédez au menu "Extrinsics" en dessous de l'onglet "Developers" .

![Extrinsics Menu](/images/governance/governance-vote-10.png)

Ici, deux extrinsics différents doivent être envoyés. Tout d'abord, vous devez fournir les informations suivantes:

 1. Sélectionnez le compte à partir duquel vous souhaitez récupérer vos tokens
 2. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `democracy`
 3. Choisissez la méthode extrinsic à utiliser pour la transaction. Cela déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas, c'est l'extrinsic `removeVote` . Cette étape est nécessaire pour déverrouiller les jetons. Cet extrinsic peut également être utilisé pour retirer votre vote d'un référendum
 4. Entrez l'index référendaire. Il s'agit du numéro qui figurait à gauche dans l'onglet "Democracy". Dans ce cas, c'est 0
 5. Cliquez sur le bouton "Submit Transaction" et signez la transaction

![Remove Vote Extrinsics](/images/governance/governance-vote-11.png)

Pour le prochain extrinsic, vous devez fournir les informations suivantes:

 1. Sélectionnez le compte à partir duquel vous souhaitez récupérer vos tokens
 2. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `democracy`
 3. Choisissez la méthode extrinsic à utiliser pour la transaction. Cela déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas, c'est l'extrinsic `unlock`
 4. Entrez le compte cible qui recevra les jetons débloqués. Dans ce cas, les jetons seront rendus à Alice
 5. Cliquez sur le bouton "Submit Transaction" et signez la transaction

![Unlock Extrinsics](/images/governance/governance-vote-12.png)

Une fois la transaction terminée, les jetons verrouillés doivent être déverrouillés. Pour vérifier, vous pouvez revenir l'onglet "Accounts" , pour cet exemple, Alice a son solde complet comme "transferable."

![Check Balance](/images/governance/governance-vote-13.png)

## Biais de participation positif

Les référendums publics utilisent une métrique de biais de participation positive, c'est-à-dire une formule d'approbation à la majorité qualifiée. L'équation est la suivante:

![Positive Turnout Bias](/images/governance/governance-vote-bias.png)

Ou:

 - **Approuver** — nombre de votes "Aye" (y compris le multiplicateur de condamnation)
 - **Contre** — nombre de votes "Nay" (y compris le multiplicateur de condamnation)
 - **Participation** — le nombre total de jetons de vote (sans inclure le multiplicateur de condamnation)
 - **Électorat** — le nombre total de jetons émis dans le réseau

In the previous example, these numbers were:

|  Variable  |     |         Valeur         |
| :--------: | :-: | :-------------------: |
|  Approuver   |     |   10800 (1800 x 6)    |
|  Contre   |     |    80 (800 x 0.1)     |
|  Participation   |     |   2600 (1800 + 800)   |
| Electorat |     |         1.22M         |
| **Resultat** |     | 1.5 < 9.8 (Aye wins!) |

En bref, une forte majorité de votes aye est requise pour approuver une proposition à faible taux de participation, mais à mesure que le taux de participation augmente, cela devient une majorité simple.

