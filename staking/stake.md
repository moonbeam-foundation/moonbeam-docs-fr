---
title: Comment staker
description: Un guide qui montre comment vous pouvez staker vos jetons dans Moonbeam en nommant des collators
---

# Comment staker vos jetons

![Staking Moonbeam Banner](/images/staking/staking-stake-banner.png)

## Introduction {: #introduction } 

Les Collators (producteurs de blocs) avec la participation la plus élevée dans le réseau rejoignent le pool actif de collators, à partir duquel ils sont sélectionnés pour offrir un bloc à la chaîne de relais.

Les détenteurs de jetons peuvent augmenter la mise des collators en utilisant leurs jetons, un processus appelé nomination (également appelé staking). Quand ils le font, ils se portent garant de ce collator spécifique, et leur nomination est un signe de confiance.

Lorsqu'un collator ne se comporte pas correctement, sa participation dans le réseau est réduite, affectant également les jetons nommés par les utilisateurs (fonctionnalité actuellement non disponible dans Moonbase Alpha). Si les collators agissent en conséquence, ils recevront des récompenses en bloc dans le cadre du modèle inflationniste. Ils peuvent les partager en tant que récompenses de staking avec leurs nominateurs.

Avec la sortie de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), les utilisateurs du réseau peuvent désormais miser leurs jetons pour désigner des collators. Ce guide décrit toutes les étapes pour y parvenir.

## Définitions générales {: #general-definitions } 

--8<-- 'text/staking/staking-definitions.md'

Actuellement, pour Moonbase Alpha:

|             Variable             |     |                         Valeur                         |
| :------------------------------: | :-: | :---------------------------------------------------: |
|     Enjeu minimum de nomination     |     |     {{ networks.moonbase.staking.min_nom_stake }}     |
|        Nomination minimale        |     |     {{ networks.moonbase.staking.min_nom_amount}}     | | Maximum nominators per collators |     |     {{ networks.moonbase.staking.max_nom_per_col }}   |
| Collators maximum par nominateur  |     |     {{ networks.moonbase.staking.max_col_per_nom }}   |
|              Tour               |     | {{ networks.moonbase.staking.round_blocks }} blocks ({{ networks.moonbase.staking.round_hours }} hours) |
|          Durée de l'obligation           |     |     {{ networks.moonbase.staking.bond_lock }} rounds  |

## Définitions extrinsèques {: #extrinsics-definitions } 

Il existe de nombreux extrinsèques liés à la palette de staking, ils ne sont donc pas tous couverts dans ce guide. Cependant, cette liste définit tous les extrinsèques associés au processus de nomination :

!!! remarque
    Les extrinsèques pourraient changer à l'avenir à mesure que la palette de staking est mise à jour.

 - **nominate** — deux entrées: adresse du collator à désigner et montant. Extrinsèque pour désigner un collator. Le montant doit être d'au moins {{ networks.moonbase.staking.min_nom_amount }} jetons
 - **leaveNominators** — pas d'entrées. Extrinsèque pour quitter l'ensemble des nominateurs. Par conséquent, toutes les nominations en cours seront révoquées
 - **nominatorBondLess** — deux entrées: l'adresse d'un collator désigné et le montant. Extrinsèque pour réduire la quantité de jetons stakés pour un collator déjà désigné. Le montant ne doit pas diminuer votre solde total staké en dessous de {{ networks.moonbase.staking.min_nom_stake }} jetons
 - **nominatorBondMore** — deux entrées: l'adresse d'un collator désigné et le montant. Extrinsèque pour augmenter la quantité de jetons jalonnés pour un collator déjà désigné
 - **revokeNomination** — une entrée: adresse d'un collator désigné. Extrinsèque pour supprimer une nomination existante

## Récupération de la liste des collators {: #retrieving-the-list-of-collators } 

Avant de commencer à staker des jetons, il est important de récupérer la liste des collators disponibles dans le réseau. Pour ce faire, accédez à "État de la chaîne" sous l'onglet "Developpeur" .

![Compte de staking](/images/staking/staking-stake-10.png)

Ici, fournissez alors les informations suivantes:

 1. Head to the "Developer" tab 
 2. Click on "Chain State"
 3. Choose the pallet to interact with. In this case, it is the `parachainStaking` pallet
 4. Choose the state to query. In this case, it is the `selectedCandidates` or `candidatePool` state
 5. Send the state query by clicking on the "+" button

