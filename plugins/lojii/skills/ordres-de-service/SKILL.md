---
name: ordres-de-service
description: >
  Suivre les ordres de service et les travaux de la gérance dans Lojii. Follow the management's work orders and works in Lojii.
  Use when the user asks for work orders, repairs, works in progress, or the works on a building or for a tenant.
user-invocable: true
---

## Français

Pour lister les ordres de service et suivre l'avancement des travaux.

### Service Lojii utilisé

- **Ordres de service** : outil `list_work_orders`. Documentation « Lojii - Les API disponibles », section 5. Gérance locative ; spécification openapi-1.0.0.json · /api/api/v1/os_gerance. La documentation : « Ordres de service et suivi des travaux : intitulé, état d'avancement, dates de création, de démarrage et de réception, avec les acteurs associés (immeuble, locataire, propriétaire, agence, gestionnaire) ».

### Ce qu'il faut avoir avant d'appeler

Aucune entrée obligatoire. Facultatif : un terme de recherche d'au moins deux caractères
(« Terme de recherche global appliqué sur les libellés, références et noms des acteurs (insensible aux accents) »). Le service Lojii n'a ni filtre d'état, ni filtre de date
de création, ni filtre par immeuble ; le connecteur les ajoute :

- "state" : un libellé d'état, comparé exactement (sans casse ni accent) ; reprendre un
  libellé tel que Lojii l'a renvoyé, jamais un libellé deviné ;
- "created_from" et "created_to" : la période de création (AAAA-MM-JJ) ;
- "building_id" : l'identifiant de l'immeuble (trouvé par la compétence lots-d-un-immeuble
  ou trouver-un-acteur), jamais son nom ;
- "newest_first" : du plus récent au plus ancien (par défaut dès qu'un filtre est posé).

Avec l'un de ces filtres, le connecteur lit la liste entière une fois (une dizaine de
secondes) puis filtre ; une liste qui ne peut pas être lue en entier est refusée, jamais
servie tronquée. Pour « les ordres de la semaine », passer "created_from" et "created_to" et
annoncer les dates.

### Lire la réponse

Par ordre : "title", "state" (l'état d'avancement, tel que Lojii le nomme),
"created_on", "works_start_on" (date de démarrage), "received_on" (date de réception),
"building", "tenant" (« locataire occupant si applicable »), "owner", "client"
(« client souscripteur »), "agency", "mandate".

### Ce que la documentation ne définit pas

- Un ordre « en retard », un délai d'intervention ou une date prévue de fin : non défini par la documentation. Donner les dates renvoyées.
- La liste des valeurs d'état : non défini par la documentation ; la rapporter telle
  quelle.

### Si l'outil refuse

Un refus n'est jamais « aucun résultat » : le dire comme un refus, en mots simples. L'outil
renvoie un message suivi d'un code entre crochets.

- [filter_required] : une entrée obligatoire manque. Demander à l'utilisateur l'une des
  entrées listées dans "accepted" (ou toutes celles listées dans "missing"), puis relancer.
- [criteria_required] : aucun critère de recherche n'a été donné ; en demander un.
- [not_found] : aucun enregistrement ne correspond à cet identifiant.
- [rate_limited] ou [neoteem_rate_limited] : trop d'appels en peu de temps ; réessayer dans
  un instant.
- [neoteem_ceiling] : le résultat est trop volumineux ; resserrer la recherche (une période
  plus courte, un immeuble précis).
- [neoteem_conflicting_rows] : Lojii a renvoyé le même identifiant avec deux contenus
  différents ; rien n'est servi ; le signaler à l'administrateur Lojii du cabinet.
