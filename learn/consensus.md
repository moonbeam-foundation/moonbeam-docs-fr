---
title: Consensus des PArachaines
description: Découvrez tout les détails du consensus Nimbus de Moonbeam et son fonctionnement dans le cadre du modèle de sécurité partagé de Polkadot
---

# Le framework du consensus de la Parachaine Nimbus

![Bannière de consensus Moonbeam](/images/consensus/consensus-banner.png)

## Introduction

Polkadot s'appuie sur un [modèle de consensus hybride](https://wiki.polkadot.network/docs/learn-consensus). Dans un tel schéma, le gadget de finalité de bloc et le mécanisme de production de bloc sont séparés. Par conséquent, les parachaines n'ont plus qu'à se soucier de produire des blocs et s'appuient sur la chaîne relais pour valider les transitions d'état.

Au niveau de la parachaine, les producteurs de blocs sont appelés [assembleurs](https://wiki.polkadot.network/docs/learn-collator). Ils maintiennent des parachaînes (telles que Moonbeam) en collectant les transactions des utilisateurs et en offrant des blocs à la chaîne de relais [validateurs] (https://wiki.polkadot.network/docs/learn-validator).

Cependant, les parachains peuvent trouver les problèmes suivants qu'ils doivent résoudre dans une affaire décentralisée et sans confiance (le cas échéant) :

 - Parmi tous les nœuds du réseau, lesquels sont autorisés à créer des blocs ?
 - Si plusieurs nœuds sont autorisés, seront-ils éligibles en même temps ? Seulement un? Peut-être quelques-uns ?

Nimbus est un framework permettant de créer des algorithmes de consensus basés sur des slots sur des parachains basées sur [Cumulus](https://github.com/paritytech/cumulus). Il s'efforce de fournir des implémentations standard pour les parties logistiques de ces moteurs de consensus et des caractéristiques utiles pour la mise en œuvre des éléments (filtres) que les chercheurs et les développeurs souhaitent personnaliser. Ces filtres peuvent être personnalisables pour définir ce qu'est un slot de paternité de bloc et peuvent être composés, de sorte que la paternité de bloc est limitée à un sous-ensemble de collationneurs en plusieurs étapes.

Par exemple, Moonbeam utilise une approche à deux couches. La première couche comprend le filtre de jalonnement de la parachaine, qui aide à sélectionner un pool d'assembleurs actifs parmi tous les candidats assembleurs à l'aide d'un classement basé sur l'enjeu. La deuxième couche ajoute un autre filtre qui réduit le nombre d'assembleurs à un sous-ensemble pour chaque emplacement.

Notez que Nimbus ne peut répondre qu'aux assembleurs éligibles pour produire un bloc parachain dans le prochain emplacement disponible. C'est le mécanisme de consensus [Cumulus](https://wiki.polkadot.network/docs/build-cumulus#docsNav) qui marque ce bloc de la parachaine comme le meilleur, et finalement le [BABE](https://wiki.polkadot. network/docs/learn-consensus#babe) et [GRANDPA](https://wiki.polkadot.network/docs/learn-consensus#grandpa-finality-gadget) modèle de consensus hybride (de la chaîne de relais) qui inclura ce bloquer la parachaine dans la chaîne de relais et la finaliser. Une fois que toutes les fourches de chaîne de relais sont résolues au niveau de la chaîne de relais, ce bloc de parachain est finalisé de manière déterministe.

Les deux sections suivantes décrivent la stratégie de filtrage actuellement utilisée dans Moonbeam.

## Filtrage du staking de la Parachaine

Les assembleurs peuvent rejoindre le pool de candidats en liant simplement des jetons via un extrinsèque. Une fois dans le pool, les détenteurs de jetons peuvent ajouter à la mise de l'assembleur via une nomination (également appelée staking), c'est-à-dire au niveau de la parachaine.

Le staking de la Parachaine est le premier des deux filtres Nimbus appliqués au pool de candidats assembleurs. Il sélectionne les meilleurs {{ network.moonbase.staking.max_collators }} assembleurs en termes de jetons mis en jeu dans le réseau, ce qui inclut le lien assembleur et les nominations des détenteurs de jetons. Ce pool filtré est appelé candidats sélectionnés, et les candidats sélectionnés sont renouvelés à chaque tour (qui dure {{ networks.moonbase.staking.round_blocks }} blocs). Pour un tour donné, le schéma suivant décrit le filtrage du staking de la parachaine :

![Filtre de staking de la Parachaine Nimbus](/images/consensus/consensus-images1.png)

À partir de ce pool, un autre filtre est appliqué pour récupérer un sous-ensemble d'assembleurs éligibles pour le prochain créneau de création de bloc.

Si vous souhaitez en savoir plus sur le staking, visitez notre [documentation de staking](/staking/overview/).

## Filtrage de sous-ensembles de taille fixe

Une fois que le filtre de staking de la parachaine est appliqué et que les candidats sélectionnés sont récupérés, un deuxième filtre est appliqué bloc par bloc et aide à réduire les candidats sélectionnés à un plus petit nombre d'assembleurs éligibles pour le prochain créneau de création de bloc.

En termes généraux, ce deuxième filtre sélectionne un sous-ensemble pseudo-aléatoire des candidats précédemment sélectionnés. Le taux d'éligibilité, un paramètre réglable, définit la taille de ce sous-ensemble.

Un taux d'éligibilité élevé réduit les chances pour le réseau de sauter un créneau de production de blocs, car davantage d'assembleurs seront éligibles pour proposer un bloc pour un créneau spécifique. Cependant, seul un certain nombre de validateurs sont affectés à une parachain, ce qui signifie que la plupart de ces blocs ne seront pas soutenus par un validateur. Pour ceux qui le sont, un nombre plus élevé de blocs sauvegardés signifie qu'il faudra peut-être plus de temps à la chaîne de relais pour résoudre tous les forks possibles et renvoyer un bloc finalisé. De plus, cela pourrait créer un avantage injuste pour certains assembleurs qui pourraient être en mesure d'obtenir leur bloc proposé plus rapidement pour relayer les validateurs de chaîne, garantissant une partie plus élevée des récompenses de bloc (le cas échéant).

Au contraire, un taux d'éligibilité inférieur pourrait permettre des délais de finalisation des blocs plus rapides et une répartition plus équitable de la production des blocs entre les assembleurs. Cependant, si les assembleurs éligibles ne sont pas en mesure de proposer un bloc (pour une raison quelconque), le réseau sautera un bloc, affectant sa stabilité.

Une fois la taille du sous-ensemble définie, les assembleurs sont sélectionnés au hasard à l'aide d'une source d'entropie. Actuellement, un algorithme interne de retournement de pièces est implémenté, mais il sera ensuite migré pour utiliser la [balise aléatoire] de la chaîne de relais (https://wiki.polkadot.network/docs/learn-randomness). Par conséquent, un nouveau sous-ensemble d'assembleurs éligibles est sélectionné pour chaque bloc de chaîne de relais. Pour un tour donné et un bloc `XYZ` donné, le schéma suivant décrit le filtrage de sous-ensemble de taille fixe :

![Filtre de jalonnement Nimbus Parachain](/images/consensus/consensus-images2.png)

## Pourquoi Nimbus ?

Vous vous demandez peut-être : mais pourquoi Nimbus ? Initialement, cela n'était pas envisagé lors du développement de Moonbeam. Au fur et à mesure que Moonbeam progressait, la nécessité d'un mécanisme de consensus de la parachaine plus personnalisable mais simple est devenue claire, car les méthodes disponibles présentaient certains inconvénients ou limitations techniques.

<!-- Dans le [consensus de la chaîne de relais](https://github.com/paritytech/cumulus/blob/master/client/consensus/relay-chain/src/lib.rs), chaque nœud se considère comme un colator et peut proposer un bloc candidat parachain. Il appartient ensuite à la chaîne de relais de résoudre les éventuelles fourches et de finaliser un bloc.

Le mécanisme de consensus [AuRa](https://crates.io/crates/sc-consensus-aura) (abréviation de tour d'autorité) est basé sur une liste connue d'autorités qui se relaient pour produire des blocs dans chaque emplacement. Chaque autorité ne peut proposer qu'un seul bloc par slot et s'appuie sur la chaîne la plus longue.-->

Avec Nimbus, écrire un moteur de consensus parachaine est aussi simple qu'écrire une palette ! Cette simplicité et cette flexibilité sont sa principale valeur ajoutée.

Certains avantages techniques de Nimbus sont examinés dans les sections suivantes.

### Poids et exécution supplémentaire

Nimbus place l'exécution de la vérification de l'auteur dans une [Palette de substrate] (https://substrate.dev/docs/en/knowledgebase/runtime/pallets). À première vue, vous pourriez penser que cela ajoute une charge d'exécution plus élevée à un seul bloc par rapport à cette vérification hors chaîne. Mais considérez cela du point de vue d'un validateur

Les validateurs devront également vérifier l'auteur. En mettant la logique d'exécution de vérification d'auteur dans une palette, le temps d'exécution peut être étalonné et quantifié avec des poids. Si ce temps d'exécution n'est pas pris en compte, il existe un risque qu'un bloc dépasse la limite d'exécution de la chaîne relais WASM (actuellement 0,5 seconde).

En pratique, cette vérification sera rapide et ne poussera probablement pas le temps d'exécution au-delà de la limite. Mais d'un point de vue théorique, il est préférable de tenir compte de son poids à des fins de mise en œuvre.

### Réutilisabilité

Un autre avantage du déplacement de l'exécution de vérification de l'auteur vers une palette, plutôt qu'un exécuteur personnalisé, est qu'un seul exécuteur peut être réutilisé pour tout consensus pouvant être exprimé dans le cadre Nimbus. Il s'agit d'algorithmes basés sur des slots et scellés par signature.

Par exemple, le [consensus fourni par la chaîne de relais](https://github.com/paritytech/cumulus/blob/master/client/consensus/relay-chain/src/lib.rs), [AuRa](https:/ /crates.io/crates/sc-consensus-aura) et [BABE](https://crates.io/crates/sc-consensus-babe) ont chacun leur propre exécuteur personnalisé. Avec Nimbus, ces mécanismes de consensus peuvent réutiliser le même exécuteur. La puissance de la réutilisabilité est démontrée par l'implémentation Nimbus d'AuRa en moins de 100 lignes de code.

### Consensus d'échange à chaud

Les équipes qui construisent des parachaines peuvent vouloir changer, ajuster ou ajuster leur algorithme de consensus de temps en temps. Sans nimbus, l'échange de consensus nécessiterait une mise à niveau du client et un hard fork.

Avec le framework Nimbus, écrire un moteur de consensus est aussi simple que d'écrire une [Palette de substrate](https://substrate.dev/docs/en/knowledgebase/runtime/pallets). Par conséquent, échanger un consensus est aussi simple que de mettre à niveau une palette.

Néanmoins, l'échange à chaud est toujours limité par des moteurs de consensus (filtres) qui s'intègrent dans Nimbus, mais cela pourrait être utile pour les équipes qui sont encore confiantes quant au consensus qu'elles souhaitent mettre en œuvre à long terme.