Chaque extrinsèque fournit une réponse différente:

 - **selectedCandidates** — renvoie l'ensemble des collators actifs actuel, c'est-à-dire les {{ networks.moonbase.staking.max_collators }} meilleurs collators par total de jetons stakés (y compris les nominations)
 - **candidatePool** — renvoie la liste actuelle de tous les collators, y compris ceux qui ne sont pas dans l'ensemble actif

![Staking Account](/images/staking/staking-stake-1.png)

## Get the Collator Nominator Count {: #get-the-collator-nominator-count }

First, you need to get the `collator_nominator_count` as you'll need to submit this parameter in a later transaction. To do so, you'll have to run the following JavaScript code snippet from within [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/js):

```js
// Simple script to get collator_nominator_count
// Remember to replace COLLATOR_ADDRESS with the address of desired collator.
const collatorAccount = 'COLLATOR_ADDRESS'; 
const collatorInfo = await api.query.parachainStaking.collatorState2(collatorAccount);
console.log(collatorInfo.toHuman()["nominators"].length);
```

 1. Head to the "Developer" tab 
 2. Click on "JavaScript"
 3. Copy the code from the previous snippet and paste it inside the code editor box 
 4. (Optional) Click the save icon and set a name for the code snippet, for example, "Get collator_nominator_count". This will save the code snippet locally
 5. Click on the run button. This will execute the code from the editor box
 6. Copy the result, as you'll need it when initiating a nomination

![Get collator nominator count](/images/staking/staking-stake-3.png)

## Get your Number of Existing Nominations {: #get-your-number-of-existing-nominations }
If you've never made a nomination from your address you can skip this section. However, if you're unsure how many existing nominations you have, you'll want to run the following JavaScript code snippet to get `nomination_count` from within [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/js):

```js
// Simple script to get your number of existing nominations.
// Remember to replace YOUR_ADDRESS_HERE with your nominator address.
const yourNominatorAccount = 'YOUR_ADDRESS_HERE'; 
const nominatorInfo = await api.query.parachainStaking.nominatorState(yourNominatorAccount);
console.log(nominatorInfo.toHuman()["nominations"].length);
```

 1. Head to the "Developer" tab 
 2. Click on "JavaScript"
 3. Copy the code from the previous snippet and paste it inside the code editor box 
 4. (Optional) Click the save icon and set a name for the code snippet, for example, "Get existing nominations". This will save the code snippet locally
 5. Click on the run button. This will execute the code from the editor box
 6. Copy the result, as you'll need it when initiating a nomination

![Get existing nomination count](/images/staking/staking-stake-4.png)

## Comment nommer un Collator  {: #how-to-nominate-a-collator } 

Cette section décrit le processus de nomination des collators. Le tutoriel utilisera les collators suivants comme référence:

|  Variable  |     |                      Addresse                       |
| :--------: | :-: | :------------------------------------------------: |
| Collator 1 |     | {{ networks.moonbase.staking.collators.address1 }} |
| Collator 2 |     | {{ networks.moonbase.staking.collators.address2 }} |

Pour accéder aux fonctionnalités de staking, vous devez utiliser l'interface PolkadotJS Apps. Pour ce faire, vous devez d'abord importer / créer un compte de style Ethereum (adresse H160), chose que vous pouvez faire en suivant [ce guide](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account).

Pour cet exemple, un compte a été importé et nommé avec un nom super original: Alice.

Actuellement, tout ce qui concerne le staking doit être accessible via le menu "Extrinsics" , sous l'onglet "Developer":

![Compte de staking](/images/staking/staking-stake-5.png)

