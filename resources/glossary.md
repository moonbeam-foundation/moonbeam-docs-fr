---
title: Glossaire
description: Nous avons compilé un glossaire de termes liés à Polkadot qui vous permettra d'en savoir plus sur l'écosystème plus facilement.
---

# Glossaire

Il existe une grande quantité de terminologie spécifique à Polkadot, Substrate et à l'écosystème émergent Parity/Web3. Nous avons compilé une liste de termes que nous pensons que vous voudrez connaître en examinant la documentation, les plans et les didacticiels de Moonbeam.

### Collators(producteurs de blocs) {: #collators } 

L'un des principaux participants au réseau nécessaire pour soutenir les parachaines au sein du réseau Polkadot.  Dans Moonbeam, les collators sont les nœuds responsables de la production des blocs et de la soumission des blocs produits jusqu'à la chaîne de relais Polkadot pour finalisation.

### Nominators(nominateurs) {: #nominators } 

Les détenteurs de jetons qui choisissent de "soutenir" un validateur. Ils peuvent recevoir une partie de la récompense du validateur, mais sont soumis à la réduction de leurs jetons staker au cas où le validateur se comporterait mal. Un nominateur peut soutenir jusqu'à 16 validateurs et leur engagement est entièrement répartie entre les validateurs soutenus sélectionnés pour le jeu de validateurs. Si vous souhaitez nommer PureStake pour Polkadot et/ou Kusama, veuillez suivre [ce guide](https://www.purestake.com/technology/polkadot-validator/).

### Preuve de participation nominée {: #nomitated-proof-of-stake } 

Le mécanisme utilisé par Polkadot pour sélectionner son ensemble de validateurs de blocs pour maximiser la sécurité de la chaîne. À la base, il s'agit d'un système de preuve de participation (PoS) dans lequel les nominateurs soutiennent les validateurs. Ces derniers avec le soutien le plus élevé sont sélectionnés pour faire partie de l'ensemble de validateurs pour une session. La mise d'un validateur est réduite en cas de mauvaise conduite. Ainsi, les proposants sont tenus de faire preuve de diligence raisonnable sur les validateurs qu'ils nomment. Si vous souhaitez nommer PureStake pour Polkadot et/ou Kusama, veuillez suivre [ce guide](https://www.purestake.com/technology/polkadot-validator/).

### Parachains {: #parachains } 

Une blockchain qui a un slot et qui est connectée à Polkadot. Les parachains reçoivent une sécurité partagée de Polkadot et la possibilité d'interagir avec d'autres parachains sur le réseau Polkadot. Ils doivent verrouiller DOT, le jeton de chaîne de relais natif, pour sécuriser un emplacement pendant une période spécifique (jusqu'à deux ans).

### Parathreads {: #parathreads } 

Une blockchain qui peut se connecter à Polkadot. Les parathreads sont capables d'interagir avec d'autres membres du réseau Polkadot, mais ils soumissionnent pour la finalisation de bloc (en DOT) sur une base de bloc à bloc. Ils sont en concurrence avec d'autres parathreads pour la finalisation du bloc, ce qui signifie que le bloc avec l'enchère la plus élevée est sélectionné pour être finalisé lors de ce tour.

### Polkadot {: #polkadot } 

Un réseau de blockchains connectées qui offre une sécurité partagée et la possibilité d'interagir entre les chaînes. Polkadot est construit en utilisant le framework de développement Substrate. Les chaînes qui se connectent à Polkadot sont appelées parachains.

### Chaîne relais {: #relay-chain } 

La blockchain principale supportant le réseau Polkadot. Les parachains se connectent à la chaîne de relais et l'utilisent pour la sécurité partagée et la transmission de messages. Les validateurs sur la chaîne de relais aident à sécuriser les parachains.

### Contrat intelligent {: #smart-contract } 

Un contrat intelligent est un programme informatique ou un protocole de transaction destiné à exécuter, contrôler ou documenter automatiquement des événements et des actions juridiquement pertinents conformément aux termes d'un contrat ou d'un accord. Les contrats intelligents visent à réduire le besoin d'intermédiaires de confiance, les arbitrages et les coûts d'exécution, ainsi que de réduire les pertes dues à la fraude et les exceptions malveillantes et accidentelles. [Apprenez-en plus](https://en.wikipedia.org/wiki/Smart_contract).

### Substrate {: #substrate } 

Un cadre de développement de blockchain basé sur Rust créé par Parity Technologies sur la base de leur expérience dans la mise en œuvre de plusieurs clients blockchain.  Substrate est livré avec de nombreux modules et fonctionnalités nécessaires à la création d'une blockchain, notamment la mise en réseau P2P, les mécanismes de consensus, le staking, la crypto-monnaie, les modules de gouvernance en chaîne, etc.  Cela réduit considérablement le temps et les efforts d'ingénierie nécessaires pour mettre en œuvre une blockchain. 

### Palettes cadres Substrate {: #substrate-frame-pallets } 

Les palettes cadre de substrate sont une collection de modules basés sur Rust, fournissant les fonctionnalités requises pour créer une blockchain.  

### Validators(validateurs) {: #validators } 

Un nœud qui sécurise la chaîne de relais Polkadot en stakant DOT dans le réseau, qui est coupé s'ils se comportent mal. Ils finalisent les blocs des collators sur les parachains et participent également au consensus pour le prochain bloc de la chaîne de relais avec d'autres validateurs.

### WebAssembly/Wasm {: #webassemblywasm } 

WebAssembly est un standard ouvert qui définit un format de code binaire portable. Il est pris en charge par différents langages de programmation, compilateurs et navigateurs.
