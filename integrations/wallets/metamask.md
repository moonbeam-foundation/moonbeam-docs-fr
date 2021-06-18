---
title: MetaMask
description: Ce guide vous explique comment connecter MetaMask, un portefeuille Ethereum basé sur un navigateur, à Moonbeam.
---

# Interagir avec Moonbeam à l'aide de MetaMask

![Intro diagram](/images/integrations/integrations-metamask-banner.png)

## Introduction

Les développeurs peuvent tirer parti des fonctionnalités de compatibilité Ethereum de Moonbeam pour intégrer des outils, tels que [MetaMask](https://metamask.io/), dans leurs DApp. Ce faisant, ils peuvent utiliser la bibliothèque injectée fournie par MetaMask pour interagir avec la blockchain.

Actuellement, MetaMask peut être configuré pour se connecter à deux réseaux: un nœud de développement Moonbeam ou le Testnet Moonbase Alpha.

Si vous avez déjà installé MetaMask, vous pouvez facilement connecter MetaMask au TestNet Moonbase Alpha :

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask">Connect MetaMask</a>
</div>

!!! note
    MetaMask apparaîtra pour demander la permission d'ajouter Moonbase Alpha en tant que réseau personnalisé. Une fois que vous avez approuvé les autorisations, MetaMask basculera votre réseau actuel vers Moonbase Alpha.

Apprenez [comment intégrer un bouton Connecter MetaMask](#integrate-metamask-into-a-dapp) dans votre dapp, afin que les utilisateurs puissent se connecter à Moonbase Alpha d'un simple clic sur un bouton.

## Connectez MetaMask à Moonbeam

Une fois [MetaMask](https://metamask.io/) installé, vous pouvez le connecter à Moonbeam en cliquant sur le logo en haut à droite et en ouvrant les paramètres.

![MetaMask3](/images/testnet/testnet-metamask3.png)

Ensuite, accédez à l'onglet Réseaux et cliquez sur le bouton "Ajouter un réseau" .

![MetaMask4](/images/testnet/testnet-metamask4.png)

Ici, vous pouvez configurer MetaMask pour les réseaux suivants:

Nœud de développement Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

TestNet Moonbase Alpha :

--8<-- 'text/testnet/testnet-details.md'

## Tutoriels étape par étape

Dans le cas où vous seriez intéressé par un guide pas à pas plus détaillé, vous pouvez accéder à nos tutoriels spécifiques:

 - MetaMask sur un [nœud de développement Moonbeam](/getting-started/local-node/using-metamask/)
 - MetaMask sur [Moonbase Alpha](/getting-started/testnet/metamask/)

## Intégrez MetaMask dans une DApp

Avec la sortie des [API de réseaux personnalisés MetaMask](https://consensys.net/blog/metamask/connect-users-to-layer-2-networks-with-the-metamask-custom-networks-api/), les utilisateurs peuvent être invités à ajouter le testnet de Moonbeam, Moonbase Alpha. 

Cette section vous guidera tout au long du processus d'ajout d'un bouton "Connectez-vous à Moonbase Alpha" qui invitera les utilisateurs à connecter leur(s) compte(s) MetaMask à Moonbase Alpha. Vos utilisateurs n'auront plus besoin de connaître ou de s'inquiéter des configurations réseau de Moonbase Alpha et de l'ajout d'un réseau personnalisé à MetaMask. Pour interagir avec Moonbeam depuis votre dApp, il suffit aux utilisateurs de cliquer sur quelques boutons pour se connecter à Moonbase Alpha et commencer.

MetaMask injecte une API Ethereum globale dans les sites Web que les utilisateurs visitent sur `window.ethereum`, qui permet aux sites Web de lire et de demander les données blockchain des utilisateurs. Vous utiliserez le fournisseur Ethereum pour guider vos utilisateurs tout au long du processus d'ajout de Moonbase Alpha en tant que réseau personnalisé. En général, vous devrez :

- Vérifiez si le fournisseur Ethereum existe et s'il s'agit de MetaMask
- Demander l'adresse du compte de l'utilisateur
- Ajouter Moonbase Alpha comme nouvelle chaîne

Ce guide est divisé en deux sections. Tout d'abord, il couvrira l'ajout d'un bouton qui sera utilisé pour déclencher l'affichage et la connexion de MetaMask à Moonbase Alpha. La deuxième partie du guide créera la logique pour connecter l'utilisateur à MetaMask. De cette façon, lorsque vous cliquez sur le bouton, vous pouvez réellement tester la fonctionnalité au fur et à mesure que vous parcourez le guide.

### Vérification des prérequis

Pour ajouter le bouton Connect MetaMask, vous aurez besoin d'un projet JavaScript et de l'extension de navigateur MetaMask installée pour les tests locaux.

Il est recommandé d'utiliser le package utilitaire  `detect-provider` de MetaMask pour détecter le fournisseur injecté à `window.ethereum`. Le package gère la détection du fournisseur de l'extension MetaMask et de MetaMask Mobile. Pour installer le package dans votre projet JavaScript, exécutez :

```
npm install @metamask/detect-provider
```
### Ajouter un bouton

Vous commencerez par ajouter un bouton qui sera utilisé pour connecter MetaMask à Moonbase Alpha. Vous voulez commencer par le bouton. Ainsi, lorsque vous créez la logique à l'étape suivante, vous pouvez tester le code au fur et à mesure que vous parcourez le guide. 

La fonction que nous allons créer dans la section suivante du guide s'appellera `configureMoonbaseAlpha`. Donc le bouton au clic devrait appeler `configureMoonbaseAlpha`.

```html
<button onClick={configureMoonbaseAlpha()}>Connect to Moonbase Alpha</button>
```

### Ajouter une logique

Maintenant que vous avez créé le bouton, vous devez ajouter la fonction `configureMoonbaseAlpha` qui sera utilisé au clic. 

1. Détectez le fournisseur à  `window.ethereum` et vérifiez si c'est MetaMask. Si vous voulez une solution simple vous pouvez accéder directement à `window.ethereum`. Ou vous pouvez utiliser le package `detect-provider` de MetaMask  et il détectera pour vous le fournisseur de l'extension MetaMask et de MetaMask Mobile.
```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        // Logic will go here    
    } else {
        console.error("Please install MetaMask");
    }
}
```

2. Demandez les comptes de l'utilisateur en appelant la méthode `eth_requestAccounts` . Cela invitera MetaMask à s'ouvrir et demandera à l'utilisateur de sélectionner les comptes auxquels il souhaite se connecter. Dans les coulisses, les autorisations sont vérifiées en appelant `wallet_requestPermissions`. Actuellement, les seules autorisations sont pour `eth_accounts`. Vous vérifiez donc en fin de compte que vous avez accès aux adresses de l'utilisateur renvoyées par `eth_accounts`. Si vous souhaitez en savoir plus sur le système d'autorisations, consultez [EIP-2255](https://eips.ethereum.org/EIPS/eip-2255).
```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        try {
            await provider.request({ method: "eth_requestAccounts"});
        } catch(e) {
            console.error(e);
        }  
    } else {
        console.error("Please install MetaMask");
    }
}
```
<img src="/images/integrations/integrations-metamask-1.png" alt="Integrate MetaMask into a Dapp - Select account" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>
<img src="/images/integrations/integrations-metamask-2.png" alt="Integrate MetaMask into a Dapp - Connect account" style="width: 50%; display: block; margin-left: auto; margin-right: auto;" />

3. Ajoutez Moonbase Alpha en tant que nouvelle chaîne en appelant `wallet_addEthereumChain`. Cela invitera l'utilisateur à fournir l'autorisation d'ajouter Moonbase Alpha en tant que réseau personnalisé. 
```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        try {
            await provider.request({ method: "eth_requestAccounts"});
            await provider.request({
                method: "wallet_addEthereumChain",
                params: [
                    {
                        chainId: "0x507", // Moonbase Alpha's chainId is 1287, which is 0x507 in hex
                        chainName: "Moonbase Alpha",
                        nativeCurrency: {
                            name: 'DEV',
                            symbol: 'DEV',
                            decimals: 18
                        },
                       rpcUrls: ["https://rpc.testnet.moonbeam.network"],
                       blockExplorerUrls: ["https://moonbase-blockscout.testnet.moonbeam.network/"]
                    },
                ]
            })
        } catch(e) {
            console.error(e);
        }  
    } else {
        console.error("Please install MetaMask");
    }
}
```

<img src="/images/integrations/integrations-metamask-3.png" alt="Integrate MetaMask into a Dapp - Add network" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>

Une fois le réseau ajouté avec succès, il invitera également l'utilisateur à passer ensuite à Moonbase Alpha.

<img src="/images/integrations/integrations-metamask-4.png" alt="Integrate MetaMask into a Dapp - Switch to network" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>

Ainsi, vous devriez maintenant avoir un bouton qui, au clic, guide les utilisateurs tout au long du processus de connexion de leurs comptes MetaMask à Moonbase Alpha. 

<img src="/images/integrations/integrations-metamask-5.png" alt="Integrate MetaMask into a Dapp - Account connected to Moonbase Alpha"/>

### Confirmer la connexion

Il est possible que vous ayez une logique qui repose sur le fait de savoir si un utilisateur est connecté ou non à Moonbase Alpha. Vous souhaitez peut-être désactiver le bouton si l'utilisateur est déjà connecté. Pour confirmer qu'un utilisateur est connecté à Moonbase Alpha, vous pouvez appeler `eth_chainId`, qui renverra l'ID de chaîne actuel de l'utilisateur:

```javascript
    const chainId = await provider.request({
        method: 'eth_chainId'
    })
    // Moonbase Alpha's chainId is 1287, which is 0x507 in hex
    if (chainId === "0x507"){
        // At this point, you might want to disable the "Connect" button
        // or inform the user that they are already connected to the
        // Moonbase Alpha testnet
    }
```

### Écoutez les changements de compte

Pour vous assurer que votre projet ou dApp reste à jour avec les dernières informations de compte, vous pouvez ajouter l'écouteur d'événement `accountsChanged` fourni par Metamask. MetaMask émet cet événement lorsque la valeur de retour de `eth_accounts` change. Si une adresse est renvoyée, c'est le compte le plus récent de votre utilisateur qui a fourni les autorisations d'accès. Si aucune adresse n'est renvoyée, cela signifie que l'utilisateur n'a fourni aucun compte avec des autorisations d'accès.

```javascript
    provider.on("accountsChanged", (accounts) => {
        if (accounts.length === 0) {
            // MetaMask is locked or the user doesn't have any connected accounts
            console.log('Please connect to MetaMask.');
        } 
    })
```

### Écoutez les changements de chaîne

Pour maintenir votre projet ou dApp à jour avec toute modification apportée à la chaîne connectée, vous devrez vous abonner à l'événement `chainChanged` . MetaMask émet cet événement chaque fois que la chaîne connectée change.

```javascript
    provider.on("chainChanged", () => {
        // MetaMask recommends reloading the page unless you have good reason not to
        window.location.reload();
    })
```

MetaMask recommande de recharger la page chaque fois que la chaîne change, à moins qu'il n'y ait une bonne raison de ne pas le faire, car il est important de toujours être synchronisé avec les changements de chaîne.
