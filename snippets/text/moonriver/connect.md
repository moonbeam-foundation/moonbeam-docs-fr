
Les points de terminaison Moonriver RPC et WSS hébergés par PureStake sont uniquement destinés au développement et ne doivent pas être utilisés dans des applications de production. Les fournisseurs suivants sont des fournisseurs alternatifs de points de terminaison :

- [OnFinality](https://onfinality.io/)
- [Elara](https://elara.patract.io/)

### HTTPS DNS {: #https-dns } 

Pour se connecter à Moonriver via HTTPS, il suffit de faire pointer votre fournisseur vers l'un des DNS RPC suivants :

=== "PureStake"
    ```
    https://rpc.moonriver.moonbeam.network
    ```

=== "OnFinality"
    ```
    https://moonriver.api.onfinality.io/public
    ```

Pour la bibliothèque web3.js, vous pouvez créer une instance locale de Web3 et définir le fournisseur pour se connecter à Moonriver (HTTP et WS sont pris en charge) :

```js
const Web3 = require('web3'); //Charger la bibliothèque Web3
.
.
.
//Créer une instance Web3 locale - définir Moonriver comme fournisseur
const web3 = new Web3("https://rpc.moonriver.moonbeam.network"); 
```
For the ethers.js library, define the provider by using `ethers.providers.StaticJsonRpcProvider(providerURL, {object})` and setting the provider URL to Moonriver:

```js
const ethers = require('ethers');


const providerURL = "https://rpc.moonriver.moonbeam.network";
// Définir le fournisseur
const provider = new ethers.providers.StaticJsonRpcProvider(providerURL, {
    chainId: 1285,
    name: 'moonriver'
});
```

Tout porte-monnaie Ethereum devrait pouvoir générer une adresse valide pour Moonbeam (par exemple, [MetaMask](https://metamask.io/)).

### WSS DNS {: #wss-dns } 

Pour les connexions WebSocket, vous pouvez utiliser le DNS suivant :

=== "PureStake"
    ```
    wss://wss.moonriver.moonbeam.network
    ```

=== "OnFinality"
    ```
    wss://moonriver.api.onfinality.io/public-ws
    ```

=== "Elara"
    ```
    wss://moonriver.kusama.elara.patract.io
    ```

### Chain ID {: #chain-id } 

ID de chaîne Moonriver est: `1285`
