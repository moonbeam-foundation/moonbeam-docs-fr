---
title: Proposer une action
description: Comment envoyer une proposition à voter sur Moonbeam via des fonctionnalités de gouvernance
---

# Propositions

![Governance Moonbeam Banner](/images/governance/governance-proposal-banner.png)

## Introduction

Comme mentionné dans la [page de présentation de la gouvernance](/governance/overview/#definitions), une proposition est une soumission à la chaîne dans laquelle un détenteur de jeton suggère qu'une action soit mise en œuvre par le système.

Les propositions sont l'un des éléments centraux du système de gouvernance car elles sont le principal outil permettant aux parties prenantes de proposer des actions / changements, sur lesquelles les autres parties prenantes votent ensuite.

Dans Moonbeam, les utilisateurs pourront créer, soutenir et voter sur des propositions en utilisant leur adresse H160 et leur clé privée, c'est-à-dire leur compte Ethereum habituel!

Avec la sortie de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), les utilisateurs du réseau peuvent désormais soumettre des propositions de référendums publics dans le TestNet. Ce guide décrit le processus de création d'une proposition. Les étapes iront de sa création jusqu'à ce qu'il atteigne les référendums publics. Vous pouvez trouver un guide sur la façon de voter sur une proposition [ici](/governance/voting/).

Plus d'informations peuvent être trouvées dans les pages Wiki de Polkadot liées à la [Gouvernance](https://wiki.polkadot.network/docs/en/learn-governance#council) et a la [participation à la démocratie](https://wiki.polkadot.network/docs/en/maintain-guides-democracy).

!!! remarque
    Ce guide a été réalisé avec une version personnalisée de Moonbeam avec de courtes périodes de lancement/mise en œuvre à des fins de démonstration uniquement.

## Definitions

Les paramètres clés de ce guide sont les suivants:

 - **Proposition** — action ou éléments proposés par les utilisateurs du réseau
 - **Soutient** — d'autres parties prenantes peuvent appuyer (approuver) une proposition si elles sont d'accord avec elle et veulent l'aider à atteindre des référendums publics. Cela nécessite de faire correspondre le dépôt du proposant d'origine
 - **Hachage de préimage** — hachage de la proposition à adopter. La première étape pour faire une proposition est de soumettre une pré-image. Le hachage n'est que son identifiant. Le proposant de la pré-image peut être différent de l'utilisateur qui propose cette pré-image en tant que proposition formelle
 - **Dépôt minimum de préimage** — montant minimum de jetons que le proposant doit payer lors de la soumission d'une préimage
 - **Dépôt minimum de proposition** — montant minimum de jetons que le proposant doit cautionner lors de la soumission d'une proposition. Les jetons peuvent être verrouillés pendant une durée indéterminée car on ne sait pas quand une proposition peut devenir un référendum (le cas échéant). Cela est vrai pour les jetons liés à la fois par le proposant et les utilisateurs qui appuient la proposition
 - **Période de lancement** — à quelle fréquence de nouveaux référendums publics sont lancés
 - **Periode calme** — durée (en blocs) pendant laquelle une proposition ne peut pas être soumise à nouveau après avoir fait l'objet d'un veto

Actuellement, pour Moonbase Alpha :

|         Variable            |     |                                                          Valeur                                                         |
| :----------------------:    | :-: | :---------------------------------------------------------------------------------------------------------------------: |
|    Période de lancement     |     | {{ networks.moonbase.democracy.launch_period.blocks}} blocks ({{ networks.moonbase.democracy.launch_period.days}} days) |
|     Periode calme           |     |   {{ networks.moonbase.democracy.cool_period.blocks}} blocks ({{ networks.moonbase.democracy.cool_period.days}} days)   |
| Dépôt minimum de préimage   |     |                                 {{ networks.moonbase.democracy.min_preim_deposit}} DEV                                  |
|Dépôt minimum de proposition |     |                                    {{ networks.moonbase.democracy.min_deposit}} DEV                                     |

## Feuille de route d'une proposition

--8<-- 'text/governance/roadmap.md'

## Proposer une action

Cette section passe en revue le processus de création d'une proposition, depuis une pré-image jusqu'à ce qu'elle atteigne les référendums publics. Au lieu de faire un exemple générique, ce guide va en fait créer une vraie proposition qui servira de base à ce guide et à d'autres

