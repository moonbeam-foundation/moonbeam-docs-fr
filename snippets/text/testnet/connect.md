### HTTPS DNS {: #https-dns } 

Pour vous connecter à Moonbase Alpha via HTTPS, pointez simplement votre fournisseur vers le DNS RPC suivant :

```
https://rpc.testnet.moonbeam.network
```

Pour la bibliothèque web3.js, vous pouvez créer une instance Web3 locale et configurer le fournisseur pour qu'il se connecte à Moonbase Alpha (HTTP et WS sont pris en charge):

```js
const Web3 = require('web3'); //Load Web3 library
.
.
.
//Create local Web3 instance - set Moonbase Alpha as provider
const web3 = new Web3('https://rpc.testnet.moonbeam.network'); 
```
For the ethers.js library, define the provider by using `ethers.providers.StaticJsonRpcProvider(providerURL, {object})` and setting the provider URL to Moonbase Alpha:

```js
const ethers = require('ethers');


const providerURL = 'https://rpc.testnet.moonbeam.network';
// Define Provider
const provider = new ethers.providers.StaticJsonRpcProvider(providerURL, {
    chainId: 1287,
    name: 'moonbase-alphanet'
});
```

Tout portefeuille Ethereum devrait pouvoir générer une adresse valide pour Moonbeam (par exemple, [MetaMask](https://metamask.io/)).

### WSS DNS {: #wss-dns } 

Pour les connexions WebSocket, vous pouvez utiliser le DNS suivant:

```
wss://wss.testnet.moonbeam.network
```

### ID de Chaine {: #chain-id } 

Pour le TestNet Moonbase Alpha , l'ID de la chaîne est: `1287`.
