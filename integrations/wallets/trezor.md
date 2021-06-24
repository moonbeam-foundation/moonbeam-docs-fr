---
title: Trezor
description: Ce guide vous explique comment utiliser votre portefeuille matériel Trezor pour signer des transactions dans Moonbeam, en tirant parti de ses fonctionnalités de compatibilité Ethereum
---

# Portefeuille matériel Trezor

![Intro diagram](/images/trezor/trezor-banner.png)

## Introduction

Les portefeuilles matériels offrent un moyen plus sûr de stocker des fonds cryptographiques, car la clé privée (utilisée pour signer les transactions) est stockée hors ligne. Trezor propose deux solutions de portefeuille matériel au moment de la rédaction : Trezor One et Trezor Model T.

Étant donné que Moonbeam est entièrement compatible avec Ethereum, vous pouvez utiliser votre appareil Trezor pour signer des transactions sur Moonbeam!

Ce tutoriel vous montre comment démarrer avec votre portefeuille matériel Trezor sur Moonbase Alpha. Le guide illustre uniquement les étapes d'un appareil Trezor Model T, mais vous pouvez également le suivre avec un Trezor One.

Veuillez noter que votre appareil Trezor signera les transactions dans n'importe quel réseau MetaMask auquel il est connecté.

## Vérification des prérequis