Pour faire une proposition dans le réseau, vous devez utiliser l'interface PolkadotJS Apps. Pour ce faire, vous devez d'abord importer un compte de style Ethereum (adresse H160), ce que vous pouvez faire en suivant [ce tutoriel](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Pour cet exemple, trois comptes ont été importés et nommés avec des noms super originaux : Alice, Bob et Charley.

![Comptes dans PolkadotJS](/images/governance/governance-proposal-1.png)

La proposition fixera le solde de Bob à `1500` via la gouvernance!

### Soumettre une pré-image de la proposition

La première étape consiste à soumettre une pré-image de la proposition. En effet, le coût de stockage des grandes pré-images peut être assez élevé, car la pré-image contient toutes les informations concernant la proposition elle-même. Avec cette configuration, un compte avec plus de fonds peut soumettre une pré-image et un autre compte peut soumettre la proposition.

Tout ce qui touche à la gouvernance vit sous l'onglet "Démocracy" . Une fois là-bas, cliquez sur le bouton "soummetre la preimage" .

![Soumettre la préimage](/images/governance/governance-proposal-2.png)

Ici, vous devez fournir les informations suivantes :

 1. Sélectionnez le compte à partir duquel vous souhaitez soumettre la préimage
 2. Choisissez la palette avec laquelle vous souhaitez interagir et la fonction (ou action) dispatchable à proposer. L'action que vous choisissez déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas, c'est la palette `democracy` et la fonction  `setBalance`
 3. Définissez l'adresse dont vous souhaitez modifier le solde
 4. Définissez le nouveau solde que cette adresse contiendra. Pour en savoir plus sur les types de soldes, vous pouvez visiter [ce site](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 5. Copiez le hachage de la préimage. Cela représente la proposition. Vous utiliserez ce hachage lors de la soumission de la proposition réelle
 6. Cliquez sur le bouton "Soummetre la preimage" et signez la transaction.

![Remplissez les informations de pré-image](/images/governance/governance-proposal-3.png)

!!! remarque
    Assurez-vous de copier le hachage de la préimage, car il est nécessaire pour soumettre la proposition.

Notez que le coût de stockage de la pré-image est affiché dans le coin inférieur gauche de cette fenêtre. Une fois la transaction soumise, vous verrez des confirmations dans le coin supérieur droit de l'interface de PolkadotJS Apps, mais rien n'aura changé dans l'écran principal de democracy. Cependant, ne vous inquiétez pas. Si la transaction est confirmée, la préimage a été soumise.

### Soumettre une proposition

Une fois que vous avez validé la préimage (consultez la section précédente), la prochaine étape majeure de la feuille de route est de soumettre la proposition qui s'y rapporte. Pour ce faire, dans l'écran principal démocracy, cliquez sur "soumettre une proposition."

![soumettre une proposition](/images/governance/governance-proposal-4.png)

Ici, vous devez fournir les informations suivantes :

 1. Sélectionnez le compte à partir duquel vous souhaitez soumettre la proposition (dans ce cas, Alice)
 2. Saisissez le hachage de la pré-image lié à la proposition. Dans cet exemple, il s'agit du hachage de la préimage `setBalance` de la section précédente
 3. Réglez le solde verrouillé. Il s'agit du nombre de jetons que le proposant associe à sa proposition. N'oubliez pas que la proposition avec le plus grand nombre de jetons verrouillés va au référendum. Le dépôt minimum est affiché juste en dessous de cet onglet de saisie
 4. Cliquez sur le bouton "Soumettre la proposition" et signez la transaction

![Remplissez les informations de la proposition](/images/governance/governance-proposal-5.png)

!!! remarque
    Les jetons peuvent être verrouillés pendant une durée indéterminée car on ne sait pas quand une proposition peut devenir un référendum (le cas échéant).

Une fois la transaction soumise, vous verrez des confirmations dans le coin supérieur droit de l'interface Polkadot JS Apps. Vous devriez également voir la proposition répertoriée dans la section "Propositions" , affichant le proposant et les montants de jetons verrouillés, et elle est maintenant prête à être secondée!

![Proposition listée](/images/governance/governance-proposal-6.png)

### Soutien à une proposition

Soutenir une proposition signifie que vous êtes d'accord avec elle et que vous souhaitez la sauvegarder avec vos jetons pour l'aider à atteindre les référendums publics. Le nombre de jetons à verrouiller est égal au dépôt initial du proposant - ni plus, ni moins.

!!! remarque
    Un seul compte peut soutenir une proposition plusieurs fois. C'est par conception, car un compte pourrait simplement envoyer des jetons à différentes adresses et les utiliser pour seconder la proposition. Ce qui compte, c'est le nombre de jetons qui soutiennent une proposition, et non le nombre de bons qu'elle a reçus.

Cette section décrit les étapes à suivre pour soutenir la proposition faite dans la section précédente. Pour ce faire, cliquez sur le bouton "Second" disponible pour chaque proposition qui apparaît dans la liste des propositions.

![Proposal listed to Second](/images/governance/governance-proposal-7.png)

Ici, vous devez fournir les informations suivantes:

 1. Sélectionnez le compte avec lequel vous souhaitez soutenir la proposition (dans ce cas, Charley)
 2. Vérifiez le nombre de jetons requis pour soutenir la proposition
 3. Cliquez sur le bouton "Second" et signez la transaction

![Fill in Second Information](/images/governance/governance-proposal-8.png)

!!! remarque
    Les jetons peuvent être verrouillés pendant une durée indéterminée car on ne sait pas quand une proposition peut devenir un référendum (le cas échéant)

Une fois la transaction soumise, vous verrez des confirmations dans le coin supérieur droit de l'interface Polkadot JS Apps. Vous devriez également voir la proposition répertoriée dans la section "Propositions" , affichant le proposant et les montants de jetons verrouillés et répertoriant les utilisateurs qui ont soutenu cette proposition!

![Proposal Seconded](/images/governance/governance-proposal-9.png)

