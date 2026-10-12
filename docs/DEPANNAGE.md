# Dépannage raisonné

Reproduire le symptôme sur un état connu, lire la réponse ou la trace, formuler une hypothèse puis effectuer un contrôle ciblé. Ne pas effacer tout le projet pour faire disparaître l’erreur.

| Symptôme | Hypothèse à vérifier | Contrôle et correction |
| --- | --- | --- |
| Succès affiché immédiatement après recharge | Copie locale considérée comme vérité | Passer par un état de vérification puis valider GET. |
| Nouveau stock retiré à chaque reprise | POST utilisé après un ID connu | Reprendre par lecture seule et compter les créations. |
| 404 corrigé par recréation automatique | Confusion absence et panne réparable | Expliquer la perte mémoire ; nouvelle intention seulement explicite. |
| Ancienne clé associée à un panier modifié | Tentative et panier mélangés | Conserver une intention figée et séparer les nouvelles actions. |
| Runbook détruit les données utiles | Redémarrage proposé avant diagnostic | Lire les statuts, références et journaux avant action destructive. |
| Pipeline verte sur mauvais commit | Rapport ancien utilisé comme preuve | Rattacher chaque résultat au SHA livré. |
| Démonstration trop large | Visite de code sans critères | Montrer les cinq événements utiles du scénario de reprise. |

Si le blocage concerne une installation manquante du Day 0, le noter séparément du résultat métier. Demander une aide ciblée après avoir inscrit la commande, le statut et le message utile dans le journal.
