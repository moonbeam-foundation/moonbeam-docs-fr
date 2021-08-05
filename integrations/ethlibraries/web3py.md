---
title: Web3.py
description: Suivez ce tutoriel pour apprendre à utiliser la bibliothèque Python Ethereum Web3 pour déployer des contrats intelligents Solidity sur Moonbeam.
---
# Bibliothèque Python Web3.py
![Intro diagram](/images/integrations/integrations-web3py-banner.png)

## Introduction {: #introduction } 

[Web3.py](https://web3py.readthedocs.io/) est un ensemble de bibliothèques qui permettent aux développeurs d'interagir avec les nœuds Ethereum à l'aide des protocoles HTTP, IPC ou WebSocket avec Python. Moonbeam dispose d'une API de type Ethereum entièrement compatible avec les invocations JSON RPC de style Ethereum. Par conséquent, les développeurs peuvent tirer parti de cette compatibilité et utiliser la bibliothèque web3.py pour interagir avec un nœud Moonbeam comme s'ils le faisaient sur Ethereum.

## Configurer Web3.py avec Moonbeam {: #setup-web3py-with-moonbeam } 

Pour démarrer avec la bibliothèque web3.py, installez-la à l'aide de la commande suivante:

```
pip3 install web3
```

Une fois terminé, la configuration la plus simple pour commencer à utiliser la bibliothèque et ses méthodes est la suivante:

```py
from web3 import Web3

web3 = Web3(Web3.HTTPProvider('RPC_URL'))
```

En fonction du réseau auquel vous souhaitez vous connecter, vous pouvez définir les valeurs `RPC_URL` suivantes:

 - Nœud de développement Moonbeam: `http://127.0.0.1:9933`
 - TestNet Moonbase Alpha: `https://rpc.testnet.moonbeam.network`
 - Moonriver: `{{ networks.moonriver.rpc_url }}`
## Tutoriels étape par étape {: #step-by-step-tutorials } 

Si vous êtes intéressé par un guide étape par étape plus détaillé, consultez nos tutoriels spécifiques sur l'utilisation de web3.py sur Moonbeam pour  [envoyer une transaction](/getting-started/local-node/send-transaction/) ou [deployer un contrat](/getting-started/local-node/deploy-contract/).