Pour désigner un collator, fournissez les informations suivantes:

 1. Sélectionnez le compte à partir duquel vous souhaitez miser vos jetons
 2. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `parachainStaking`
 3. Choisissez la méthode extrinsèque à utiliser pour la transaction. Cela déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas, c'est l'extrinsèque `nominate`
 4. Désignez l'adresse du collator que vous souhaitez nommer. Dans ce cas, il est défini sur `{{ networks.moonbase.staking.collators.address1 }}`
 5. Désignez le nombre de jetons que vous souhaitez miser
 6. Input the `collator_nominator_count` you [retrieved above from the JavaScript console](/staking/stake/#get-the-collator-nominator-count)
 7. Input the `nomination_count` [you retrieved from the Javascript console](/staking/stake/#get-your-number-of-existing-nominations). This is `0` if you haven't yet nominated a collator
 8. Click the "Submit Transaction" button and sign the transaction

![Staking Join Nominators Extrinsics](/images/staking/staking-stake-6.png)

!!! note
    The parameters used in steps 6 and 7 are for gas estimation purposes and do not need to be exact. However, they should not be lower than the actual values. 

Une fois la transaction confirmée, vous pouvez retourner dans l'onglet "Comptes" pour vérifier que vous disposez d'un solde réservé (égal au nombre de jetons mis en jeu).

Pour vérifier une nomination, vous pouvez accéder à "État de la chaîne" sous l'onglet "Developpeur" .

![Staking Account and Chain State](/images/staking/staking-stake-3.png)

Ici fournissez alors les informations suivantes:

 1. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `parachainStaking`
 2. Choose the state to query. In this case, it is the `nominatorState`
 3. Make sure to enable the "include option" slider
 4. Envoyez la requête d'état en cliquant sur le bouton "+"

![Staking Chain State Query](/images/staking/staking-stake-8.png)

Dans la réponse, vous devriez voir votre compte (dans ce cas, le compte d'Alice) avec une liste des nominations. Chaque nomination contient l'adresse cible du collator et le montant.

Vous pouvez suivre les mêmes étapes que celles décrites pour désigner d'autres assembleurs dans le réseau. Par exemple, Alice a également été nominée `{{ networks.moonbase.staking.collators.address2 }}` .

## Comment révoquer les nominations {: #how-to-stop-nominations } 

Si vous êtes déjà un nominateur, vous avez deux options pour révoquer vos nominations: utiliser l'extrinsèque `revokeNomination` pour retirer vos jetons d'un collator spécifique, ou utiliser l'extrinsèque `leaveNominators` pour révoquer toutes les nominations en cours.

Cet exemple est une continuation de la section précédente et suppose que vous avez au moins deux nominations actives.

Vous pouvez supprimer votre nomination d'un collator spécifique en accédant au menu "Extrinsics" en dessous de l'onglet "Developer" . Fournissez alors les informations suivantes:

 1. Sélectionnez le compte dont vous souhaitez supprimer votre nomination
 2. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `parachainStaking`
 3. Choisissez la méthode extrinsèque à utiliser pour la transaction. Cela déterminera les champs qui doivent remplir les étapes suivantes. Dans ce cas, c'est l'extrinsèque `revokeNomination`
 4. Désignez l'adresse du collecteur pour lequel vous souhaitez supprimer votre nomination. Dans ce cas, il est défini sur `{{ networks.moonbase.staking.collators.address2 }}`
 5. Cliquez sur le bouton "Soumettre la transaction" et signez la transaction

![Staking Revoke Nomination Extrinsic](/images/staking/staking-stake-9.png)

Une fois la transaction confirmée, vous pouvez vérifier que votre nomination a été supprimée dans l'option "État de la chaîne" sous l'onglet "Developpeur" .

Fournissez alors les informations suivantes:

 1. Choisissez la palette avec laquelle vous souhaitez interagir. Dans ce cas, c'est la palette `parachainStaking`
 2. Choisissez l'état à interroger. Dans ce cas, c'est l'état `nominatorState`
 3. Make sure to enable the "include options" slider
 4. Envoyez la requête d'état en cliquant sur le bouton "+"

![Staking Revoke Nomination Chin State](/images/staking/staking-stake-8.png)

Dans la réponse, vous devriez voir votre compte (dans ce cas, le compte d'Alice) avec une liste des nominations. Chaque nomination contient l'adresse cible du collator et le montant.

Comme mentionné précédemment, vous pouvez également supprimer toutes les nominations en cours avec l'extrinsèque `leaveNominators` (à l'étape 3 des instructions "Extrinsics"). Cet extrinsèque ne nécessite aucune entrée:

![Staking Leave Nominatiors Extrinsic](/images/staking/staking-stake-10.png)

Une fois la transaction confirmée, votre compte ne doit pas être répertorié dans l'état `nominatorState` lors de la requête et vous ne devez pas avoir de solde réservé (lié au staking).

## Récompenses de staking {: #staking-rewards } 

À mesure que les collators reçoivent les récompenses de la production de blocs, les nominateurs reçoivent également des récompenses. Un bref aperçu de la façon dont les récompenses sont calculées se trouve sur [cette page](/staking/overview/#reward-distribution).

En résumé, les nominateurs gagneront des récompenses au prorata de leur mise par rapport au total des nominations pour le collator récompensé (y compris la mise de collator lui même).

Dans l'exemple précédent, Alice a été récompensée avec `0.0044` jetons après deux tours de paiement:

![Staking Reward Example](/images/staking/staking-stake-1.png)
