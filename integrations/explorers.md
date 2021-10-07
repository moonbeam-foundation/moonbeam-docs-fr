---
title: Explorateurs de blocs
description: Un aperçu des explorateurs de blocs actuellement disponibles qui peuvent être utilisés pour naviguer dans les couches Substrate et Ethereum du TestNet Moonbeam .
---
# Explorateurs de blocs

![Explorer Banner](/images/builders/tools/explorers/explorers-banner.png)

## Introduction {: #introduction } 

Les explorateurs de blocs peuvent être considérés comme des moteurs de recherche pour la blockchain. Ils permettent aux utilisateurs de rechercher des informations telles que les soldes, les contrats et les transactions. Les explorateurs de blocs plus avancés offrent même des capacités d'indexation, qui leur permettent de fournir un ensemble complet d'informations, telles que les jetons ERC20 dans le réseau. Ils pourraient même proposer des services API pour y accéder via des services externes

Moonbeam fournit deux ensembles d'explorateurs différents: l'un pour interroger l'API Ethereum et l'autre pour l'API Substrate.

!!! remarque
    Si vous utilisez le navigateur Brave et que votre explorateur ne se connecte pas à l'instance Moonbeam vers laquelle vous le pointez, essayez de désactiver Brave Shield.

## API Ethereum {: #ethereum-api } 

### Expedition (nœud de développement - TestNet) {: #expedition-dev-node-testnet } 

Une version sur le thème de Moonbeam de l' explorateur d' [Expedition](https://github.com/etclabscore/expedition) peut être trouvée dans [ce lien](https://moonbeam-explorer.netlify.app/).

Par défaut, l'explorateur est connecté au Moonbase Alpha TestNet. Cependant, vous pouvez le connecter en suivant les étapes suivantes:

 1. Cliquez sur l'icône d'engrenage dans le coin supérieur droit
 2. Sélectionnez "Developpement" si vous avez un nœud en cours d'exécution sur `http://localhost:9933` (emplacement RPC par défaut d'un nœud Moonbeam fonctionnant avec l’indicateur `--dev`). Vous pouvez également revenir à "Moonbase Alpha"
 3. Dans le cas où vous souhaitez vous connecter à une URL RPC spécifique, sélectionnez "RPC personnalisé" et saisissez l'URL. Par exemple, `http://localhost:9937`

![Expedition Explorer](/images/builders/tools/explorers/explorers-1.png)

### Blockscout (TestNet) {: #blockscout-testnet } 

Blockscout fournit une interface facile à utiliser permettant aux utilisateurs d'inspecter et de confirmer les transactions sur les blockchains EVM, y compris Moonbeam. Il vous permet de rechercher des transactions, d'afficher les comptes et les soldes, et de vérifier les contrats intelligents. Plus d'informations peuvent être trouvées sur leur [site de documentation](https://docs.blockscout.com/).

Comme fonctionnalités principales, Blockscout propose:

 - Développement open source, ce qui signifie que tout le code est ouvert à la communauté pour l'explorer et l'améliorer. Vous pouvez trouver le code [ici](https://github.com/blockscout/blockscout)
 - Suivi des transactions en temps réel
 - Interaction de contrat intelligent
 - Prise en charge des jetons ERC20 et ERC721, répertoriant tous les contrats de jetons disponibles dans une interface conviviale
 -API complète avec GraphQL, où les utilisateurs peuvent tester les appels d'API directement à partir d'une interface Web

Une instance de Blockscout fonctionnant sur Moonbase Alpha TestNet peut être trouvée dans [ce lien](https://moonbase-blockscout.testnet.moonbeam.network/).

![Blockscout Explorer](/images/builders/tools/explorers/explorers-2.png)

## API Substrate {: #substrate-api }

### PolkadotJS (nœud de développement - TestNet) {: #polkadotjs-dev-node-testnet } 

Polkadot JS Apps utilise le point de terminaison WebSocket pour interagir avec le réseau. Pour le connecter à un nœud de développement Moonbeam, vous pouvez suivre les étapes de [ce tutoriel](/getting-started/local-node/setting-up-a-node/#connecting-polkadot-js-apps-to-a-local-moonbeam-node). Le port par défaut pour cela est `9944`.

![Polkadot JS Local Node](/images/builders/tools/explorers/explorers-3.png)

Pour afficher et interagir avec la couche substrate de Moonbase Alpha, accédez à [cette URL](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/explorer). Il s'agit de Polkadot JS Apps pointant vers le TestNet. Vous pouvez trouver plus d'informations sur [cette page](/integrations/wallets/polkadotjs/).

![Polkadot JS Moonbase Alpha](/images/builders/tools/explorers/explorers-4.png)

### Subscan {: #subscan } 

Subscan fournit des capacités d'explorateur de chaînes de blocs pour les chaînes basées sur substrate. Il est capable d'analyser les modules standards ou personnalisés. Par exemple, cela est utile pour afficher des informations concernant les palettes (ou modules) de Staking, Governance et EVM. Le code est entièrement open-source et peut être trouvé [ici](https://github.com/itering/subscan-essentials).

Une instance de Subscan fonctionnant sur Moonbase Alpha TestNet peut être trouvée dans [ce lien](https://moonbase.subscan.io/).

![Subscan Moonbase Alpha](/images/builders/tools/explorers/explorers-5.png)
