---
title: Ledger
description: Ce guide vous explique comment utiliser votre portefeuille matériel Ledger pour signer des transactions dans Moonbeam, en tirant parti de ses fonctionnalités de compatibilité Ethereum
---

# Portefeuille materiel Ledger

![Intro diagram](/images/tokens/connect/ledger/ledger-banner.png)

## Introduction {: #introduction } 

Les portefeuilles matériels offrent un moyen plus sûr de stocker des fonds cryptographiques, car la clé privée (utilisée pour signer les transactions) est stockée hors ligne. Ledger propose deux solutions de portefeuille matériel au moment de la rédaction : Ledger Nano S et Ledger Nano X.

Étant donné que Moonbeam est entièrement compatible avec Ethereum et que Ledger prend désormais en charge la signature dans différents réseaux d'identification de chaîne, vous pouvez utiliser votre appareil Ledger pour signer des transactions sur Moonbeam !

Ce tutoriel vous montre comment démarrer avec votre portefeuille matériel Ledger sur Moonbase Alpha. Le guide illustre uniquement les étapes d'un appareil Ledger Nano X, mais vous pouvez également le suivre avec un Ledger Nano S. Le même processus peut être appliqué aux autres réseaux de l'écosystème Moonbeam.

## Vérification des prérequis {: #checking-prerequisites } 

