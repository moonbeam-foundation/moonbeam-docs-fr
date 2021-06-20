---
title: Collators
description: Instructions pour devenir un Collator dans le réseau Moonbeam une fois que vous exécutez un nœud
---

# Exécuter un Collator sur Moonbeam

![Collator Moonbeam Banner](/images/fullnode/collator-banner.png)

## Introduction

Les collators sont des membres du réseau qui maintiennent les parachains auxquelles ils participent. Ils exécutent un nœud complet (à la fois pour leur parachain particulière et pour la chaîne de relais), et ils produisent la preuve de transition d'état pour les validateurs de chaîne de relais.

Avec la sortie de Moonbase Alpha v6, les utilisateurs peuvent non seulement créer des nœuds complets, mais ils peuvent également activer la fonctionnalité `collate` et participer à l'écosystème en tant que collators.

Ce guide vous guidera à travers les étapes de création de votre nœud de collator, qui est une extension d'un nœud complet.

## Exigences techniques

D'un point de vue technique, les collators doivent répondre aux exigences suivantes:

 - Avoir un nœud complet en cours d'exécution avec l' option de collation. Pour ce faire, suivez [ce tutoriel](/node-operators/networks/full-node/) en considérant les extraits de code spécifiques pour les collators
 - Activez le serveur de télémétrie pour votre nœud complet. Pour ce faire, suivez [ce tutoriel](/node-operators/networks/telemetry/)

## Exigences relatives au compte et au Staking

Semblable aux validateurs Polkadot, vous devez créer un compte (bien que dans ce cas, il s'agisse d'un compte H160) et avoir une mise nommée (jetons DEV) afin de procéder à la production de blocs. Les créneaux horaires sont actuellement limités à {{ networks.moonbase.collators_slots }}, mais peuvent être augmentés au fil du temps.  

Les collators doivent avoir un minimum de {{ networks.moonbase.staking.collator_min_stake }} DEV pour être considérés comme éligibles (devenir un candidat). Seuls les {{ networks.moonbase.staking.max_collators }} meilleurs collators par enjeu désigné feront partie de l'ensemble actif.    

!!! note
    Actuellement, la création ou l'importation d'un compte dans PolkadotJS via une graine mnémonique entraînera une adresse publique différente si vous essayez ultérieurement d'importer ce compte dans un portefeuille Ethereum tel que MetaMask. En effet, PolkadotJS utilise BIP39, tandis qu'Ethereum utilise BIP32 ou BIP44. 

## Compte dans PolkadotJS

Un collator a un compte associé à ses activités de collation. Ce compte est utilisé pour l'identifier en tant que producteur de blocs et envoyer les paiements des récompenses de bloc.

Actuellement, vous avez deux façons de procéder en ce qui concerne la création d'un compte dans [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts):

 - Importer un compte H160 existant (ou créer un nouveau) à partir de portefeuilles externes ou de services tels que [MetaMask](/integrations/wallets/metamask/) et [MathWallet](/integrations/wallets/mathwallet/)
 - Créez un nouveau compte H160 avec [PolkadotJS](/integrations/wallets/polkadotjs/)

Une fois que vous avez un compte H160 importé dans PolkadotJS, vous devriez le voir sous l'onglet "Comptes" . Assurez-vous d'avoir votre adresse publique à portée de main (`PUBLIC_KEY`), car elle est nécessaire pour configurer le [déploiement de votre nœud complet](/node-operators/networks/full-node/) avec les options de collation.

![Account in PolkadotJS](/images/fullnode/collator-polkadotjs1.png)

## Devenir un candidat Collator

Une fois que votre nœud est en cours d'exécution et synchronisé avec le réseau, vous devenez un candidat collator en suivant les étapes ci-dessous dans [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts):

 1. Accédez à l'onglet "Developpers" et cliquez sur "Extrinsics"
 2. Sélectionnez le compte que vous souhaitez associer à vos activités de collation
 3. Confirmez que votre compte collator est alimenté avec au moins {{ networks.moonbase.staking.collator_min_stake }} jetons DEV plus des frais supplémentaires pour les frais de transaction 
 4. Sélectionnez la palette `parachainStaking` dans le menu "submit the following extrinsics"
 5. Ouvrez le menu déroulant, qui répertorie tous les extrinsèques possibles liés au staking, et sélectionnez la fonction `joinCandidates()`
 6. Définissez la caution sur au moins {{ networks.moonbase.staking.collator_min_stake }}, qui est le montant minimum pour être considéré comme candidat collator. Seule la caution du collator compte pour cette vérification. Les nominations supplémentaires ne comptent pas
 7. Soumettez la transaction. Suivez l'assistant et signez la transaction à l'aide du mot de passe que vous avez défini pour le compte

![Join Collators pool PolkadotJS](/images/fullnode/collator-polkadotjs2.png)

!!! note
    Les noms de fonction et l'exigence de caution minimale sont susceptibles de changer dans les versions futures.

Comme mentionné précédemment, seuls les {{ networks.moonbase.staking.max_collators }} meilleurs collators par enjeu désigné feront partie de l'ensemble actif. 

## Arrêter le collating(production de blocs)

Similaire à la fonction `chill()` de Polkadot , pour quitter le pool de candidats de collator, suivez les mêmes étapes que précédemment, mais sélectionnez la fonction `leaveCandidates()` à l'étape 5.


## Délais

Le tableau suivant présente certains des délais concernant les différentes actions liées aux activités de collation :

|                Action               |   |   Tours  |   |   Heures  |
|:-----------------------------------:|:-:|:---------:|:-:|:--------:|
|  Rejoindre / quitter les candidats collators     |   |     2     |   |    4     |
|      Ajouter / supprimer des nominations         |   |     1     |   |    2     |
|Paiements de récompenses (après le tour en cours)|   |     2     |   |    4     |


!!! note 
    Les valeurs présentées dans le tableau précédent sont susceptibles d'être modifiées dans les versions futures.

