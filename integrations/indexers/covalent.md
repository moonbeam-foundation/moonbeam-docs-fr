---
title: L'API Covalent
description: Interrogation des données Blockchain Moonbeam avec l'API Covalent
---

# Premiers pas avec l'API Covalent

![The Graph sur Moonbeam](/images/covalent/covalentbannerimage.png)

## Introduction {: #introduction } 

Covalent fournit une API unifiée pour apporter une transparence et une visibilité totales aux actifs sur tous les réseaux blockchain. En termes simples, Covalent propose une API unique qui vous permet d'extraire des données de transaction de blockchain détaillées et granulaires à partir de plusieurs blockchains sans code. L'API Covalent unifiée vous permet de créer des applications entièrement nouvelles ou d'augmenter celles existantes sans configurer ni entretenir l'infrastructure. Covalent prend en charge Moonbase Alpha et prévoit de prendre en charge Moonbeam et Moonriver.

## Vérification des prérequis {: #checking-prerequisites } 

Toutes les demandes nécessitent une authentification ; vous aurez besoin d'[une clé API gratuite](https://www.covalenthq.com/platform/#/auth/register/) pour utiliser l'API Covalent.
Vous aurez également besoin des éléments suivants :

 - Avoir MetaMask installé et [connecté à Moonbase](/getting-started/moonbase/metamask/)
 - Avoir un compte avec des fonds, que vous pouvez obtenir à partir de [Mission Control](/getting-started/moonbase/faucet/)

## Types de points de terminaison {: #types-of-endpoints } 

L'API Covalent a deux classes de points de terminaison :

 - **Classe A** - points de terminaison qui renvoient des données de blockchain enrichies applicables à tous les réseaux de blockchain, par exemple : soldes, transactions, événements de journal, etc.
 - **Classe B** — points de terminaison qui sont destinés à un protocole spécifique sur une blockchain, par ex. Uniswap est uniquement Ethereum et ne s'applique pas aux autres réseaux blockchain

## Principes fondamentaux de l'API Covalent {: #fundamentals-of-the-covalent-api } 
 - L'API Covalent est RESTful et elle est conçue autour des principales ressources disponibles via l'interface Web
 - La version actuelle de l'API est la version 1
 - Le format de retour par défaut pour tous les points de terminaison est JSON
 - Toutes les demandes nécessitent une authentification ; vous aurez besoin d'[une clé API gratuite](https://www.covalenthq.com/platform/#/auth/register/) pour utiliser l'API Covalent
 - L'URL racine de l'API est https://api.covalenthq.com/v1/
 - Toutes les requêtes sont effectuées via HTTPS (les appels via HTTP simple échoueront)
 - Le taux de rafraîchissement des APIs est en temps réel : 30s ou 2 blocs, et batch 10m ou 40 blocs  

## Points de terminaison pris en charge {: #supported-endpoints } 
 - **Soldes** : obtenez des soldes de jetons pour une adresse. Renvoie une liste de tous les soldes de jetons ERC20 et NFT, y compris ERC721 et ERC1155, ainsi que leurs prix au comptant actuels (si disponibles)
 - **Transactions** : récupère toutes les transactions pour une adresse, y compris les événements de journal décodés. Fait un crawl profond de la blockchain pour
 récupérer toutes les transactions qui font référence à cette adresse
 - **Transferts** — Obtenez des transferts de jetons ERC20 pour une adresse ainsi que les prix des jetons historiques (si disponibles)
 - **Détenteurs de jetons** — Renvoie une liste paginée des détenteurs de jetons
 - **Événements de journal (contrat intelligent)** : renvoie une liste paginée des événements de journal décodés émis par un contrat intelligent particulier
 - **Événements de journal (Topic Hash)** — Renvoie une liste paginée d'événements de journal décodés avec un ou plusieurs hachages de sujet séparés par une virgule


### Demande de formatage {: #request-formatting }
   | Point final | | Formater |
   | :---------- | :-: | :------------------- |
   | Soldes | | api.covalenthq.com/v1/1287/address/{address}/balances_v2/ |
   | Opérations | | api.covalenthq.com/v1/1287/address/{address}/transactions_v2/|
   | Transferts | | api.covalenthq.com/v1/1287/address/{address}/transfers_v2/ |
   | Détenteurs de jetons | | api.covalenthq.com/v1/1287/tokens/{contract_address}/token_holders/ |
   | Enregistrer les événements (contrat intelligent) | | api.covalenthq.com/v1/1287/events/address/{contract_address}/ |
   | Événements de journal (Hash de sujet) | | api.covalenthq.com/v1/1287/events/topics/{topic}/ |

