# Évaluation et preuves

Le barème porte sur ce qui est **démontré et expliqué**. Une suite non exécutée ou une capture absente n’est pas un succès implicite. Les choix internes peuvent différer du corrigé si le contrat et les critères sont respectés.

| Critère | Points / 20 | Preuve attendue |
| --- | --- | --- |
| Critères et plan | 3 | Tranche définie, risques et dépendances explicites. |
| Reprise verticale correcte | 4 | ID connu relu par GET sans nouvelle création. |
| Incidents et données non fiables | 4 | 404, panne et corruption traités sans faux succès. |
| Tests et accessibilité | 3 | Non-création, revalidation et parcours clavier vérifiés. |
| ADR, runbook et revue | 3 | Documents actionnables et remarque de revue traitée. |
| Démonstration et progression | 3 | Commit livré, preuves réelles et niveau visé formulé honnêtement. |

## Questions d’explication

1. Quand la reprise doit-elle utiliser GET et quand peut-elle rejouer un POST ?
2. Pourquoi un statut CONFIRMED stocké localement ne suffit-il pas ?
3. Que signifie le 404 après redémarrage dans ce laboratoire ?
4. Quel contrôle faut-il effectuer avant de recommander un redémarrage ?
5. Que démontre le cursus et que ne permet-il pas d’affirmer sur l’expérience professionnelle ?

## Format de preuve

Pour chaque contrôle : commit, environnement, commande ou action, résultat attendu, résultat observé, interprétation et lien vers le rapport/capture. Séparer backend, frontend, navigateur et éventuelle CI distante. Pour une mesure : conserver aussi protocole et valeurs brutes.

Un invariant critique non respecté reste non acquis même si les points de présentation sont obtenus. L’évaluation est formative ; elle n’atteste ni une mise en production, ni un niveau senior, ni la sécurité d’un service public.