Avant de commencer, mettez à jour [Ledger Live](https://www.ledger.com/ledger-live/download) à la dernière version disponible. Assurez-vous également que votre périphérique de portefeuille matériel Ledger exécute le dernier micrologiciel. Le site Web d'assistance de Ledger propose des tutoriels sur la mise à jour du micrologiciel des deux dispositifs [Ledger Nano S](https://support.ledger.com/hc/en-us/articles/360002731113-Update-Ledger-Nano-S-firmware) et [Ledger Nano X](https://support.ledger.com/hc/en-us/articles/360013349800-Update-Ledger-Nano-X-firmware) .

Une fois que vous exécutez la dernière version du micrologiciel, assurez-vous que vous exécutez la dernière application Ethereum. Le site Web d'assistance de Ledger propose un didacticiel sur [comment installer l'app Ethereum](https://support.ledger.com/hc/en-us/articles/360009576554-Ethereum-ETH-).

Au moment de la rédaction, les versions suivantes étaient utilisées:

 - Ledger Live 2.29
 - Ledger Nano S firmware v2.0.0
 - Ledger Nano X firmware v1.3.0
 - Ethereum app v1.8.5

De plus, vous aurez besoin de MetaMask comme intermédiaire entre votre appareil Ledger et Moonbase Alpha. Assurez-vous que votre MetaMask est [connecté à Moonbase Alpha](/integrations/wallets/metamask/). Les utilisateurs de Chrome (version 91) ont besoin de quelques étapes supplémentaires, qui [sont détaillées dans ce tutoriel](#chrome-browser). L'utilisation de Firefox se traduira par une expérience beaucoup plus simple/directe.

Veuillez noter que votre appareil Ledger signera les transactions dans n'importe quel réseau MetaMask auquel il est connecté.

## Importation de votre compte Ledger dans MetaMask {: #importing-your-ledger-account-to-metamask } 

Pour commencer, vous devez connecter votre appareil Ledger à l'ordinateur, le déverrouiller, ouvrir l'application Ethereum. Ensuite, pour importer votre compte Ethereum Ledger dans MetaMask, procédez comme suit:

 1. Cliquez sur le logo en haut à droite pour développer le menu
 2. Sélectionnez "Connect Hardware Wallet"

![MetaMask Connect Hardware Wallet](/images/tokens/connect/ledger/ledger-1.png)

Dans l'écran suivant, vous êtes invité à sélectionner le portefeuille matériel que vous souhaitez utiliser dans MetaMask. Au moment de la rédaction, seuls les portefeuilles matériels Ledger et Trezor sont pris en charge. Ici, suivez les étapes suivantes:

 1. Sélectionnez le logo Ledger
 2. Cliquer sur "Continue"

![MetaMask Select Ledger Hardware Wallet](/images/tokens/connect/ledger/ledger-2.png)

Si MetaMask a réussi à se connecter à votre appareil Ledger, vous devriez voir une liste de cinq comptes de style Ethereum. Si ca n'est pas le cas, vérifiez que Ledger Live est fermé, que vous avez connecté votre appareil Ledger à l'ordinateur, l'avez déverrouillé et que l'application Ethereum est ouverte. Si vous utiliser chrome vérifiez ces [étapes supplémentaires](#chrome-browser).

À partir de cette liste de cinq comptes Ethereum, procédez comme suit:

 1. Sélectionnez les comptes que vous souhaitez importer depuis votre appareil Ledger
 2. Cliquer sur "Unlock"

![MetaMask Select Ethereum Accounts to Import](/images/tokens/connect/ledger/ledger-3.png)

Si vous avez importé avec succès votre compte de style Ledger Ethereum, vous devriez le voir s'afficher sur l'écran principal de MetaMask comme illustré dans l'image suivante:

![MetaMask Successfully Imported Ledger Account](/images/tokens/connect/ledger/ledger-4.png)

Vous avez maintenant importé avec succès un compte compatible Moonbeam depuis votre appareil Ledger et êtes maintenant prêt à commencer à [signer des transactions à l'aide de votre portefeuille matériel](#signing-a-transaction-using-your-ledger).

### Navigateur Chrome {: #chrome-browser } 

À partir de la version 91 de Chrome, les utilisateurs qui souhaitent connecter leur appareil Ledger à MetaMask doivent exécuter la dernière version de Ledger Live (v2.29 au moment de la rédaction). 

De plus, dans MetaMask, ils doivent activer le support Ledger Live. Pour ce faire, suivez les étapes suivantes:

 1. Développez le menu en haut à droite et accédez à "Settings"
 2. Aller vers "Advanced"
 3. Activer la fonctionnalité "Use Ledger Live"

Avec cette fonctionnalité activée, MetaMask ouvrira Ledger Live lors de la tentative de connexion à votre appareil Ledger. Vous pouvez en savoir plus à ce sujet dans ce [blog post MetaMask](https://metamask.zendesk.com/hc/en-us/articles/360020394612-How-to-connect-a-Trezor-or-Ledger-Hardware-Wallet).

## Signer une transaction à l'aide de votre Ledger {: #signing-a-transaction-using-your-ledger } 

Si vous avez réussi à [importé votre compte Ledger dans MetaMask](#importing-your-ledger-account-to-metamask), vous êtes prêt à signer des transactions sur Moonbeam à l'aide de votre appareil Ledger. Ce tutoriel va vous montrer comment envoyer une transaction simple sur le TestNet Moonbase Alpha , mais il s'applique aux autres réseaux de l'écosystème Moonbeam.

Tout d'abord, assurez-vous que votre compte Ledger soit [financé avec des jetons DEV](/getting-started/moonbase/faucet/). Cliquez ensuite sur le bouton "Send".

![MetaMask Ledger Account Funded](/images/tokens/connect/ledger/ledger-5.png)

Comme vous le feriez dans une transaction standard, définissez l'adresse du destinataire, entrez le nombre de jetons à envoyer, vérifiez les détails de la transaction et confirmez-la. Cela lancera l'assistant de signature de transaction dans votre appareil Ledger. Ici, suivez les étapes suivantes:

 1. Cliquez sur le bouton pour passer à l'écran suivant. Votre appareil Ledger vous demande uniquement d'examiner la transaction
 2. Vérifiez le nombre de jetons envoyés. Veuillez noter que le token correspond au réseau auquel MetaMask est connecté. **Dans ce cas, il s'agit de jetons DEV et non d'ETH!** Lorsque vous êtes prêt, passez à l'écran suivant
 3. Vérifiez l'adresse du destinataire et passez à l'écran suivant
 4. Vérifiez l'ID de chaîne du réseau. Cette information confirme à quel réseau MetaMask est connecté. Par exemple, pour Moonbase Alpha, l'ID de chaîne est 1287, Moonriver 1285 (pas encore en ligne) et Moonbeam 1284 (pas encore en ligne). Lorsque vous êtes prêt, passez à l'écran suivant
 5. Vérifiez les frais max applicables à cette transaction. Il s'agit du prix du gaz multiplié par la limite de gaz que vous avez définie sur MetaMask. Lorsque vous êtes prêt, passez à l'écran suivant
 6. Si vous êtes d'accord avec tous les détails de la transaction, approuvez-la. Cela signera la transaction et demandera à Metamask de déclencher l'envoi. Si vous n'êtes pas d'accord, passez à l'écran suivant
 7. Si vous n'êtes pas d'accord avec tous les détails de la transaction, rejetez-la. Cela annulera la transaction et MetaMask la marquera comme ayant échoué

!!! remarque
    Au moment de la rédaction, le nom du jeton est toujours affiché sous la forme `ETH`. Veuillez noter que le token manipulé est celui correspondant au réseau auquel MetaMask est connecté.

![MetaMask Ledger Transaction Wizard](/images/tokens/connect/ledger/ledger-6.png)

Juste après avoir approuvé la transaction, MetaMask l'envoie au réseau. Une fois la transaction confirmée, elle sera affichée comme "Envoyer" sur l'écran principal de MetaMask.

![MetaMask Ledger Transaction Wizard](/images/tokens/connect/ledger/ledger-7.png)

Et c'est tout ! Vous avez signé une transaction sur Moonbase Alpha en utilisant votre portefeuille matériel Ledger!

## Interagir avec les contrats à l'aide de votre Ledger {: #interacting-with-contracts-using-your-ledger } 

Par défaut, les périphériques Ledger n'admettent pas de champ `data` dans l'objet de transaction. Par conséquent, les utilisateurs ne peuvent pas déployer ou interagir avec des contrats intelligents.

Cependant, si vous souhaitez utiliser votre portefeuille matériel Ledger pour les transactions liées aux contrats intelligents, vous devez modifier un paramètre de configuration dans l'application Ethereum. Pour ce faire, suivez les étapes suivantes:

 1. Ouvrez l'application Ledger Ethereum
 2. Aller vers "Settings"
 3. Trouvez la page "Contract data". Elle devrait indiquer "NOT allowed" en bas
 4. Sélectionnez/validez l'option pour changer sa valeur en "Allowed"

!!! remarque
    Cette option est nécessaire pour utiliser votre appareil Ledger pour interagir avec les contrats de jetons ERC20 qui pourraient vivre à l'intérieur de l'écosystème Moonbeam.

![MetaMask Ledger Allow Contracts Tx](/images/tokens/connect/ledger/ledger-8.png)
