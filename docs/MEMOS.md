# Mémos — Produit · Livrer une réservation reprenable

À relire pour expliquer les décisions ; les mémos ne remplacent pas les preuves de [l’évaluation](EVALUATION.md).

## Reprendre par lecture

Une référence connue permet de demander au serveur ce qu’il connaît encore. Le GET de reprise n’est pas une création, même si l’écran est visuellement identique à la confirmation initiale. Les données persistées dans le navigateur aident à retrouver cette référence ; elles ne justifient pas d’afficher un état terminal sans revalidation.

## Deux sortes de reprise

Après réponse perdue sans ID, la même clé et la même intention permettent un rejeu idempotent. Après réponse reçue avec ID, une lecture suffit. Confondre ces situations peut produire un POST supplémentaire ou demander une référence inexistante. Une machine d’états nommée rend explicite ce que le prochain bouton fera réellement.

## Incident de mémoire

Redémarrer le processus efface les réservations. Le 404 de reprise est alors cohérent avec le contrat. Il ne signifie ni paiement annulé ni remboursement, puisque le domaine ne traite aucun achat. Le runbook doit expliquer cette limite, conserver les traces utiles et éviter une nouvelle création automatique présentée comme une réparation transparente.

## ADR et alternatives

Une ADR mémorise pourquoi une décision a été prise dans un contexte donné. Elle nomme les alternatives et les conséquences, y compris les contraintes qu’on accepte. Elle n’a pas besoin d’un long historique. Une décision sur la revalidation serveur devient utile quand une autre personne envisage de remplacer ce GET par un affichage local instantané.

## Runbook et preuve de rétablissement

Un runbook part d’un symptôme observable et propose des contrôles qui évitent d’aggraver la situation. Il distingue API inaccessible, référence absente et document local inutilisable. L’action de correction se termine par une vérification. « Relancer et espérer » ne donne pas la même assurance qu’un GET identifié et un état d’interface cohérent.

## Livraison et expérience

Une livraison comporte un problème, un changement, des preuves et des limites. Les tests s’attachent à un commit, la revue à une décision, la mesure à un protocole. Le cursus aide à pratiquer ces liens. Il n’attribue pas automatiquement un niveau senior et n’efface pas la différence entre une simulation locale et les contraintes d’un système exploité par une équipe.
