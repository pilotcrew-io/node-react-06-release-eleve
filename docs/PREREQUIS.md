# Prérequis et reprise avant la séance

- Importer avant la séance le code et la chaîne de qualité de l’atelier 05.
- Disposer d’une réservation idempotente, de tests et d’un journal de preuves.
- Connaître les limites du stockage mémoire et la portée du stockage navigateur.

Lire [Day 0](../DAY0.md). La préparation, les installations et l’import des acquis se font **avant** les 270 minutes. Le formateur vérifie que chacun dispose d’un point de départ utilisable.

## Reprise depuis nr-05-quality

Cloner le nouveau fork de **node-react-06-release-eleve**, puis copier ses propres backend/, frontend/, scripts/ et configurations utiles depuis le commit final de nr-05-quality. Copier les lockfiles. Garder les documents du présent module. Ne pas transférer .git, node_modules, dist, rapports, fichiers temporaires ou secrets. Noter le dépôt, le commit et les chemins importés dans [le journal](../travail/REPONSES.md).

Dans chacun des projets importés, lancer npm ci, npm test et npm run build selon les scripts déjà construits. Corriger les défauts de transfert avant l’horloge, puis faire un commit de reprise. Toute base de rattrapage reçue du formateur doit être nommée comme telle.

## Contrat commun du magasin fictif

**PilotCrew Retail** est un laboratoire local : 30 produits inventés, `retail-001` à `retail-030`, dix dans chacune des catégories `maison`, `bureau`, `plein-air`. Chaque produit commence avec un stock de 20. Aucun paiement, compte client, prestataire de paiement, livraison ou achat réel n’est réalisé. Tous les montants sont des entiers en centimes EUR ; `priceCents: 2500` signifie 25,00 € fictifs. Les images externes sont absentes du socle pour garder une mesure reproductible.

| Objet | Champs du contrat |
|---|---|
| Produit | `id`, `name`, `description`, `category`, `priceCents`, `stock` |
| Page de produits | `items`, `total`, `page`, `pageSize` |
| Intention de réservation | `items`, tableau de lignes `{productId, quantity}` |
| Réservation | `id`, `status: CONFIRMED`, `items`, `totalCents`, `currency: EUR`, `createdAt` |
| Ligne confirmée | `productId`, `name`, `quantity`, `unitPriceCents`, `lineTotalCents` |

| Route | Résultat attendu |
|---|---|
| `GET /api/products` | 200, page ; défauts : page 1, 12 éléments, tri name-asc |
| `GET /api/products/{id}` | 200, produit actuel ; 404 PRODUCT_NOT_FOUND |
| `POST /api/reservations` | Dès l’atelier 03 : 201 et Location ; clé Idempotency-Key obligatoire |
| `GET /api/reservations/{id}` | Dès l’atelier 03 : 200, réservation ; 404 RESERVATION_NOT_FOUND |

La recherche `q` contient au plus 80 caractères après retrait des espaces en bordure et ignore casse et accents dans le nom et la description. `category` accepte uniquement les trois valeurs ci-dessus ; omettre le paramètre pour toutes les catégories. `page` est un entier de 1 à 10000 et `pageSize` de 1 à 24, écrits en chiffres ASCII sans zéro initial. `sort` accepte `name-asc`, `price-asc`, `price-desc` ; l’ID départage les égalités. Paramètres inconnus, répétés, structurés et valeurs invalides produisent 400 INVALID_QUERY. Une page au-delà des résultats reste une page valide, avec `items: []`.

Une réservation accepte **1 à 10 produits distincts, quantité entière de 1 à 5 par produit**, sans champ de prix, remise, devise ou statut envoyé par le client. La clé comprend 8 à 80 caractères ASCII alphanumériques ou tirets ; un UUID convient. L’ordre des lignes ne change pas l’intention. Même clé et mêmes couples produit/quantité : même réponse 201, même ID, aucun retrait supplémentaire. Même clé et intention différente : 409 IDEMPOTENCY_CONFLICT. Stock insuffisant : 409 INSUFFICIENT_STOCK, sans aucune écriture ni consommation de la clé. Toutes les lignes sont contrôlées avant de diminuer le moindre stock.

Le format d’erreur est `{error: {code, message}}`. Le backend normalise 400 INVALID_REQUEST/INVALID_JSON, 404, 405 METHOD_NOT_ALLOWED avec Allow, 413 PAYLOAD_TOO_LARGE au-delà de 16 KiB, 415 UNSUPPORTED_MEDIA_TYPE, 409 et 503 CAPACITY_REACHED à 500 réservations uniques. Le code peut expliquer l’erreur sans exposer une trace technique. Le plafond laisse rejouer les intentions déjà enregistrées.

Le serveur écoute `127.0.0.1:8080`. Vite écoute `127.0.0.1:5173` et relaie `/api`. Réservations et stocks restent en mémoire dans **un seul processus Node**. L’opération métier demeure synchrone, sans await entre vérification et écriture. Elle n’est ni une transaction durable, ni un verrou entre plusieurs processus. Le redémarrage perd les réservations et réinitialise les stocks ; aucune expiration ni annulation de réservation n’est implémentée.