- [neoteem_unauthorized] : Lojii a refusé l'accès ; contacter l'administrateur du cabinet.
- Autre code commençant par [neoteem_ (Lojii indisponible, réponse illisible) : réessayer
  plus tard, sans inventer de réponse.
- Si le connecteur Lojii n'est pas connecté, ou si Claude demande une connexion : inviter
  la personne à se connecter avec son propre accès Lojii (réglages des connecteurs de
  Claude). Elle voit les données de son cabinet, selon ses droits.

### Règles

1. Ne rapporter que ce que l'outil a renvoyé : aucun nom, aucune date, aucun montant qui ne
   vienne pas de la réponse.
2. Donner à chaque champ le sens que la documentation lui donne (cité ci-dessus). Quand la
   documentation ne dit rien d'un champ ou d'une notion, écrire « non défini par la documentation », jamais une interprétation.
3. Ne jamais inventer une entrée obligatoire (un identifiant, une période, un code) : la
   demander. Un nom donné par l'utilisateur se transforme en identifiant par un appel, pas
   par une supposition.
4. Une réponse marquée "truncated" est partielle : le dire et proposer la page suivante
   ("nextCursor"). Un "upstream_defect" signifie que Lojii a renvoyé des doublons, retirés
   par le connecteur : le dire.
5. Lecture seule : ce connecteur ne modifie rien dans Lojii.

## English

To list work orders and follow the progress of works.

### Lojii service used

- **Ordres de service**: tool `list_work_orders`. Documentation "Lojii - Les API disponibles", section 5. Gérance locative; specification openapi-1.0.0.json · /api/api/v1/os_gerance. The documentation: "Ordres de service et suivi des travaux : intitulé, état d'avancement, dates de création, de démarrage et de réception, avec les acteurs associés (immeuble, locataire, propriétaire, agence, gestionnaire)".

### What you need before calling

No required input. Optional: a search term of at least two characters ("Terme de recherche global appliqué sur les libellés, références et noms des acteurs (insensible aux accents)"). The Lojii service has no state filter, no creation-date filter and no building
filter; the connector adds them:

- "state": a state label, matched exactly (case and accent-insensitive); reuse a label as
  Lojii returned it, never a guessed one;
- "created_from" and "created_to": the creation period (YYYY-MM-DD);
- "building_id": the building identifier (found with the lots-d-un-immeuble or
  trouver-un-acteur skill), never its name;
- "newest_first": newest first (the default once a filter is set).

With any of these filters the connector reads the whole list once (about ten seconds) and
then filters; a list that cannot be read whole is refused, never served cut. For "this
week's orders", pass "created_from" and "created_to" and state the dates.

### Reading the answer

Per order: "title", "state" (progress, as Lojii names it), "created_on",
"works_start_on" (start date), "received_on" (acceptance date), "building", "tenant"
("locataire occupant si applicable"), "owner", "client" ("client souscripteur"),
"agency", "mandate".

### What the documentation does not define

- A "late" order, a response time or a planned end date: not defined by the documentation. Give the dates returned.
- The list of state values: not defined by the documentation; report it as is.

### If the tool refuses

A refusal is never "no results": say it is a refusal, in plain words. The tool returns a
message followed by a code in square brackets.

- [filter_required]: a required input is missing. Ask the user for one of the inputs listed
  in "accepted" (or all of those listed in "missing"), then call again.
- [criteria_required]: no search criterion was given; ask for one.
- [not_found]: no record matches this identifier.
- [rate_limited] or [neoteem_rate_limited]: too many calls in a short time; try again in a
  moment.
- [neoteem_ceiling]: the result is too large; narrow the search (a shorter period, one
  building).
- [neoteem_conflicting_rows]: Lojii returned the same identifier with two different
  contents; nothing is served; report it to the agency's Lojii administrator.
- [neoteem_unauthorized]: Lojii refused access; contact the agency's administrator.
- Any other code starting with [neoteem_ (Lojii unavailable, unreadable answer): try again
  later, without inventing an answer.
- If the Lojii connector is not connected, or Claude asks to connect: invite the person to
  sign in with their own Lojii access (Claude's connector settings). They see their own
  agency's data, within their rights.

### Rules

1. Report only what the tool returned: no name, date or amount that is not in the answer.
2. Give each field the meaning the documentation gives it (cited above). Where the
   documentation says nothing about a field or a notion, write "not defined by the documentation", never an interpretation.
3. Never invent a required input (an identifier, a period, a code): ask for it. A name the
   user gives becomes an identifier through a call, not through a guess.
4. An answer marked "truncated" is partial: say so and offer the next page ("nextCursor").
   An "upstream_defect" means Lojii returned duplicates, which the connector removed: say so.
5. Read-only: this connector changes nothing in Lojii.
