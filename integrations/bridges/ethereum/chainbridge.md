---
title: ChainBridge
description: Comment utiliser ChainBridge pour connecter des actifs entre Ethereum et Moonbeam à l'aide de contrats intelligents
---
# Pont Ethereum Moonbeam de ChainBridge

![ChainBridge Moonbeam banner](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-banner.png)

## Introduction {: #introduction } 

Un pont permet à deux chaînes économiquement souveraines et technologiquement différentes de communiquer entre elles. Ils peuvent aller de centralisés et fiables à décentralisés et de confiance limitée. L'une des solutions actuellement disponibles est [ChainBridge](https://github.com/ChainSafe/ChainBridge#installation), un pont blockchain multidirectionnel modulaire construit par [ChainSafe](https://chainsafe.io/). Une implémentation ChainBridge est maintenant disponible dans Moonbeam, qui connecte notre Moonbase Alpha TestNet et les Kovan / Rinkeby TestNets d'Ethereum.

Ce guide est divisé en deux sections principales. Dans la première partie, nous expliquerons le flux de travail général du pont. Dans la deuxième partie, nous allons passer en revue quelques exemples utilisant le pont pour transférer les actifs ERC-20 et ERC-721 entre Moonbase Alpha et Kovan / Rinkeby. 

 - [Comment fonctionne le pont](/integrations/bridges/ethereum/chainbridge/#how-the-bridge-works)
    - [Définitions générales](/integrations/bridges/ethereum/chainbridge/#general-definitions)
 - [Utilisation du pont dans Moonbase Alpha](/integrations/bridges/ethereum/chainbridge/#try-it-on-moonbase-alpha)
    - [Transférer des jetons ERC-20](/integrations/bridges/ethereum/chainbridge/#erc-20-token-transfer)
    - [Transférer des jetons ERC-721](/integrations/bridges/ethereum/chainbridge/#erc-721-token-transfer)
    - [Gestionnaire générique](/integrations/bridges/ethereum/chainbridge/#generic-handler)
    
## Comment fonctionne le pont {: #how-the-bridge-works } 

ChainBridge est, à la base, un protocole de transmission de messages. Les événements sur une chaîne source sont utilisés pour envoyer un message qui est acheminé vers la chaîne de destination. Il y a trois rôles principaux:

 - Listener: extraire des événements d'une chaîne source et construire un message
 - Router: transmet le message du Listener au Writer
 - Writer: interpréter les messages et soumettre les transactions à la chaîne de destination
 
ChainBridge s'appuie actuellement sur des relais de confiance pour exécuter ces rôles. Cependant, il dispose d'un mécanisme qui empêche tout relais individuel d'abuser de son pouvoir et de mal gérer les fonds. À un niveau élevé, les relais créent des propositions dans la chaîne cible qui sont soumises à l'approbation d'autres relais. Le vote d'approbation se produit également dans la chaîne cible, et chaque proposition n'est exécutée qu'après avoir atteint un certain seuil de vote. 

Des deux côtés du pont, il y a un ensemble de contrats intelligents, où chacun a une fonction spécifique:

 - **Contrat de pont** — les utilisateurs et les relais interagissent avec ce contrat. Il délègue les appels au gestionnaire de contrats pour les dépôts, démarre une transaction sur la chaîne source et pour les exécutions des propositions sur la chaîne cible
 - **Contrats du gestionnaire** — valide les paramètres fournis par l'utilisateur, créant un enregistrement de dépôt / exécution
 - **Contrat cible** — comme son nom l'indique, c'est le contrat avec lequel nous allons interagir de chaque côté du pont

### Flux de travail général {: #general-workflow } 


Le flux de travail général est le suivant (de la chaîne A à la chaîne B):
 
  - Un utilisateur lance une transaction avec la fonction _deposit()_ dans le contrat de pont de la chaîne A. Ici, l'utilisateur doit saisir la chaîne cible, l'ID de ressource et les données d'appel _calldata_ (définitions après le diagramme). Après quelques vérifications, la fonction _deposit()_  du contrat handler est appelée, qui exécute l'appel correspondant du contrat cible.
  - Une fois la fonction du contrat cible dans la chaîne A exécutée, un événement de  _Depot_ est émis par le contrat de pont, qui contient les données nécessaires à exécuter sur la chaîne B. C'est ce qu'on appelle une proposition. Chaque proposition peut avoir cinq statuts (inactif, actif, accepté, exécuté et annulé) 
  - Les relais écoutent toujours des deux côtés de la chaîne. Une fois qu'un relais a détecté l'événement, il lance un vote sur la proposition, qui se produit sur le contrat de pont sur la chaîne B. Cela définit l'état de la proposition de inactif à actif.
  - Les relais doivent voter sur la proposition. Chaque fois qu'un relais vote, un événement est émis par le contrat de pont qui met à jour son statut. Une fois qu'un seuil est atteint, l'état passe d'actif à accepté. Un relais exécute alors la proposition sur la chaîne B via le contrat de pont.
  - Après quelques vérifications, le pont exécute la proposition dans le contrat cible via le contrat gestionnaire sur la chaîne B. Un autre événement est émis, qui met à jour le statut de la proposition de accepté à exécuté.

Ce flux de travail est résumé dans le diagramme suivant:

![ChainBridge Moonbeam diagram](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-diagram.png)

Les deux contrats cibles de chaque côté du pont sont liés en effectuant une série d'enregistrements dans le gestionnaire de contrat correspondant via le contrat de pont. Ces enregistrements ne peuvent actuellement être effectués que par l'administrateur du contrat de pont.

### Définitions générales {: #general-definitions } 

Ici, nous avons rassemblé une liste de concepts applicables à l'implémentation ChainBridge (de la chaîne A à la chaîne B):

 - **ChainBridge Chain ID** — il ne doit pas être confondu avec l'ID de chaîne du réseau. Il s'agit d'un identifiant de réseau unique utilisé par le protocole pour chaque chaîne. Il peut être différent de l'ID de chaîne réel du réseau lui-même. Par exemple, pour Moonbase Alpha et Rinkeby, nous avons défini l'ID de chaîne ChainBridge sur 43 et 4 respectivement (Kovan a été défini sur 42)
 - **ID de ressource** — est un mot de 32 octets destiné à identifier de manière unique un actif dans un environnement inter-chaînes. Notez que l'octet le moins significatif est réservé à chainId, donc nous aurions 31 octets au total pour représenter un actif d'une chaîne dans notre pont. Par exemple, cela peut exprimer tokenX sur la chaîne A est équivalent à tokenY sur la chaîne B
 - **Calldata** — est le paramètre requis pour le gestionnaire qui inclut les informations nécessaires pour exécuter la proposition sur la chaîne B. La sérialisation exacte est définie pour chaque gestionnaire. Vous pouvez trouver plus d'informations [ici](https://chainbridge.chainsafe.io/chains/ethereum/#erc20-erc721-handlers)

## Essayez-le sur Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Nous avons mis en place un relais avec l'implémentation ChainBridge, qui est connecté à notre Moonbase Alpha TestNet et aux testnets Rinkeby et Kovan d'Ethereum.

ChainSafe a [des gestionnaires préprogrammés](https://chainbridge.chainsafe.io/chains/ethereum/#handler-contracts) spécifiques aux interfaces ERC-20 et ERC-721, et ces gestionnaires sont utilisés pour transférer les jetons ERC-20 et ERC-721 entre les chaînes. En termes généraux, cela ne fait que réduire le diagramme à usage général que nous avons décrit précédemment, de sorte que le gestionnaire ne fonctionne qu'avec les fonctions de jeton spécifiques telles que _lock/burn_, et _mint/unlock_. 

Ce guide passera en revue deux exemples différents d'utilisation du pont pour transférer des jetons entre les chaînes. Vous frapperez les jetons Moon ERC-20S et ERC-721 et les transférerez sur le pont de Moonbase Alpha à Kovan. Les mêmes étapes peuvent être suivies et appliquées à Rinkeby. Pour interagir avec Moonbase Alpha et Kovan / Rinkeby, vous aurez besoin des informations suivantes:

```
# Kovan/Rinkeby - Moonbase Alpha bridge contract address:
    {{ networks.moonbase.chainbridge.bridge_address }}

 # Kovan/Rinkeby - Moonbase Alpha ERC-20 handler contract:
    {{ networks.moonbase.chainbridge.ERC20_handler }}
   
# Kovan/Rinkeby - Moonbase Alpha ERC-721 handler contract:
    {{ networks.moonbase.chainbridge.ERC721_handler }}
```

!!! remarque
    Le contrat de pont, le contrat ERC-20 et les adresses de contrat ERC-721 énumérées ci-dessus sont applicables pour Kovan et Rinkeby.

### Transfert de jetons ERC-20 {: #erc-20-token-transfer } 

Les jetons ERC-20 qui veulent être déplacés à travers le pont doivent être enregistrés par les relais dans le contrat du gestionnaire. Par conséquent, pour tester le pont, nous avons déployé un jeton ERC-20 (ERC20S) où n'importe quel utilisateur peut minter 5 jetons:

```
# Kovan/Rinkeby - Moonbase Alpha custom ERC-20 sample token:
    {{ networks.moonbase.chainbridge.ERC20S }}
```

De la même manière, interagir directement avec le contrat Bridge et appeler la fonction `deposit()` avec les paramètres corrects peut paraitre compliqué. Pour faciliter le processus d'utilisation du pont, nous avons créé un contrat de pont modifié, qui construit les entrées nécessaires à la fonction `deposit()` :

```
# Kovan/Rinkeby - Moonbase Alpha custom bridge contract:
    {{ networks.moonbase.chainbridge.bridge_address }}
```


En termes simples, le contrat modifié utilisé pour lancer le transfert a l' _IDdeChaine_ et _IDRessources_ prédéfinis pour cet exemple. Par conséquent, il crée l' objet _calldata_ à partir de l'entrée de l'utilisateur, qui est uniquement l'adresse du destinataire et la valeur à envoyer.

Le flux de travail général pour cet exemple peut être vu dans ce diagramme:

![ChainBridge ERC20 workflow](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-erc20.png)

Pour essayer le pont avec cet exemple de jeton ERC-20, nous devons suivre les étapes suivantes (quelle que soit la direction du transfert):
 
 - Jetons Minter dans la chaîne source (cela approuve le contrat du gestionnaire source en tant que dépensier pour le montant frappé)
 - Utilisez le contrat de pont modifié pour envoyer des jetons de la chaîne source à la chaîne cible
 - Attendez que le processus soit terminé
 - Approuver le contrat du gestionnaire en tant que dépensier pour renvoyer les jetons
 - Utilisez le contrat de pont modifié pour envoyer des jetons de la chaîne cible à la chaîne source

!!! remarque
    N'oubliez pas que les jetons ne seront transférés que si le contrat du gestionnaire dispose d'une allocation suffisante pour dépenser des jetons au nom du propriétaire. Si le processus échoue, vérifiez l'allocation.

Envoyons des jetons ERC20S de Moonbase Alpha à Kovan . Si vous vouliez l'essayer avec Rinkeby, les étapes et les adresses sont les mêmes. Pour cela, nous utiliserons [Remix](/integrations/remix/). Tout d'abord, nous pouvons utiliser l'interface suivante pour interagir avec ce contrat et minter les jetons:

```solidity
pragma solidity ^0.8.1;

/**
    Interface for the Custom ERC20 Token contract for ChainBridge implementation
    Kovan/Rinkeby - Moonbase Alpha ERC-20 Address : 
        {{ networks.moonbase.chainbridge.ERC20S }}
*/

interface ICustomERC20 {

    // Mint 5 ERC20S Tokens
    function mintTokens() external;

    // Get Token Name
    function name() external view returns (string memory);
    
    /** 
        Increase allowance to Handler
        Kovan/Rinkeby - Moonbase Alpha ERC-20 Handler:
           {{ networks.moonbase.chainbridge.ERC20_handler}}
    */
    function increaseAllowance(address spender, uint256 addedValue) external returns (bool);
    
    // Get allowance
    function allowance(address owner, address spender) external view returns (uint256);
}
```

Notez que la fonction mint du contrat de jeton ERC-20 a également été modifiée pour approuver le contrat de gestionnaire correspondant en tant que dépensier lors de la frappe de jetons.

Après avoir ajouté le contrat ERC20 personnalisé à Remix et l'avoir compilé, les étapes suivantes consistent à fabriquer les jetons ERC20S:

1. Accédez à la page **Deploy & Run Transactions** sur Remix
2. Sélectionnez Injected Web3 dans la liste déroulante **Environment**
3. Chargez l'adresse de contrat de jeton ERC-20 personnalisée et cliquez sur **At Address**
4. Appelez la fonction `mintTokens()` et signez la transaction. 
5. Une fois la transaction confirmée, vous devriez avoir reçu 5 jetons ERC20S. Vous pouvez vérifier votre solde en ajoutant le jeton à [MetaMask](/integrations/wallets/metamask/).

![ChainBridge ERC20 mint Tokens](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-1.png)

Une fois que nous avons les jetons, nous pouvons procéder à leur envoi par le pont vers la chaîne cible. Dans ce cas, rappelez-vous que nous le faisons à partir depuis **Moonbase Alpha** vers **Kovan**. Il existe une interface unique qui vous permettra de transférer les jetons ERC20S et ERC721M. Pour cet exemple, vous utiliserez la fonction `sendERC20SToken()` pour initier le transfert de vos jetons ERC20S mintés:

```solidity
pragma solidity 0.8.1;

/**
    Simple Interface to interact with bridge to transfer the ERC20S and ERC721M tokens
    Kovan/Rinkeby - Moonbase Alpha Bridge Address: 
        {{ networks.moonbase.chainbridge.bridge_address }}
 */

interface IBridge {

    /**
     * Calls the `deposit` function of the Chainbridge Bridge contract for the custom ERC20 (ERC20Sample) 
     * by building the requested bytes object from: the recipient, the specified amount and the destination
     * chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC20SToken(uint256 destinationChainID, address recipient, uint256 amount) external;
    
    /**
     * Calls the `deposit` function for the custom ERC721 (ERC721Moon) that is only mintable in the
     * MOON side of the bridge. It builds the bytes object requested by the method from: the recipient,
     * the specified token ID and the destination chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC721MoonToken(uint256 destinationChainID, address recipient, uint256 tokenId) external;
}
```

Après avoir ajouté le contrat Bridge à Remix et l'avoir compilé, pour envoyer des jetons ERC20 sur le pont, vous devrez:

1. Chargez l'adresse du contrat de pont et cliquez sur **At Address**
2. Pour appeler la fonction `sendERC20SToken()` entrez l'ID de la chaîne de destination (Pour cet exemple , nous utilisons Kovan: `42`)
3. Entrez l'adresse du destinataire de l'autre côté du pont
4. Ajouter le montant à transférer en WEI
5. Cliquez sur **transact** puis MetaMask devrait apparaître pour vous demander de signer la transaction. 

Une fois la transaction confirmée, le processus peut prendre environ 3 minutes, après quoi vous devriez avoir reçu les jetons à Kovan!

![ChainBridge ERC20 send Tokens](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-2.png)

Vous pouvez vérifier votre solde en ajoutant le jeton à [MetaMask](/integrations/wallets/metamask/) et en le connectant au réseau cible - dans notre cas Kovan.

![ChainBridge ERC20 balance](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-3.png)

N'oubliez pas que vous pouvez également créer des jetons ERC20S dans Kovan et les envoyer à Moonbase Alpha. Pour approuver un dépenseur ou augmenter son allocation, vous pouvez utiliser la fonction `increaseAllowance()` de l'interface fournie. Pour vérifier l'allocation du contrat de gestionnaire dans le contrat de jeton ERC20, vous pouvez utiliser la fonction `allowance()` de l'interface.

!!! remarque
    Les jetons ne seront transférés que si le contrat du gestionnaire dispose d'une allocation suffisante pour dépenser des jetons au nom du propriétaire. Si le processus échoue, vérifiez l'allocation.

### Transfert de jeton ERC-721 {: #erc-721-token-transfer } 

Semblable à notre exemple précédent, les contrats de jetons ERC-721 doivent être enregistrés par les relais pour permettre le transfert via le pont. Par conséquent, nous avons personnalisé un contrat de jeton ERC-721 afin que tout utilisateur puisse créer un jeton pour tester le pont. Cependant, comme chaque jeton est non fongible, et par conséquent unique, la fonction mint n'est disponible que dans le contrat de jeton de chaîne source et non dans le contrat cible. En d'autres termes, les jetons ERC-721M ne peuvent être mintés que sur Moonbase Alpha, puis transférés à Rinkeby ou Kovan. Le diagramme suivant explique le flux de travail pour cet exemple, où il est important de souligner que l'ID de jeton et les métadonnées sont conservés.

![ChainBridge ERC721 workflow](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-erc721.png)

Pour minter des jetons dans Moonbase Alpha (nommés ERC721Moon avec le symbole ERC721M) et les envoyer dans les deux sens à Kovan / Rinkeby, vous avez besoin de l'adresse suivante:

```
# Kovan/Rinkeby - Moonbase Alpha ERC-721 Moon tokens (ERC721M),
# Mint function in Moonbase Alpha: 
    {{ networks.moonbase.chainbridge.ERC721M }}
```

Au lieu d'interagir avec le contrat de pont et d'appeler la fonction `deposit()`, nous avons modifié le contrat de pont pour faciliter le processus d'utilisation du pont (même adresse que dans l'exemple précédent):

```
# Kovan/Rinkeby - Moonbase Alpha custom bridge contract:
    {{ networks.moonbase.chainbridge.bridge_address }}
```

En termes simples, le contrat de pont modifié utilisé pour lancer le transfert va créer l _IDchaine_ and _IDressource_ pour cet exemple basé sur l'ID de la chaîne de destination que vous fournissez. Par conséquent, il crée l' objet _calldata_ à partir de l'entrée de l'utilisateur, qui est uniquement l'adresse du destinataire et l'ID de jeton à envoyer.

Envoyons un jeton ERC721M depuis **Moonbase Alpha** vers **Kovan**.  Pour cela, nous utiliserons [Remix](/integrations/remix/). L'interface suivante peut être utilisée pour interagir avec le contrat source ERC721M et minter les jetons. La fonction `tokenOfOwnerByIndex()` peut également être utilisée pour vérifier les ID de jeton appartenant à une adresse spécifique, en passant l'adresse et l'index à interroger (chaque ID de jeton est stocké en tant qu'élément de tableau associé à l'adresse):

```solidity
pragma solidity ^0.8.1;

/**
    Interface for the Custom ERC721 Token contract for ChainBridge implementation:
    Kovan/Rinkeby - Moonbase Alpha:
        ERC721Moon: {{ networks.moonbase.chainbridge.ERC721M }}

    ERC721Moon tokens are only mintable on Moonbase Alpha
*/

interface ICustomERC721 {
    
    // Mint 1 ERC-721 Token
    function mintToken() external returns (uint256);
    
    // Query tokens owned by Owner
    function tokenOfOwnerByIndex(address _owner, uint256 _index) external view returns (uint256);

    // Get Token Name
    function name() external view returns (string memory);
    
    // Get Token URI
    function tokenURI(uint256 tokenId) external view returns (string memory);
    
    /**
        Set Approval for Handler 
        Kovan/Rinkeby - Moonbase Alpha ERC-721 Handler:
           {{ networks.moonbase.chainbridge.ERC721_handler }}
    */
    function approve(address to, uint256 tokenId) external;
    
    // Check the address approved for a specific token ID
    function getApproved(uint256 tokenId) external view returns (address);
}
```

Notez que la fonction mint du contrat de jeton ERC-721 a également été modifiée pour approuver le contrat de gestionnaire correspondant en tant que dépensier lors de la fonction mint de jetons.

Après avoir ajouté le contrat à Remix et l'avoir compilé, nous voudrons ensuite créer un jeton ERC721M:

1. Accédez à la page **Deploy & Run Transactions** sur Remix
2. Sélectionnez Injected Web3 dans la liste déroulante **Environment**
3. Chargez l'adresse de contrat de jeton ERC721M personnalisée et cliquez sur **At Address**
4. Appelez la fonction `mintTokens()` et signez la transaction. 
5. Une fois la transaction confirmée, vous devriez avoir reçu un jeton ERC721M. Vous pouvez vérifier votre solde en ajoutant le jeton à [MetaMask](/integrations/wallets/metamask/).

![ChainBridge ERC721 mint Tokens](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-4.png) 

L'interface suivante vous permet d'utiliser la fonction `sendERC721MoonToken()` pour lancer le transfert de jetons mintés à l'origine dans Moonbase Alpha (ERC721M).

```solidity
pragma solidity 0.8.1;

/**
    Simple Interface to interact with bridge to transfer the ERC20S and ERC721M tokens
    Kovan/Rinkeby - Moonbase Alpha Bridge Address: 
        {{ networks.moonbase.chainbridge.bridge_address }}
 */

interface IBridge {

    /**
     * Calls the `deposit` function of the Chainbridge Bridge contract for the custom ERC20 (ERC20Sample) 
     * by building the requested bytes object from: the recipient, the specified amount and the destination
     * chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC20SToken(uint256 destinationChainID, address recipient, uint256 amount) external;
    
    /**
     * Calls the `deposit` function for the custom ERC721 (ERC721Moon) that is only mintable in the
     * MOON side of the bridge. It builds the bytes object requested by the method from: the recipient,
     * the specified token ID and the destination chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC721MoonToken(uint256 destinationChainID, address recipient, uint256 tokenId) external;
}
```

Vous pouvez maintenant envoyer le jeton ERC721M via le pont vers la chaîne cible. Dans ce cas, rappelez-vous que nous le ferons de Moonbase Alpha à Kovan. Pour transférer le jeton ERC721M sur le pont:

1. Chargez l'adresse du contrat de pont et cliquez sur **At Address**
2. Appelez la fonction `sendERC721MoonToken()` pour lancer le transfert de ERC721M jetons à l' origine dans Moonbase Alpha frappé en fournissant l'ID de la chaîne de destination (Pour cet exemple , nous utilisons Kovan: `42`)
3. Entrez l'adresse du destinataire de l'autre côté du pont
4. Ajoutez l'ID de jeton à transférer
5. Cliquez sur **transact** puis MetaMask devrait apparaître pour vous demander de signer la transaction.

Une fois la transaction confirmée, le processus peut prendre environ 3 minutes, après quoi vous devriez avoir reçu le même identifiant de jeton dans Kovan!

![ChainBridge ERC721 send Token](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-5.png)

Vous pouvez vérifier votre solde en ajoutant le jeton à [MetaMask](/integrations/wallets/metamask/) et en le connectant au réseau cible, dans notre cas Kovan.

![ChainBridge ERC721 balance](/images/builders/integrations/bridges/eth/chainbridge/chainbridge-6.png)

N'oubliez pas que les jetons ERC721M ne peuvent être mintés que dans Moonbase Alpha et qu'ils seront ensuite disponibles pour être envoyés dans les deux sens vers Kovan ou Rinkeby. Il est important de toujours vérifier l'allocation fournie au contrat de gestionnaire dans le contrat de jeton ERC721 correspondant. Vous pouvez approuver le contrat du gestionnaire pour envoyer des jetons en votre nom à l'aide de la fonction `approve()` fournie dans l'interface. Vous pouvez vérifier l'approbation de chacun de vos identifiants de jeton avec la fonction `getApproved()` .

!!! remarque
    Les jetons ne seront transférés que si le contrat du gestionnaire est approuvé pour transférer les jetons au propriétaire. Si le processus échoue, vérifiez l'approbation.

### Gestionnaire générique {: #generic-handler } 

Le Gestionnaire générique offre la possibilité d'exécuter une fonction dans la chaîne A et de créer une proposition pour exécuter une autre fonction dans la chaîne B (similaire au diagramme de workflow général). Cela fournit un moyen convaincant de connecter deux blockchains indépendantes.

L'adresse du Gestionnaire générique est:
```
{{ networks.moonbase.chainbridge.generic_handler }}
```

Si vous souhaitez implémenter cette fonctionnalité, vous pouvez nous contacter directement via notre [Serveur Discord](https://discord.com/invite/PfpUATX).  Nous serons heureux de discuter de cette implémentation.

### Moonbase Alpha ChainBridge UI {: #moonbase-alpha-chainbridge-ui } 

Si vous voulez jouer avec le transfert de jetons ERC20S de Moonbase Alpha vers Kovan ou Rinkeby sans avoir à configurer les contrats dans Remix, vous pouvez consulter notre interface utilisateur [Moonbase Alpha ChainBridge UI](https://moonbase-chainbridge.netlify.app/transfer).

