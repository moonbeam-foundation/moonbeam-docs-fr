---
title: Collators
description: Instructions pour devenir un Collator dans le réseau Moonbeam une fois que vous exécutez un nœud
---

# Exécuter un Collator sur Moonbeam

![Collator Moonbeam Banner](/images/fullnode/collator-banner.png)

## Introduction {: #introduction } 

Les collators sont des membres du réseau qui maintiennent les parachains auxquelles ils participent. Ils exécutent un nœud complet (à la fois pour leur parachain particulière et pour la chaîne de relais), et ils produisent la preuve de transition d'état pour les validateurs de chaîne de relais.

Les utilisateurs peuvent créer des nœuds complets sur Moonbase Alpha et Moonriver et activer la fonction "collate" pour participer à l'écosystème en tant que collators.

Moonbeam utilise le [Nimbus Parachain Consensus Framework](/learn/consensus/). Cela fournit un filtre en deux étapes pour allouer aux collators un créneau de production de bloc :

  - Le filtre de staking de la parachain sélectionne les meilleurs {{ networks.moonbase.staking.max_collators }} collators sur Moonbase Alpha et les meilleurs {{ networks.moonriver.staking.max_collators }} collators sur Moonriver en termes de tokens stakés dans le réseau. Ce pool filtré est appelé candidats sélectionnés, et les candidats sélectionnés sont soumis à une rotation à chaque tour
  - Le filtre de sous-ensemble de taille fixe sélectionne un sous-ensemble pseudo-aléatoire des candidats précédemment sélectionnés pour chaque créneau de production de blocs

