---
title: Contrats & Librairies
description:  Apprenez à créer facilement des contrats OpenZeppelin communs avec leur assistant de contrats et à les déployer sur Moonbeam grâce à ses fonctionnalités de compatibilité Ethereum
---

# OpenZeppelin Contrats & Librairies

![OpenZeppelin Contracts Banner](/images/openzeppelin/ozcontracts-banner.png)

## Introduction

Les contrats et les bibliothèques OpenZeppelin sont devenus un standard dans l'industrie. Ils aident les développeurs à minimiser les risques, car leurs modèles de code open source sont testés rigoureusement pour Ethereum et d'autres blockchains. Leur code comprend les implémentations les plus utilisées des normes et des modules complémentaires ERC et apparaît souvent dans des guides et des tutoriels de la communauté.

Étant donné que Moonbeam est entièrement compatible avec Ethereum, tous les contrats et bibliothèques d'OpenZeppelin peuvent être implémentés sans aucune modification.

Ce guide est divisé en deux sections. La première partie décrit l'assistant de contrats OpenZeppelin, un excellent outil en ligne pour vous aider à créer des contrats intelligents à l'aide du code OpenZeppelin. La deuxième section fournit un guide étape par étape sur la façon dont vous pouvez déployer ces contrats sur Moonbeam.

## Assistant de Contrats OpenZeppelin