## Essaye le {: #try-it-out } 
Tout d'abord, assurez-vous d'avoir [votre clé API](https://www.covalenthq.com/platform/#/auth/register/) qui commence par "ckey_". Le point de terminaison des détenteurs de jetons renvoie une liste de tous les détenteurs de jetons d'un jeton particulier. Pour cet appel d'API, vous aurez besoin des éléments suivants :

 - Votre clé API
 - ID de la chaîne Alpha de la base lunaire : 1287
 - Adresse du contrat (jeton ERTH dans cet exemple) : 0x08B40414525687731C23F430CEBb424b332b3d35

### Utilisation de Curl {: #using-curl } 
Essayez d'exécuter la commande ci-dessous dans une fenêtre de terminal après avoir remplacé l'espace réservé par votre clé API.

```
curl https://api.covalenthq.com/v1/1287/tokens/\
0x08B40414525687731C23F430CEBb424b332b3d35/token_holders/ \
-u {YOUR API KEY HERE}:
```
!!! remarque
     Les deux points `:` après la clé API sont importants car sinon vous serez invité à entrer un mot de passe (qui n'est pas nécessaire).


L'API Covalent renverra une liste de détenteurs de jetons pour le jeton ERTH. À moins que vous ne possédiez déjà des jetons ERTH, votre adresse sera absente de cette liste. Rendez-vous sur le [Moonbase Alpha ERC-20 Faucet] (https://moonbase-minterc20.netlify.app/) pour générer vous-même des jetons ERTH. Répétez maintenant la même demande d'API Covalent que ci-dessus. L'API Covalent se met à jour en temps réel, vous devriez donc maintenant voir votre adresse dans la liste des détenteurs de jetons pour le jeton ERTH.

## Exemples Javascript {: #javascript-examples } 
Copiez et collez le bloc de code ci-dessous dans votre environnement préféré, ou [JSFiddle](https://jsfiddle.net/). Après avoir défini la clé API, définissez la constante d'adresse. N'oubliez pas que notre ID de chaîne est « 1287 » pour Moonbase Alpha.

=== "Utiliser Récupérer"
    ```js
    // set your API key
	const APIKEY = 'YOUR API KEY HERE';

	function getData() {
    const address = '0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8'; //example
    const chainId = '1287'; //Moonbeam TestNet (Moonbase Alpha Chain ID)
    const url = new URL(`https://api.covalenthq.com/v1/${chainId}/address/${address}/balances_v2/`);
    
    url.search = new URLSearchParams({
        key: APIKEY
    })

    // use fetch API to get Covalent data
    fetch(url)
    .then((resp) => resp.json())
    .then(function(data) {
        const result = data.data;
  
        console.log(result)
        return result
        }
	)}

    getData();
    ```

=== "Using Async"
    ```js
    // set your API key
    const APIKEY = 'YOUR API KEY HERE';
	const address = '0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8'; //example
	const chainId = '1287'; //Moonbeam TestNet (Moonbase Alpha Chain ID)
	const url = new URL(`https://api.covalenthq.com/v1/${chainId}/address/${address}/balances_v2/`);

    url.search = new URLSearchParams({
        key: APIKEY
    })

    async function getData() {
    	const response = await fetch(url);
    	const result = await response.json();
    	console.log(result)
    	return result;
	}

	getData();

    ```

The output should resemble the below. The balances endpoint returns a list of all ERC20 and NFT token balances including ERC721 and ERC1155 along with their current spot prices (if available).

![Javascript Console Output](/images/covalent/covalentjs.png)

## Exemple Python {: #python-example } 
Covalent n'a pas de wrapper API officiel. Pour interroger directement l'API, vous devrez utiliser la [bibliothèque de requêtes] Python (https://pypi.org/project/requests/). Installez les requêtes dans votre environnement à partir de la ligne de commande avec `pip install request`. Ensuite, importez-le et utilisez-le dans votre code. Utilisez les méthodes get des verbes HTTP pour renvoyer les informations de l'API. Copiez et collez le bloc de code ci-dessous dans votre environnement préféré et exécutez-le. La sortie devrait ressembler à la capture d'écran ci-dessus, mais le formatage peut varier en fonction de votre environnement.

```python
import requests

def fetch_wallet_balance(address):
	api_url = 'https://api.covalenthq.com'
    endpoint = f'/v1/1287/address/{address}/balances_v2/'
    url = api_url + endpoint
    r = requests.get(url, auth=('YOUR API KEY HERE',''))
    print(r.json())
    return(r.json())

#Example address request
fetch_wallet_balance('0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8')

```

!!! remarque
     Le 2ème paramètre de `auth` est vide, car aucun mot de passe n'est requis - votre clé API est tout ce dont vous avez besoin.

### Bibliothèques communautaires {: #community-built-libraries } 
Covalent possède actuellement des bibliothèques en Python, Node et Go, qui sont construites et maintenues par la communauté dans le cadre du [Covalent Alchemists Program] (https://www.covalenthq.com/ambassador/). Les outils ont été créés par la communauté pour apporter de la valeur aux utilisateurs de l'API Covalent et sont [disponibles ici] (https://www.covalenthq.com/docs/tools/community).

!!! remarque
     Remarque : Ces outils ne sont PAS maintenus par Covalent et les utilisateurs doivent faire preuve de diligence raisonnable en évaluant ces outils avant de les utiliser dans leurs projets.