Avant de commencer, mettez à jour [Trezor Suite](https://suite.trezor.io/) à la dernière version disponible. Assurez-vous également que votre périphérique de portefeuille matériel Trezor exécute le dernier micrologiciel. Le wiki Trezor propose des tutoriels sur la façon de mettre à jour le firmware des deux appareils [Trezor One](https://wiki.trezor.io/User_manual:Updating_the_Trezor_device_firmware__T1) et [Trezor Model T](https://wiki.trezor.io/User_manual:Updating_the_Trezor_device_firmware) .

Au moment de la rédaction, les versions suivantes étaient utilisées:

 - Trezor Suite 21.5.1
 - Trezor One firmware v1.10.0
 - Trezor Model T firmware v2.4.0

De plus, vous aurez besoin de MetaMask comme intermédiaire entre votre appareil Trezor et Moonbase Alpha. Assurez-vous que votre MetaMask est [connecté à Moonbase Alpha](/integrations/wallets/metamask/). Veuillez noter que votre appareil Trezor signera les transactions dans n'importe quel réseau MetaMask auquel il est connecté.

## Importer votre compte Trezor dans MetaMask

Pour commencer, vous devez avoir configuré un portefeuille (soit standard, soit un portefeuille caché). Une fois que vous avez connecté votre appareil Trezor, l'avez déverrouillé et configurez un portefeuille dans Trezor Suite. Ensuite, pour importer votre compte Trezor Ethereum dans MetaMask, procédez comme suit:

 1. Cliquez sur le logo en haut à droite pour développer le menu
 2. Sélectionnez "Connect Hardware wallet"

![MetaMask Connect Hardware Wallet](/images/ledger/ledger-images1.png)

Juste après, vous êtes invité à sélectionner le portefeuille matériel que vous souhaitez utiliser dans MetaMask. Au moment de la rédaction, seuls les portefeuilles matériels Ledger et Trezor sont pris en charge. Si votre appareil Trezor est prêt à fonctionner, suivez les étapes suivantes:

 1. Selectionnez le logo Trezor
 2. Cliquez sur "Continue"

![MetaMask Select Trezor Hardware Wallet](/images/trezor/trezor-images2.png)

Après avoir cliqué sur le bouton, un nouvel onglet nommé `TrezorConnect` devrait apparaitre, où vous devrez associer votre appareil. Ce n'est pas nécessaire si vous avez ouvert Trezor Suite et que votre appareil est connecté. Ici, cliquez sur "Pair devices.

![Trezor Hardware Wallet Connect Pair Device](/images/trezor/trezor-images3.png)

Sur l'écran suivant, procédez comme suit:

 1. Cliquez sur "Check for devices." Cela ouvrira un menu indiquant à quel appareil Trezor (si disponible) vous souhaitez vous connecter
 2. Sélectionnez l'appareil Trezor que vous souhaitez utiliser
 3. Cliquez sur "Connect"

![Trezor Hardware Wallet Connect Wizard Select and Connect Device](/images/trezor/trezor-images4.png)

Une fois votre appareil connecté, vous devez autoriser MetaMask à lire ses clés publiques. Par conséquent, cliquez sur "Autoriser une fois pour cette session". En option, vous pouvez également cocher la case "Ne plus me demander".

![Trezor Hardware Wallet Connect Wizard Allow Read Public Keys](/images/trezor/trezor-images5.png)

Ensuite, il vous est demandé si vous souhaitez exporter la clé publique de votre compte Ethereum (l'onglet a été recadré et étiqueté 1 dans l'image suivante). Juste après, vous êtes invité à utiliser [l'option de phrase secrète de Trezor (onglet rogné et étiqueté 2 dans l'image). Si vous souhaitez utiliser le portefeuille par défaut, cliquez simplement sur "Entrée". A l'inverse, veuillez suivre [Article wiki de Trezor pour les portefeuilles de mots de passe](https://wiki.trezor.io/Passphrase).

![Trezor Hardware Wallet Connect Wizard Allow Export and Passphrase](/images/trezor/trezor-images6.png)

Si MetaMask a réussi à se connecter à votre appareil Trezor, vous devriez voir une liste de cinq comptes de style Ethereum. A l'inverse, veuillez vérifier que vous avez correctement connecté votre appareil Trezor à l'ordinateur et qu'il est déverrouillé. Vous pouvez également répéter le processus avec l'application Trezor Suite ouverte.

À partir de cette liste de cinq comptes Ethereum, procédez comme suit:

 1. Sélectionnez les comptes que vous souhaitez importer depuis votre appareil Trezor
 2. Cliquer sur "Unlock"

![Trezor Select Ethereum Accounts to Import](/images/trezor/trezor-images7.png)

Si vous avez importé avec succès votre compte de style Trezor Ethereum, vous devriez le voir s'afficher sur l'écran principal de MetaMask comme indiqué dans l'image suivante:

![MetaMask Successfully Imported Trezor Account](/images/trezor/trezor-images8.png)

Vous avez maintenant importé avec succès un compte compatible Moonbeam depuis votre appareil Trezor et êtes maintenant prêt à commencer [signer des transactions à l'aide de votre portefeuille matériel](#signing-a-transaction-using-your-trezor).

## Signer une transaction à l'aide de votre Trezor

Si vous avez réussi à [importer votre compte Trezor dans MetaMask](#importing-your-trezor-account-to-metamask), vous êtes prêt à signer des transactions sur Moonbeam à l'aide de votre appareil Trezor. Ce tutoriel va vous montrer comment envoyer une transaction simple sur le Moonbase Alpha TestNet, mais il s'applique aux autres réseaux de l'écosystème Moonbeam.

Tout d'abord, assurez-vous que votre compte Trezor est [financé avec des jetons DEV](/getting-started/moonbase/faucet/). Ensuite, cliquez sur le bouton "Send".

![MetaMask Trezor Account Funded](/images/trezor/trezor-images9.png)

Un onglet `TrezorConnect` devrait apparaître, demandant la permission de lire les clés publiques de votre appareil et de préparer votre Trezor pour la transaction et la signature de données. Une fois que vous êtes prêt, cliquez sur "Allow once for this session (Autoriser une fois pour cette session)"." En option, vous pouvez également cocher la case "Don't ask me again (Ne plus me demander)".

![Trezor Hardware Wallet Allow Read Public Keys and Signing](/images/trezor/trezor-images10.png)

Comme vous le feriez dans une transaction standard, définissez l'adresse du destinataire, entrez le nombre de jetons à envoyer, vérifiez les détails de la transaction et confirmez-la. Cela lancera l'assistant de signature de transaction dans votre appareil Trezor. Ici, suivez les étapes suivantes:

 1. Vérifiez tous les détails de la transaction. Veuillez noter que le token correspond au réseau auquel MetaMask est connecté. **Dans ce cas, il s'agit de jetons DEV et non UNKN!**
 2. Une fois tous les détails vérifiés, maintenez le bouton enfoncé pour confirmer

!!! note
    Au moment de la rédaction, le nom du jeton pour tous les réseaux liés à Moonbeam est toujours affiché comme `UNKN`. Veuillez noter que le token manipulé est celui correspondant au réseau auquel MetaMask est connecté.

![Trezor Hardware Wallet Sign Transaction](/images/trezor/trezor-images11.png)

Juste après avoir approuvé la transaction, MetaMask l'envoie au réseau. Une fois la transaction confirmée, elle sera affichée comme "Envoyer" sur l'écran principal de MetaMask.

![MetaMask Trezor Transaction Wizard](/images/trezor/trezor-images12.png)

Et c'est tout ! Vous avez signé une transaction sur Moonbase Alpha en utilisant votre portefeuille matériel Trezor. 

Le processus d'interaction avec les contrats intelligents à l'aide de votre appareil Trezor est similaire. Assurez-vous de revérifier les données signées sur votre appareil Trezor avant de confirmer la transaction.
