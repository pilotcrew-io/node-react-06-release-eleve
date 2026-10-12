# Tutoriel 06 — Produit · Livrer une réservation reprenable

Le temps indiqué suppose [les prérequis](PREREQUIS.md) terminés. Le [contrat commun](PREREQUIS.md) est la référence pour les champs, routes et limites. Consigner les résultats au fil de l’eau dans [le journal](../travail/REPONSES.md).

## 1. Transformer la demande en critères — 25 min

Reformulez la demande en une phrase centrée sur l’utilisateur : retrouver une réservation fictive après rechargement pour comprendre son état actuel. Écrivez ensuite des critères observables. Une référence conservée déclenche une lecture GET ; un résultat valide affiche les données serveur ; une erreur réseau propose de relire ; une référence absente explique la limite mémoire ; aucun de ces chemins ne crée automatiquement un POST.

Ajoutez le cas d’une tentative dont la réponse initiale avait été perdue et dont aucun ID n’était connu. Elle relève encore de la reprise idempotente de l’atelier 03, avec la clé et l’intention conservées. Distinguez cette situation de la reprise par ID après succès. Une action « reprendre » identique en apparence peut cacher deux opérations différentes ; l’interface et les tests doivent les rendre explicables.

Classez les risques : double retrait, faux succès issu du stockage local, perte du contexte en cas de panne, message incompréhensible au clavier et documentation qui promet une durabilité absente. Choisissez ce qui entrera dans la version du jour. Vous disposez de quatre heures trente ; ajouter comptes, paiement ou base de données sortirait du périmètre.

## 2. Planifier et écrire une première ADR — 40 min

Découpez la tranche en tâches vérifiables : restauration validée, état de relecture, GET par ID, traitement 404, tests de non-création, messages, documentation et revue. Attribuez à chaque tâche sa dépendance et son risque. La planification n’est pas un engagement à tout prix ; elle rend le compromis visible si un incident consomme du temps.

Créez une ADR courte dans votre travail. Donnez un contexte, une décision, deux alternatives et leurs conséquences. Le sujet principal est la conservation d’une référence navigateur avec une revalidation serveur. Comparez-la à une copie locale présentée comme vérité et à une recréation automatique au rechargement. Expliquez pourquoi ces alternatives seraient trompeuses ou appliqueraient un effet supplémentaire.

Précisez la portée du serveur mémoire : un redémarrage perd les réservations et remet les stocks initiaux. Un changement de version du frontend peut aussi modifier la forme du document local. Décidez comment reconnaître une version inconnue et fournir une action explicite. Ne promettez pas de migration silencieuse sans en écrire et en tester les règles.

Définissez la Definition of Done de votre tranche : critères démontrés, tests et build, absence de données réelles, documentation de lancement et incident, revue par une autre personne. L’atelier emprunte des pratiques de travail Agile ; ce rythme condensé ne remplace pas un cycle d’équipe complet ni une expérience de production.

## 3. Construire la reprise par ID — 70 min

Après une confirmation valide, conservez la référence nécessaire à une future lecture dans `pilotcrew.retail.reservation.v1`. À la restauration, validez la structure, les longueurs et les invariants avant tout usage. Ne faites jamais confiance à un statut CONFIRMED ou à un total stocké localement pour afficher une confirmation actuelle. Reconstituez seulement les champs autorisés qui servent à déterminer la prochaine opération.

Introduisez un état « vérification de la réservation » distinct de la confirmation. Si un ID existe, appelez GET /api/reservations/{id}. Le client valide la réponse et la cohérence de sa forme ; le titre et le montant confirmé apparaissent seulement après cette validation. Nettoyez l’effet et protégez les résultats tardifs lorsque la référence change ou que la page est démontée.

En cas de panne réseau, gardez l’ID et proposez « Réessayer la lecture ». En cas de 404 RESERVATION_NOT_FOUND, expliquez que la référence n’existe plus dans le serveur local, notamment après son redémarrage. N’annoncez ni annulation réelle ni remboursement : il n’y a pas eu d’achat. N’envoyez aucun POST pour réparer ce 404. Proposez un retour explicite au catalogue et une nouvelle intention clairement distinguée.

Si une tentative incertaine contient seulement clé et lignes, retrouvez le parcours de l’atelier 03 et son bouton de reprise idempotente. Ne le transformez pas automatiquement en lecture par un ID inventé. Interdisez les mélanges incohérents d’une ancienne clé avec un nouveau panier. La personne doit comprendre ce qui sera rejoué avant d’activer l’action.