OpenZeppelin a développé un outil de génération de contrat interactif basé sur le Web qui est probablement le moyen le plus simple et le plus rapide d'écrire votre contrat intelligent à l'aide du code OpenZeppelin. L'outil s'appelle Contracts Wizard, et vous pouvez le trouver sur leur [site de documentation] (https://docs.openzeppelin.com/contracts/4.x/wizard).

Actuellement, l'Assistant de Contrats prend en charge les normes ERC suivantes :

 - [**ERC20**](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) — une norme de jeton fongible qui suit [EIP-20](https://eips.ethereum.org/EIPS/eip-20). Fongible signifie que tous les jetons sont équivalents et interchangeables, c'est-à-dire de valeur égale. Un exemple typique de jetons fongibles est la monnaie fiduciaire, où chaque billet de même valeur a la même valeur.
 - [**ERC721**](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/) — un contrat de jeton non fongible qui suit [EIP-721](https://eips.ethereum.org/EIPS/eip-721). Non fongible signifie que chaque jeton est différent, et donc unique. Un jeton ERC721 peut représenter la propriété de cet objet unique, qu'il s'agisse d'un objet de collection dans un jeu, d'un état réel, etc. 
 - [**ERC1155**](https://docs.openzeppelin.com/contracts/4.x/erc1155) — également connu sous le nom de contrat multi-jetonss, car il peut représenter à la fois des jetons fongibles et non fongibles dans un seul contrat intelligent. Çela suit [EIP-1155](https://eips.ethereum.org/EIPS/eip-1155)

L'assistant comprend les sections suivantes :

  1. **Sélection de norme de jeton** : affiche toutes les différentes normes prises en charge par l'assistant
  2. **Paramètres** : fournit les paramètres de base pour chaque norme de jeton, tels que le nom du jeton, le symbole, le pre-mint (fourniture du jeton lorsque le contrat est déployé) et l'URI (pour les jetons non fongibles)
  3. **Fonctionnalités** — liste de toutes les fonctionnalités disponibles pour chaque norme de jeton. Vous pouvez trouver plus d'informations sur les différentes fonctionnalités dans les liens suivants :
     - [ERC20](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20)
     - [ERC721](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721)
     - [ERC1155](https://docs.openzeppelin.com/contracts/4.x/api/token/erc1155)
  4. **Contrôle d'accès** — liste de tous les [mécanismes de contrôle d'accès](https://docs.openzeppelin.com/contracts/4.x/access-control) disponibles pour chaque norme de jeton
  5. **Affichage du code interactif** : affiche le code du contrat intelligent avec la configuration définie par l'utilisateur

![Assistant de Contrats OpenZeppelin](/images/openzeppelin/ozwizard-images1.png)

Une fois que vous avez configuré votre contrat avec tous les paramètres et fonctionnalités, c'est aussi simple que de copier et coller le code dans votre fichier de contrat.

## Déploiement des contrats OpenZeppelin sur Moonbeam

Cette section passe en revue les étapes de déploiement des contrats OpenZeppelin sur Moonbeam. Il couvre les contrats suivants :

 - ERC20 (jetons fongibles)
 - ERC721 (jetons non fongibles)
 - ERC1155 (norme multi-jetons)

Tout le code des contrats a été obtenu à l'aide d'OpenZeppelin [Assistant de Contrat](https://docs.openzeppelin.com/contracts/4.x/wizard).
 
### Vérification des prérequis

Les étapes décrites dans cette section supposent que [MetaMask](https://metamask.io/) est installé et connecté à Moonbase Alpha TestNet. Le déploiement du contrat se fait à l'aide du [Remix IDE](https://remix.ethereum.org/) via l'environnement "Injected Web3". Vous pouvez trouver les tutoriels correspondants dans les liens suivants :

 - [Interagir avec Moonbeam en utilisant MetaMask](/integrations/wallets/metamask/)
 - [Interagir avec Moonbeam en utilisant Remix](/integrations/remix/)

### Déploiement d'un jeton ERC20

Pour cet exemple, un jeton ERC20 sera déployé sur Moonbase Alpha. Le code final utilisé combine différents contrats d'OpenZeppelin :

 - **ERC20.sol** — Implémentation du jeton ERC20 avec les fonctionnalités optionnelles de l'interface de base. Inclut le mécanisme d'approvisionnement avec une fonction `mint`, mais doit être explicitement appelé à partir du contrat principal
 - **Ownable.sol** — extension pour restreindre l'accès à certaines fonctions

Le contrat de jeton ERC20 OpenZeppelin monnayable fournit une fonction `mint`que le propriétaire du contrat ne peut appeler que. Par défaut, le propriétaire est l'adresse du déployeur du contrat. Il y a aussi un nombre de jetons `1000` envoyés au déployeur du contrat configuré dans la fonction `constructeur`.

La première étape consiste à aller sur [Remix](https://remix.ethereum.org/) et à suivre les étapes suivantes :

 1. Cliquez sur l'icône "Créer un nouveau fichier" et définissez un nom de fichier. Pour cet exemple, il a été défini sur `ERC20.sol`
 2. Assurez-vous que le fichier a été créé avec succès. Cliquez sur le fichier pour l'ouvrir dans l'éditeur de texte
 3. Rédigez votre contrat intelligent à l'aide de l'éditeur de fichiers. Pour cet exemple, le code suivant a été utilisé :

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC20, Ownable {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000 * 10 ** decimals());
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
}
```

Ce contrat intelligent de jeton ERC20 a été extrait de l'[Assistant de contrat](#openzeppelin-contract-wizard), en définissant un préminage de `1000` jetons et en activant la fonction `Mintable`.

![Démarrer avec Remix](/images/openzeppelin/ozcontracts-images1.png)

Une fois votre contrat intelligent rédigé, vous pouvez le compiler en procédant comme suit :

 1. Dirigez-vous vers le "Compilateur Solidity"
 2. Cliquez sur le bouton compiler
 3. Alternativement, vous pouvez vérifier la fonction "Compilation automatique"

![Compiler le contrat ERC20 avec Remix](/images/openzeppelin/ozcontracts-images2.png)

Une fois le contrat compilé, vous êtes prêt à le déployer en procédant comme suit :

 1. Dirigez-vous vers l'onglet "Déployer et exécuter des transactions"
 2. Changez l'environnement en "Injected Web3". Cela utilisera le fournisseur injecté de MetaMask. Par conséquent, le contrat sera déployé sur n'importe quel réseau auquel MetaMask est connecté. MetaMask peut afficher une fenêtre contextuelle indiquant que Remix essaie de se connecter à votre portefeuille
 3. Sélectionnez le contrat approprié à déployer. Dans cet exemple, il s'agit du contrat `MyToken` dans le fichier `ERC20.sol`
 4. Si tout est prêt, cliquez sur le bouton "Déployer". Vérifiez les informations de transaction dans MetaMask et confirmez-les
 5. Après quelques secondes, la transaction devrait être confirmée et vous devriez voir votre contrat sous « Contrats déployés »

![Déployer le contrat ERC20 avec Remix](/images/openzeppelin/ozcontracts-images3.png)

Et c'est tout ! Vous avez déployé un contrat de jeton ERC20 à l'aide des contrats et des bibliothèques d'OpenZeppelin. Ensuite, vous pouvez interagir avec votre contrat de jeton via Remix, ou l'ajouter à MetaMask.

### Déploiement d'un jeton ERC721

Pour cet exemple, un jeton ERC721 sera déployé sur Moonbase Alpha. Le code final utilisé combine différents contrats d'OpenZeppelin :

 - **ERC721** — Implémentation du jeton ERC721 avec les fonctionnalités optionnelles de l'interface de base. Inclut le mécanisme d'approvisionnement avec une fonction `_mint` mais doit être explicitement appelé depuis le contrat principal
 - **Burnable** — extension permettant la destruction des tokens par leurs propriétaires (ou adresses approuvées)
 - **Enumerable** — extension pour permettre l'énumération en chaîne des jetons
 - **Ownable.sol** — extension pour restreindre l'accès à certaines fonctions

Le contrat de jeton monnayable ERC721 OpenZeppelin fournit une fonction `mint` qui ne peut être appelée que par le propriétaire du contrat. Par défaut, le propriétaire est l'adresse du déployeur du contrat.

Comme pour le [contrat ERC20](#deploying-an-erc20-token), la première étape consiste à aller sur [Remix](https://remix.ethereum.org/) et à créer un nouveau fichier. Pour cet exemple, le nom du fichier sera `ERC721.sol`.

Ensuite, vous devrez écrire le contrat intelligent et le compiler. Pour cet exemple, le code suivant est utilisé :

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC721, ERC721Enumerable, ERC721Burnable, Ownable {
    constructor() ERC721("MyToken", "MTK") {}

    function safeMint(address to, uint256 tokenId) public onlyOwner {
        _safeMint(to, tokenId);
    }

    function _baseURI() internal pure override returns (string memory) {
        return "Test";
    }

    function _beforeTokenTransfer(address from, address to, uint256 tokenId)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._beforeTokenTransfer(from, to, tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

Ce contrat intelligent de jeton ERC721 a été extrait de [Assistant de Contract] (#openzeppelin-contract-wizard), définissant l'`URI de base` comme `Test` et activant les fonctionnalités `Mintable`, `Burnable` et `Enumerable`.

Une fois le contrat compilé, rendez-vous dans l'onglet "Déployer et exécuter des transactions". Ici, vous devez :

 1. Changez l'environnement en "Injected Web3". Cela utilisera le fournisseur injecté de MetaMask. Par conséquent, le contrat sera déployé sur n'importe quel réseau auquel MetaMask est connecté. MetaMask peut afficher une fenêtre contextuelle indiquant que Remix essaie de se connecter à votre portefeuille
 2. Sélectionnez le contrat approprié à déployer. Dans cet exemple, il s'agit du contrat `MyToken` dans le fichier `ERC721.sol`
 3. Si tout est prêt, cliquez sur le bouton "Déployer". Vérifiez les informations de transaction dans MetaMask et confirmez-les
 4. Après quelques secondes, la transaction devrait être confirmée et vous devriez voir votre contrat sous « Contrats déployés »

![Déployer le contrat ERC721 avec Remix](/images/openzeppelin/ozcontracts-images4.png)

Et c'est tout ! Vous avez déployé un contrat de jeton ERC721 à l'aide des contrats et des bibliothèques d'OpenZeppelin. Ensuite, vous pouvez interagir avec votre contrat de jeton via Remix, ou l'ajouter à MetaMask.

### Déploiement d'un jeton ERC1155

Pour cet exemple, un jeton ERC1155 sera déployé sur Moonbase Alpha. Le code final utilisé combine différents contrats d'OpenZeppelin :

 - **ERC1155** — Implémentation du jeton ERC1155 avec les fonctionnalités optionnelles de l'interface de base. Inclut le mécanisme d'approvisionnement avec une fonction `_mint` mais doit être explicitement appelé depuis le contrat principal
 - **Pausable** — extension permettant de suspendre le transfert de jetons, les frappes et les gravures
 - **Ownable.sol** — extension pour restreindre l'accès à certaines fonctions

Le contrat de jeton ERC1155 d'OpenZeppelin fournit une fonction `_mint` qui ne peut être appelée que dans la fonction `constructor`. Par conséquent, cet exemple crée 1000 jetons avec un ID de `0` et 1 jeton unique avec un ID de `1`.

La première étape consiste à aller sur [Remix](https://remix.ethereum.org/) et à créer un nouveau fichier. Pour cet exemple, le nom de fichier sera `ERC1155.sol`.

Comme indiqué pour le [jeton ERC20](#deploying-an-erc20-token), vous devrez écrire le contrat intelligent et le compiler. Pour cet exemple, le code suivant est utilisé :

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/Pausable.sol";

contract MyToken is ERC1155, Ownable, Pausable {
    constructor() ERC1155("Test") {
        _mint(msg.sender, 0, 1000 * 10 ** 18, "");
        _mint(msg.sender, 1, 1, "");
    }

    function setURI(string memory newuri) public onlyOwner {
        _setURI(newuri);
    }

    function pause() public onlyOwner {
        _pause();
    }

    function unpause() public onlyOwner {
        _unpause();
    }

    function _beforeTokenTransfer(address operator, address from, address to, uint256[] memory ids, uint256[] memory amounts, bytes memory data)
        internal
        whenNotPaused
        override
    {
        super._beforeTokenTransfer(operator, from, to, ids, amounts, data);
    }
}
```

Ce contrat intelligent de jeton ERC1155 a été extrait de l'[Assistant de Contrat](#openzeppelin-contract-wizard), en ne définissant aucun `URI de base` et en activant la fonction `Pausable`. La fonction constructeur a été modifiée pour inclure la frappe d'un jeton fongible et non fongible.

Une fois le contrat compilé, rendez-vous dans l'onglet "Déployer et exécuter des transactions". Ici, vous devez :

 1. Changez l'environnement en "Injected Web3". Cela utilisera le fournisseur injecté de MetaMask. Par conséquent, le contrat sera déployé sur n'importe quel réseau auquel MetaMask est connecté. MetaMask peut afficher une fenêtre contextuelle indiquant que Remix essaie de se connecter à votre portefeuille
 2. Sélectionnez le contrat approprié à déployer. Dans cet exemple, il s'agit du contrat `MyToken` dans le fichier `ERC1155.sol`
 3. Si tout est prêt, cliquez sur le bouton "Déployer". Vérifiez les informations de transaction dans MetaMask et confirmez-les
 4. Après quelques secondes, la transaction devrait être confirmée et vous devriez voir votre contrat sous « Contrats déployés »

![Déployer le contrat ERC1155 avec Remix](/images/openzeppelin/ozcontracts-images5.png)

Et c'est tout ! Vous avez déployé un contrat de jeton ERC1155 à l'aide des contrats et des bibliothèques d'OpenZeppelin. Ensuite, vous pouvez interagir avec votre contrat de jeton via Remix.
