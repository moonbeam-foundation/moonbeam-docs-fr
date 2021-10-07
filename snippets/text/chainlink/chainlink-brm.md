Un nœud Oracle possède un ensemble de JobIDs, où chacun correspond à une tâche qui peut être demandée par un utilisateur, par exemple, récupérer un flux de prix. Pour ce faire, l'utilisateur doit envoyer une demande via un contrat, nous l'appellerons le contrat _Client_ en transmettant les informations suivantes:

 - Adresse Oracle : adresse du contrat déployé par le nœud Oracle
 - Job ID: tâche à exécuter
 - Paiement: paiement en jetons LINK que l'Oracle recevra pour répondre à la demande

Cette demande envoie en fait un _transferAndCall_ au contrat de jeton LINK, qui gère le paiement et relaie la demande au contrat Oracle. Ici, un événement est émis avec la requête, qui est capté par le nœud Oracle. Ensuite, le nœud récupère les données nécessaires et exécute la fonction _fulfilOracleRequest_ , qui exécute un rappel qui stocke les informations demandées dans le contrat Client. Le schéma suivant explique ce flux de travail.

![Basic Request Diagram](/images/builders/integrations/oracles/chainlink/chainlink-basic-request.png)
