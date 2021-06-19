Avant qu'une proposition ne soit adoptée, elle doit passer par différentes étapes. Le flux général d'une proposition est le suivant (quelques simplifications ont été apportées)

 - Les détenteurs de jetons doivent créer une pré-image de la proposition, qui définit l'action à réaliser. Le déposant paie des frais par octet stocké : plus la pré-image est grande, plus les frais sont élevés. Une fois soumis, il renvoie un hachage de préimage
 - Les détenteurs de jetons peuvent soumettre la proposition en utilisant le hachage de préimage, verrouillant les jetons dans le processus. Une fois la transaction de soumission acceptée, la proposition est répertoriée publiquement
 - Une fois la proposition répertoriée, les détenteurs de tokens peuvent appuyer la proposition (se porter garant) en verrouillant le même nombre de tokens que l'auteur de la proposition d'origine a verrouillé
 - La proposition la plus appuyée passe au référendum public
 - Une fois en référendum, les détenteurs de jetons votent "Aye" ou "Nay" sur la proposition en verrouillant des jetons. Deux facteurs expliquent le poids du vote : le montant bloqué et la période de blocage
 - Si la proposition est acceptée, elle est promulguée après un certain temps

![Proposal Roadmap](/images/governance/governance-proposal-roadmap.png)