Vérifiez que le backend GET reste une lecture sans effet, avec 200 ou 404 normalisé. Les versions publiques de données sont des copies ; relire plusieurs fois ne doit changer ni createdAt, ni total, ni stock. Ajoutez une assertion sur cette absence d’effet. La reprise verticale combine donc un comportement frontend et une garantie serveur que vous pouvez défendre séparément.

## 4. Injecter un incident et vérifier — 50 min

Créez une réservation fictive sur un serveur neuf, conservez son ID, puis rechargez le navigateur sans redémarrer l’API. Observez le GET de reprise, les données relues et l’absence de nouveau POST. Vérifiez aussi que modifier une valeur de total dans le stockage local ne modifie pas le montant confirmé après relecture.

Arrêtez ensuite le seul serveur du laboratoire et relancez-le. Rechargez une deuxième fois. La référence est désormais absente : le comportement attendu est un 404 expliqué et aucune nouvelle réservation automatique. Relevez le stock et les appels pour prouver cette absence d’effet. Répondez dans le journal à la question « peut-on reconstruire la réservation perdue à partir de ce seul état ? » sans inventer une restauration de base de données qui n’existe pas.

Testez un ID mal formé dans le stockage, une réponse serveur incohérente et une panne temporaire. Les messages doivent différencier données locales inutilisables, référence absente et service inaccessible. Parcourez ces états avec le clavier ; aucune action n’est cachée derrière une couleur ou un jargon technique. Un refus de relecture n’efface pas automatiquement toute l’intention de l’utilisateur.

```bash
npm test
npm run build
npm run test:e2e
```

Exécutez les commandes appropriées dans frontend/, puis tests et build dans backend/. Notez séparément ce qui a réellement tourné. Si vous disposez d’une pipeline GitLab fonctionnelle, attendez son résultat sur le commit livré ; un ancien vert sur un autre commit ne couvre pas la modification.

## 5. Faire relire et préparer la livraison — 55 min

Rédigez un runbook qui permet à une autre personne de lancer les deux projets depuis leurs lockfiles, vérifier leur disponibilité et diagnostiquer trois incidents : API injoignable, réservation absente après redémarrage et stockage navigateur corrompu. Chaque entrée précise symptôme, contrôle non destructif, interprétation, action et preuve de rétablissement. N’utilisez pas « redémarrer tout » comme réponse universelle : le redémarrage détruit justement les données mémoire.

Écrivez les notes de version : besoin traité, comportement ajouté, incompatibilités éventuelles de stockage, commandes vérifiées, problèmes connus et limites. L’ADR doit expliquer le choix avec des conséquences concrètes. Le journal de performance reste attaché à son protocole ; ne recyclez pas une mesure ancienne comme preuve de toute la nouvelle version.

Préparez une merge request ou une revue locale identifiable. La distribution du cursus utilise des forks GitHub ; la même description de problème, changement et preuve convient à une revue GitLab lorsque vous y exécutez la CI. Le modèle de revue fourni sert de trame. Faites vérifier par un binôme une assertion métier et un parcours clavier ; consignez sa remarque et votre réponse.

Démontrez en cinq minutes : catalogue et panier, réservation, rechargement avec GET, redémarrage suivi d’un 404 utile, puis preuve que le stock n’a pas été réservé une deuxième fois. Ouvrez enfin le rapport du commit livré. Ne transformez pas la démo en visite de tous les fichiers ; chaque écran doit répondre à un critère annoncé.

## 6. Évaluer la progression et remettre — 30 min

Rendez les documents dans votre espace de travail, le code de la tranche, les tests et les preuves. Séparez ce que vous avez construit, ce qui provenait d’une base de rattrapage et ce qui a été aidé pendant la séance. Cette distinction permet de choisir une prochaine étape adaptée au lieu d’attribuer une compétence sur la seule présence de code.

Écrivez un bilan en trois parties : une décision que vous savez défendre, un incident que vous savez reproduire et une limite que vous ne savez pas encore traiter. Proposez un plan de progression avec un exercice mesurable pour chaque limite : base transactionnelle et concurrence, accessibilité avec utilisateurs, observabilité ou vraie exploitation CI selon votre besoin.

Les vingt-sept heures constituent un cursus appliqué, orienté React avec un backend Node cohérent. L’offre d’inspiration vise une expérience professionnelle d’au moins sept ans ; cette durée ne peut pas être obtenue par un atelier. Votre dossier démontre plutôt une méthode de construction, de vérification et de communication, utile pour la suite de votre pratique.
