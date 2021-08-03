---
title: Précompilation du Staking
description: Démonstration de l'interface de précompilation Moonbeam Parachain Staking Ethereum Solidity
---

# Précompilation de Staking

![Staking Moonbeam Banner](/images/staking/staking-precompile-banner.png)

## Introduction {: #introduction } 

Une palette de proof of Stake déléguée a récemment fait ses débuts appelée [Parachain-Staking] (https://github.com/PureStake/moonbeam/tree/master/pallets/parachain-staking/src), permettant aux détenteurs de jetons (nominateurs) d'exprimer exactement pour quels candidats collecteurs (collators) ils aimeraient soutenir et avec quelle mise. La conception de la palette Parachain-Staking est telle qu'elle impose un partage des risques/récompenses sur la chaîne entre les délégants et les assembleurs.

Le module de Staking est codé en Rust et fait partie d'une palette qui n'est normalement pas accessible depuis le côté Ethereum de Moonbeam. Cependant, une précompilation de staking permet aux développeurs d'accéder aux fonctionnalités de staking à l'aide de l'API Ethereum dans un contrat précompilé situé à l'adresse `{{networks.moonbase.staking.precompile_address}}`. Le Staking Precompile a été publié pour la première fois dans [Moonbase Alpha v8 release](https://moonbeam.network/announcements/testnet-upgrade-moonbase-alpha-v8/).

## L'interface de solidity Parachain-Staking {: #the-parachain-staking-solidity-interface } 

[StakingInterface.sol](https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol) est une interface à travers laquelle les contrats de solidité peuvent interagir avec la Parachain-Staking. La beauté est que les développeurs de solidity n'ont pas à apprendre l'API Substrate. Au lieu de cela, ils peuvent interagir avec les fonctions de Staking à l'aide de l'interface Ethereum avec laquelle ils sont familiers.

L'interface comprend les fonctions suivantes :

- **is_nominator**(*address* collator) — fonction en lecture seule qui vérifie si l'adresse spécifiée est actuellement un nominateur de jalonnement
 - **is_candidate**(*address* collator) — fonction en lecture seule qui vérifie si l'adresse spécifiée est actuellement un collator candidat
 - **min_nomination**() — fonction en lecture seule qui obtient le montant minimum de nomination
 - **join_candidates**(*uint256* montant) — permet au compte de rejoindre l'ensemble des candidats collator avec un montant de caution spécifié
 - **leave_candidates**() - supprime immédiatement le compte du pool de candidats pour empêcher les autres de le sélectionner en tant qu'assembleur et déclenche la dissociation une fois les tours de BondDuration écoulés
 - **go_offline**() — quitter temporairement l'ensemble des candidats à l'assemblage sans dissociation
 - **go_online**() — rejoint l'ensemble des candidats à l'assemblage après avoir appelé précédemment go_offline()
 - **candidate_bond_more**(*uint256* more) — le candidat collateur augmente la caution d'un montant spécifié
 - **candidate_bond_less**(*uint256* less) - le candidat de l'assembleur diminue la caution d'un montant spécifié
 - **nominer**(*adresse* assembleur, *uint256* montant) — si l'appelant n'est pas un nominateur, cette fonction l'ajoute à l'ensemble des nominateurs. Si l'appelant est déjà un nominateur, il ajuste le montant de sa nomination
 - **leave_nominators**() - quitter l'ensemble des nominateurs et révoquer toutes les nominations en cours
 - **revoke_nominations**(*address* collator) — révoquer une nomination spécifique
 - **nominator_bond_more**(*address* assembleur, *uint256* more) — le nominateur augmente le lien avec un assembleur d'un montant spécifié
 - **nominator_bond_less**(*address* assembleur, *uint256* less) — le nominateur diminue le lien avec un assembleur d'un montant spécifié

## Interagir avec la précompilation de Staking {: #interacting-with-the-staking-precompile } 

### Vérification des prérequis {: #checking-prerequisites } 
L'exemple ci-dessous est illustré sur Moonbase Alpha, cependant, il est compatible avec tous les réseaux, y compris Moonriver et Moonbeam.

  - Avoir MetaMask installé et [connecté à Moonbase Alpha](/getting-started/moonbase/metamask/)
  - Avoir un compte avec plus de `{{networks.moonbase.staking.min_nom_stake}}` jetons. Vous pouvez l'obtenir à partir de [Mission Control](/getting-started/moonbase/faucet/)

!!! remarque
    L'exemple ci-dessous nécessite plus de `{{networks.moonbase.staking.min_nom_stake}}` jetons en raison du montant minimum de nomination plus les frais de gas. Si vous avez besoin de plus que les faucets distribuent, veuillez nous contacter sur Discord et nous serons heureux de vous aider.

### Configuration de Remix {: #remix-set-up } 
1. Obtenez une copie de [StakingInterface.sol](https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol)
2. Copiez et collez le contenu du fichier dans un fichier Remix nommé StakingInterface.sol

![Copier et coller l'interface de staking dans Remix](/images/staking/staking-precompile-1.png)

### Compiler le contrat {: #compile-the-contract } 
1. Cliquez sur l'onglet Compiler, deuxième à partir du haut
2. Compilez [Staking Interface.sol] (https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol)

![Compilation de StakingInteface.sol](/images/staking/staking-precompile-2.png)

### Accéder au contrat {: #access-the-contract } 
1. Cliquez sur l'onglet Déployer et exécuter, juste en dessous de l'onglet Compiler dans Remix. **Remarque** : nous ne déployons pas de contrat ici, mais nous accédons à un contrat précompilé déjà déployé
2. Assurez-vous que "Injected Web3" est sélectionné dans la liste déroulante Environnement
3. Assurez-vous que "ParachainStaking - StakingInterface.sol" est sélectionné dans la liste déroulante Contrat. Puisqu'il s'agit d'un contrat précompilé, il n'est pas nécessaire de déployer, à la place, nous allons fournir l'adresse de la précompilation dans le champ « À l'adresse »
4. Fournissez l'adresse de la précompile de Staking : `{{networks.moonbase.staking.precompile_address}}` et cliquez sur « À l'adresse »

![Fournir l'adresse](/images/staking/staking-precompile-3.png)

### Nommez un Collecteur {: #nominate-a-collator } 
Pour cet exemple, nous allons nommer un collecteur. Les nominateurs sont des détenteurs de jetons qui misent des jetons, se portant garants pour des collecteurs spécifiques. Tout utilisateur qui détient un montant minimum de {{networks.moonbase.staking.min_nom_stake}} tokens comme solde gratuit peut devenir un nominateur.

1. Développez le panneau avec l'adresse du contrat. Localisez la fonction de nomination et développez le panneau pour voir les paramètres
2. Fournissez l'adresse d'un collecteur tel que `{{ networks.moonbase.staking.collators.address1 }}`
3. Indiquez le montant à proposer dans WEI. Il y a un minimum de `{{networks.moonbase.staking.min_nom_stake}}` jetons à nommer, donc le montant le plus bas dans WEI est `5000000000000000000`
4. Appuyez sur "transact" et confirmez la transaction dans Metamask

![Nomer un collecteur](/images/staking/staking-precompile-4.png)

### Vérifier la nomination {: #verify-nomination } 
Pour vérifier que votre nomination a réussi, vous pouvez vérifier l'état de la chaîne dans les applications Polkadot.js. Tout d'abord, ajoutez votre adresse Metamask au [carnet d'adresses dans les applications Polkadot.js](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/addresses) . Si vous avez déjà terminé cette étape, vous pouvez passer à la section suivante.

#### Ajouter l'adresse Metamask au carnet d'adresses {: #add-metamask-address-to-address-book } 
1. Accédez à Comptes -> Carnet d'adresses
2. Cliquez sur "Ajouter un contact"
3. Ajoutez votre adresse métamasque
4. Fournissez un surnom pour le compte

![Ajouter au carnet d'adresses](/images/staking/staking-precompile-5.png)

#### Vérifier l'état du nominateur {: #verify-nominator-state } 
1. Pour vérifier que votre nomination a réussi, rendez-vous sur [Polkadot.js Apps](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/chainstate) et accédez à Développeur -> État de la chaîne
2. Sélectionnez la palette "parachainStaking"
3. Sélectionnez la requête "nominatorState"
4. Cliquez sur le bouton "Plus" pour retourner les résultats et vérifier votre nomination

![Vérifier la nomination](/images/staking/staking-precompile-6.png)

### Révocation d'une nomination {: #revoking-a-nomination } 
Pour révoquer une nomination et récupérer vos jetons, appelez la méthode `revoke_nomination`, en fournissant la même adresse que celle avec laquelle vous avez commencé la nomination ci-dessus. Vous pouvez vérifier à nouveau l'état de votre nominateur sur Polkadot.js Apps pour confirmer.

![Révoquer la nomination](/images/staking/staking-precompile-7.png)