Ce guide vous guidera à travers les étapes suivantes :

  - **[Exigences techniques](#technical-requirements)** : vous indique les critères auxquels vous devez répondre d'un point de vue technique
  - **[Exigences Comptes et Stacking](#accounts-and-staking-requirements)** : passe par le processus de configuration de votre compte et de jetons de liaison pour devenir un candidat à l'assemblage
  - **[Générer des clés de session](#generate-session-keys)** : explique comment générer des clés de session, utilisées pour mapper votre ID d'auteur avec votre compte H160
  - **[Mappez l'identifiant de l'auteur sur votre compte](#map-author-id-to-your-account)** - décrit les étapes pour mapper votre clé de session publique sur votre compte H160, où les récompenses de bloc seront versées.

## Exigences techniques {: #technical-requirements } 

D'un point de vue technique, les collators doivent répondre aux exigences suivantes:

 - Avoir un nœud complet en cours d'exécution avec l' option de collation. Pour ce faire, suivez [ce tutoriel](/node-operators/networks/full-node/) en considérant les extraits de code spécifiques pour les collators
 - Activez le serveur de télémétrie pour votre nœud complet. Pour ce faire, suivez [ce tutoriel](/node-operators/networks/telemetry/)

## Exigences relatives au compte et au Staking {: #accounts-and-staking-requirements } 

Comme pour les validateurs Polkadot, vous devez créer un compte. Pour Moonbeam, il s'agit d'un compte H160 ou d'un compte de type Ethereum dont vous détenez les clés privées. En outre, vous devez disposer d'un montant minimum de jetons pour être considéré comme éligible (devenir un candidat). Seule une certaine quantité des meilleurs collators par enjeu nominatif sera dans l'ensemble actif.

=== "Moonbase Alpha"
    |    Variable     |                          Valeur                          |
    |:---------------:|:-------------------------------------------------------:|
    |   Montant de l'obligation   | {{ networks.moonbase.staking.collator_bond_min }} DEV   |
    | Taille de l'ensemble actif | {{ networks.moonbase.staking.max_collators }} collators |

=== "Moonriver"
    |    Variable     |                          Valeur                           |
    |:---------------:|:--------------------------------------------------------:|
    |   Montant de l'obligation   | {{ networks.moonriver.staking.collator_bond_min }} MOVR  |
    | Taille de l'ensemble actif | {{ networks.moonriver.staking.max_collators }} collators | 

## Compte dans PolkadotJS {: #account-in-polkadotjs } 

Un collator a un compte associé à ses activités de collation. Ce compte est utilisé pour l'identifier en tant que producteur de blocs et envoyer les paiements des récompenses de bloc.

Actuellement, vous avez deux façons de procéder en ce qui concerne la création d'un compte dans [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts):

 - Importer un compte H160 existant (ou créer un nouveau) à partir de portefeuilles externes ou de services tels que [MetaMask](/integrations/wallets/metamask/) et [MathWallet](/integrations/wallets/mathwallet/)
 - Créez un nouveau compte H160 avec [PolkadotJS](/integrations/wallets/polkadotjs/)

Une fois que vous avez un compte H160 importé dans PolkadotJS, vous devriez le voir sous l'onglet "Comptes" . Assurez-vous d'avoir votre adresse publique à portée de main (`PUBLIC_KEY`), car elle est nécessaire pour configurer le [déploiement de votre nœud complet](/node-operators/networks/full-node/) avec les options de collation.

![Account in PolkadotJS](/images/fullnode/collator-polkadotjs1.png)

## Devenir un candidat Collator {: #become-a-collator-candidate } 

Avant de commencer, il est important de noter le calendrier des différentes actions liées aux activités de collecte :

=== "Moonbase Alpha"
    |               Variable                |       valeur        |
    |:-------------------------------------:|:------------------:|
    |    Rejoindre/quitter candidats collator     | {{ networks.moonbase.collator_timings.join_leave_candidates.rounds }} rounds ({{ networks.moonbase.collator_timings.join_leave_candidates.hours }} heures) |
    |        Ajouter/supprimer des candidatures         | {{ networks.moonbase.collator_timings.add_remove_nominations.rounds }} rounds ({{ networks.moonbase.collator_timings.add_remove_nominations.hours }} heures) |
    | Paiements des récompenses (after current round) | {{ networks.moonbase.collator_timings.rewards_payouts.rounds }} rounds ({{ networks.moonbase.collator_timings.rewards_payouts.hours }} heures) |

=== "Moonriver"
    |               Variable                |       valeur        |
    |:-------------------------------------:|:------------------:|
    |    Rejoindre/quitter candidats collator     | {{ networks.moonriver.collator_timings.join_leave_candidates.rounds }} rounds ({{ networks.moonriver.collator_timings.join_leave_candidates.hours }} heures) |
    |        Ajouter/supprimer des candidatures         | {{ networks.moonriver.collator_timings.add_remove_nominations.rounds }} rounds ({{ networks.moonriver.collator_timings.add_remove_nominations.hours }} heures) |
    | Paiements des récompenses (after current round) | {{ networks.moonriver.collator_timings.rewards_payouts.rounds }} rounds ({{ networks.moonriver.collator_timings.rewards_payouts.hours }} heures) |

!!! note 
    Les valeurs présentées dans le tableau précédent sont susceptibles d'être modifiées dans les prochaines versions.

### Obtenir la taille du pool de candidats {: #get-the-size-of-the-candidate-pool } 

Tout d'abord, vous devez obtenir la taille de `candidatePool` (cela peut changer en fonction de la gouvernance) car vous devrez soumettre ce paramètre dans une transaction ultérieure. Pour ce faire, vous devrez exécuter l'extrait de code JavaScript suivant à partir de [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network# /js):

```js
// Simple script to get candidate pool size
const candidatePool = await api.query.parachainStaking.candidatePool();
console.log(`Candidate pool size is: ${candidatePool.length}`);
```

  1. Dirigez-vous vers l'onglet "Développeur"
  2. Cliquez sur "JavaScript"
  3. Copiez le code de l'extrait précédent et collez-le dans la zone de l'éditeur de code
  4. (Facultatif) Cliquez sur l'icône d'enregistrement et attribuez un nom à l'extrait de code, par exemple, « Obtenir la taille du pool candidat ». Cela enregistrera l'extrait de code localement
  5. Cliquez sur le bouton Exécuter. Cela exécutera le code de la boîte de l'éditeur
  6. Copiez le résultat, car vous en aurez besoin pour rejoindre le vivier de candidats

![Obtenir le nombre de candidats](/images/fullnode/collator-polkadotjs2.png)

### Rejoignez le pool de candidats {: #join-the-candidate-pool } 

Une fois que votre nœud est en fonctionnement et en synchronisation avec le réseau, vous devenez un candidat collator (et rejoignez le pool de candidats). Selon le réseau auquel vous êtes connecté, rendez-vous sur PolkadotJS pour [Moonbase Alpha].(https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts) ou [Moonriver](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.moonriver.moonbeam.network#/accounts) et suivez les étapes suivantes :

 1. Accédez à l'onglet "Développeurs" et cliquez sur "Extrinsèques"
 2. Sélectionnez le compte que vous souhaitez associer à vos activités de classement
 3. Confirmez que votre compte collator est alimenté avec au moins la [mise minimale requise] (#accounts-and-staking-requirements) prévoir un supplément pour les frais de transaction.
 4. Sélectionnez la palette « parachainStaking » dans le menu « soumettre les éléments extrinsèques suivants »
 5. Ouvrez le menu déroulant, qui répertorie toutes les extrinsèques possibles liées au staking, et sélectionnez la fonction `joinCandidates()`
 6. Fixez la caution à au moins le [montant minimum](#accounts-and-staking-requirements) pour être considéré comme un candidat collator. Seule la caution collator compte pour cette vérification. Les nominations supplémentaires ne comptent pas
 7. Définissez le nombre de candidats comme taille du pool de candidats. Pour savoir comment récupérer cette valeur, consultez [cette section](#get-the-size-of-the-candidate-pool)
 8. Soumettez la transaction. Suivez l'assistant et signez la transaction en utilisant le mot de passe que vous avez défini pour le compte

![Rejoindre le pool de collateurs PolkadotJS](/images/fullnode/collator-polkadotjs3.png)

!!! Remarque
     Les noms de fonction et l'exigence de caution minimale sont susceptibles de changer dans les versions futures.

Comme mentionné précédemment, seuls les meilleurs {{ networks.moonbase.staking.max_collators }} collators sur Moonbase Alpha et les meilleurs {{ networks.moonriver.staking.max_collators }} collators sur Moonriver par mise nominée seront dans l'ensemble actif. 
### Arrêter l'assemblage  {: #stop-collating } 

Semblable à la fonction `chill()` de Polkadot, pour quitter le pool de candidats de l'assembleur, suivez les mêmes étapes qu'auparavant mais sélectionnez la fonction `leaveCandidates()` à l'étape 5.
     
## clés de session  {: #session-keys } 

Avec la sortie de [Moonbase Alpha v8](/networks/testnet/), les collators signeront les blocs à l'aide d'un ID d'auteur, qui est essentiellement une [clé de session](https://wiki.polkadot.network/docs/learn-keys #session-keys). Pour correspondre à la norme de substrat, les clés de session de l'assembleur Moonbeam sont [SR25519](https://wiki.polkadot.network/docs/learn-keys#what-is-sr25519-and-where-did-it-come-from). Ce guide vous montrera comment créer/faire pivoter vos clés de session associées à votre nœud d'assemblage.

Tout d'abord, assurez-vous que vous [exécutez un nœud d'assemblage](/node-operators/networks/full-node/) et que vous avez exposé les ports RPC. Une fois que votre nœud d'assemblage est en cours d'exécution, votre terminal doit imprimer des journaux similaires :

![Journaux du terminal de l'assembleur](/images/fullnode/collator-terminal1.png)

Ensuite, les clés de session peuvent être tournées en envoyant un appel RPC au point de terminaison HTTP avec la méthode `author_rotateKeys`. Pour référence, si le point de terminaison HTTP de votre assembleur est sur le port "9933", l'appel JSON-RPC peut ressembler à ceci :


```
curl http://127.0.0.1:9933 -H \
"Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"author_rotateKeys",
    "params": []
  }'
```

Le nœud d'assemblage doit répondre avec la clé publique correspondante du nouvel ID d'auteur (clé de session).

![Coller Terminal Logs RPC Rotate Keys](/images/fullnode/collator-terminal2.png)

Assurez-vous de noter cette clé publique de l'ID de l'auteur. Ensuite, cela sera mappé sur une adresse de style H160 Ethereum à laquelle les récompenses de bloc sont versées.

## Mapper l'identifiant de l'auteur sur votre compte {: #map-author-id-to-your-account } 

Une fois que vous avez généré votre identifiant d'auteur (clés de session), l'étape suivante consiste à le mapper sur votre compte H160 (une adresse de style Ethereum). Assurez-vous de détenir les clés privées de ce compte, car c'est là que les récompenses de bloc sont versées.

Un lien est envoyé lors de la mise en correspondance de votre ID d'auteur avec votre compte. Ce lien est par ID d'auteur enregistré. Le lien est défini comme suit :

 - Moonbase Alpha - {{ networks.moonbase.staking.collator_map_bond }} DEV tokens 
 - Moonriver - {{ networks.moonriver.staking.collator_map_bond }} MOVR tokens. 

Le module `authorMapping` a les éléments extrinsèques suivants programmés :

 - **addAssociation**(*address* authorID) — associe votre ID d'auteur au compte H160 à partir duquel la transaction est envoyée, assurant ainsi le véritable propriétaire de ses clés privées. Il nécessite un [lien](#accounts-and-staking-requirements)
 - **clearAssociation**(*address* authorID) — efface l'association d'un ID d'auteur au compte H160 à partir duquel la transaction est envoyée, qui doit être le propriétaire de cet ID d'auteur. Rembourse également l'obligation
 - **updateAssociation**(*address* oldAuthorID, *address* newAuthorID) — met à jour le mappage d'un ancien ID d'auteur vers un nouveau. Utile après une rotation de clé ou une migration. Il exécute à la fois les extrinsèques d'association « ajouter » et « effacer » de manière atomique, permettant la rotation des clés sans avoir besoin d'une deuxième liaison

Le module ajoute également les appels RPC suivants (état de la chaîne) :

- **mapping**(*address* optionalAuthorID) — affiche tous les mappages stockés sur la chaîne, ou uniquement ceux liés à l'entrée s'ils sont fournis

### Cartographie extrinsèque {: #mapping-extrinsic } 

Pour associer votre ID d'auteur à votre compte, vous devez faire partie du [pool de candidats](#become-a-collator-candidate). Une fois que vous êtes candidat au collateur, vous devez envoyer un mapping extrinsèque (transaction). Notez que cela vous permettra d'obtenir des tokens par l'ID de l'auteur enregistré. Pour ce faire, suivez les étapes suivantes :

 1. Dirigez-vous vers l'onglet "Développeur"
 2. Sélectionnez l'option "Extrinsèques"
 3. Choisissez le compte auquel vous souhaitez mapper votre ID d'auteur à associer, à partir duquel vous signerez cette transaction
 4. Sélectionnez l'extrinsèque `authorMapping`
 5. Définissez la méthode sur `addAssociation()`
 6. Saisissez l'ID de l'auteur. Dans ce cas, il a été obtenu via l'appel RPC `author_rotateKeys` dans la section précédente
 7. Cliquez sur "Soumettre la transaction"

![Mappage de l'ID d'auteur sur le compte extrinsèque](/images/fullnode/collator-polkadotjs4.png)

Si la transaction est réussie, vous verrez une notification de confirmation sur votre écran. Au contraire, assurez-vous d'avoir rejoint le [pool de candidats](#become-a-collator-candidate).

![Mappage de l'ID d'auteur vers le compte extrinsèque réussi](/images/fullnode/collator-polkadotjs5.png)

### Vérification des mappages {: #checking-the-mappings } 

Vous pouvez également vérifier les mappages actuels sur la chaîne en vérifiant l'état de la chaîne. Pour ce faire, procédez comme suit :

  1. Dirigez-vous vers l'onglet "Développeur"
  2. Sélectionnez l'option "État de la chaîne"
  3. Choisissez `authorMapping` comme état à interroger
  4. Sélectionnez la méthode `mappingWithDeposit`
  5. Fournissez un ID d'auteur à interroger. En option, vous pouvez désactiver le curseur pour récupérer tous les mappages
  6. Cliquez sur le bouton "+" pour envoyer l'appel RPC

![État de la chaîne de mappage d'ID d'auteur](/images/fullnode/collator-polkadotjs6.png)

Vous devriez pouvoir voir le compte H160 associé à l'ID d'auteur fourni. Si aucun ID d'auteur n'était inclus, cela renverrait tous les mappages stockés dans la chaîne.
