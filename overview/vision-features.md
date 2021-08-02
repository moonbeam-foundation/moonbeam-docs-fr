---
title: Vision
description: Moonbeam est conçu pour permettre un avenir multi-chaînes, où les utilisateurs et les actifs peuvent se déplacer librement à travers de nombreuses chaînes spécialisées et hétérogènes.
---

# Notre vision pour Moonbeam

Nous croyons en un avenir multi-chaînes avec de nombreuses chaînes comptant plusieurs actifs et utilisateurs. Dans ce contexte, nous avons créé Moonbeam: une plate-forme de contrat intelligent qui fournit un environnement Ethereum compatible pour la construction d'applications décentralisées. Moonbeam a été conçu pour servir ces nouveaux types d'actifs et d'utilisateurs qui existent dans deux chaînes ou plus.

Les plates-formes de contrats intelligents existantes sont conçues pour servir les utilisateurs et les actifs sur une chaîne unique et spécifique. En fournissant une fonctionnalité de contrat intelligent inter-chaînes, Moonbeam permet aux développeurs de transférer les charges de travail et la logique existantes vers Moonbeam et d'étendre la portée de leurs applications à de nouveaux utilisateurs et actifs sur d'autres chaînes.

L'intégration croisée de Moonbeam est accomplie en devenant une [parachain](/resources/glossary/#parachains) sur le réseau Polkadot. Le [réseau Polkadot](/resources/glossary/#polkadot) fournit l'intégration et la connectivité entre les parachaines connectées au réseau. Il permet aussi l’intégration via des ponts à d'autres chaînes non basées sur Polkadot, telles que Ethereum et Bitcoin.

## À qui profite Moonbeam {: #who-benefits-from-moonbeam } 

Il y a trois cibles principales qui peuvent le plus bénéficier de la fonctionnalité cross-chain de Moonbeam:

### Projets existants basés sur Ethereum {: #existing-ethereum-based-projects } 

Les projets confrontés à des problèmes de coût et d'évolution sur Ethereum peuvent utiliser Moonbeam pour: 

 - Déplacez des parties de leurs charges de travail existantes et de leur état hors de la Layer 1 d'Ethereum avec des modifications minimales.  
 - Mettre en œuvre une approche hybride, où les applications vivent simultanément sur Ethereum et Moonbeam.  
 - Étendre leur portée au réseau Polkadot et aux autres chaînes connectées à Polkadot.  

### Projets de l’écosystème Polkadot {: #polkadot-ecosystem-projects } 

Les projets nécessitant une fonctionnalité de contrat intelligent peuvent utiliser Moonbeam pour:  

 - Augmentez leurs parachaines et parathreads existants.  
 - Ajoutez de nouvelles fonctionnalités nécessaires mais non incluses sur la  [chaîne de relais principale de Polkadot](/resources/glossary/#relay-chain).  Par exemple, ils pourraient créer un endroit où les équipes peuvent financer leurs projets, mettre en œuvre des “lockdrops” et traiter d'autres transactions financières plus complexes que celles fournies par la fonctionnalité [Substrate](/resources/glossary/#substrate) de base.  
 - Tirez parti de la chaîne d'outils de développement mature et étendue d’Ethereum.  

### Développeurs de nouveaux DApps {: #developers-of-new-dapps } 

Les individus et les équipes qui souhaitent essayer de construire sur Polkadot peuvent utiliser Moonbeam pour: 

 - Tirez parti des fonctionnalités spécialisées des parachaines Polkadot tout en atteignant les utilisateurs et les actifs d'autres chaînes.  
 - Composez la fonctionnalité des parachaines Polkadot en utilisant Moonbeam comme couche d'intégration légère qui regroupe les services réseau, avant de les présenter aux utilisateurs finaux. La mise en œuvre d'un service composé à l'aide d'intégrations prédéfinies sur une plate-forme de contrat intelligent sera (dans de nombreux cas) beaucoup plus rapide et plus facile que de créer un environnement d'exécution Substrate complet et d'effectuer les intégrations vous-même pendant l'exécution.  

## Principales caractéristiques et fonctionnalités {: #key-features-and-functionality } 

Moonbeam atteint ces objectifs avec les caractéristiques clés suivantes:  

 - **Décentralisé et sans autorisation** , fournissant une exigence de base pour la résistance à la censure et la prise en charge de nombreux cas d'utilisation de DApp existants et futurs.  
 - **Contient une implémentation EVM complète** , permettant la migration des contrats intelligents basés sur Solidity avec un minimum de changement et avec les résultats d'exécution attendus.  
 - **Implémente l'API Web3 RPC** afin que les frontaux DApp existants puissent être migrés avec un minimum de changements requis, et ainsi les outils existants basés sur Ethereum, tels que Truffle, Remix et MetaMask, peuvent être utilisés sans modification contre Moonbeam.  
 - **Compatible avec le jeu d'outils de l'écosystème de substrate** ,  y compris les explorateurs de blocs, les bibliothèques de développement frontales et les portefeuilles, permettant aux développeurs et aux utilisateurs d'utiliser le bon outil pour ce qu'ils tentent d'accomplir.  
 - **Intégration inter-chaînes native** via le réseau Polkadot et via des ponts de jetons, ce qui permet le mouvement des jetons, la visibilité de l'état et la transmission de messages avec Ethereum et d'autres chaînes.  
 - **Gouvernance en chaîne** pour permettre aux parties prenantes de faire évoluer rapidement et sans fourchette le protocole de base en fonction des besoins des développeurs et de la communauté.  

Cette combinaison unique d'éléments comble une lacune stratégique du marché, tout en permettant à Moonbeam de répondre aux futurs besoins des développeurs à mesure que le réseau Polkadot se développe au fil du temps. Construire votre propre chaîne avec Substrate est très utile, et s'accompagne également d'un certain nombre de tâches supplémentaires, telles que l'apprentissage et la mise en œuvre du runtime de la chaîne dans Rust, la création d'une économie de jetons et l'incitation d'une communauté d'opérateurs de nœuds.

Pour de nombreux développeurs et projets, une approche de contrat intelligent compatible Ethereum sera beaucoup plus simple et plus rapide à mettre en œuvre. Et en créant ces contrats intelligents sur Moonbeam, les développeurs peuvent toujours s'intégrer à d'autres chaînes et tirer parti des effets de réseau basés sur Polkadot.
