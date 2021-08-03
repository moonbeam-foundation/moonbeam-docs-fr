---
title: Intégration inter-chaînes
description: Les intégrations inter-chaînes de Moonbeam prendront en charge à la fois les parachaînes basées sur Polkadot et les chaînes non-Polkadot, comme Ethereum.
---

# Plans d'intégration inter-chaînes

L'une des principales fonctionnalités prévues pour Moonbeam est de fournir aux développeurs un moyen simple d'utiliser des contrats intelligents pour s'intégrer à d'autres chaînes de l'écosystème Polkadot.

Polkadot définit un protocole d'intégration de bas niveau, appelé Cross-Chain Message Passing (XCMP), qui peut être utilisé pour faciliter la communication entre les parachains au sein du réseau Polkadot et partager une logique de confiance entre les chaînes du réseau Polkadot, appelées shared protected runtime execution enclaves (SPREE). Parity est en train de mettre en œuvre XCMP et est en phase de conception de SPREE au moment de la rédaction de ce document (juillet 2020). Après le lancement du réseau principal Polkadot, le support XCMP et SPREE sera publié sous forme de mises à niveau de la chaîne de relais Polkadot. Nous prévoyons de mettre en œuvre et de prendre en charge des scénarios d'intégration basés sur ces protocoles lorsqu'ils sont disponibles.

## Une analogie: le "Bash de Polkadot" {: #an-analogy-the-bash-of-polkadot } 

Pour nous, Polkadot remplira une fonction similaire à quelque chose comme Linux. Les deux sont des plates-formes orientées développeur qui viennent avec des bibliothèques pour faciliter la création d'applications. 

Rappelez-vous l'ancienne philosophie Unix, où les utilisateurs créent des outils qui font un travail et le font bien. Ceci est similaire à la spécialisation que nous attendons des parachains sur Polkadot. Sous Linux, vous pouvez combiner et composer ces outils spécialement conçus pour obtenir des effets d'ordre supérieur en utilisant un bash de type shell. Nous prévoyons que les contrats intelligents basés sur Moonbeam fourniront un environnement analogue “de type Bash”, dans lequel des contrats intelligents spécialisés et des fonctionnalités de parachain pourront être composés pour atteindre des objectifs d'ordre supérieur.

Il se peut que les projets commencent comme un ou plusieurs contrats intelligents Moonbeam et migrent au fil du temps pour devenir des “applications natives” — qui pourraient être des parathreads ou des parachains dans le contexte de Polkadot - s'ils ont besoin de plus de performances ou d'un contrôle plus direct sur leurs économies.

## Intégration avec d'autres chaînes basées sur Polkadot {: #integration-with-other-polkadot-based-chains } 

Le scénario initial qui nous intéresse le plus est de permettre le mouvement de jetons d'autres chaînes vers des jetons basés sur Moonbeam, de sorte qu'ils puissent être utilisés dans DeFi et d'autres applications sur la plate-forme. Une fois leur travail terminé, ces actifs peuvent ensuite revenir ou quitter d'autres chaînes.

À mesure que les fonctionnalités d'intégration du réseau Polkadot évoluent, nous continuerons à fournir aux développeurs des moyens d'accéder à ces intégrations à partir de contrats intelligents et de composer des fonctionnalités à travers les chaînes dans les contrats intelligents Moonbeam.

## Intégration avec Ethereum {: #integration-with-ethereum } 

La connectivité à Ethereum est une capacité importante nécessaire pour que Moonbeam puisse prendre en charge les projets basés sur Ethereum, en particulier dans les déploiements hybrides où les projets sont simultanément déployés sur Ethereum et Moonbeam. Il y a au moins un projet en cours de développement, indépendant de Moonbeam, pour construire un pont Ethereum basé sur la parachain. Une fois que ce pont sera opérationnel, il fournira un mécanisme pour déplacer les jetons, l'état et les messages vers et depuis Ethereum en tirant parti de Polkadot.

Jusqu'à ce qu'il y ait un pont basé sur la parachain en production, nous prévoyons de fournir deux solutions pour les projets qui souhaitent intégrer Ethereum et Moonbeam:

 1. Un utilitaire qui peut exporter l'état d'Ethereum dans un fichier binaire, où ce fichier binaire peut être utilisé pour importer cet état dans Moonbeam. Chaque utilisation de cet utilitaire serait une migration unidirectionnelle unique.
 2. Un pont Ethereum point à point directement intégré à Moonbeam. Ce pont permettrait le mouvement des jetons et les requêtes et messages d'état inter-chaînes. Au fur et à mesure que l'écosystème Polkadot se développe, nous nous attendons à plusieurs options d'intégration Ethereum comme choix pour les projets se déployant sur Moonbeam.
