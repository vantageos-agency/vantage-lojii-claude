---
name: point-hebdomadaire
description: >
  Préparer le point hebdomadaire du cabinet à partir de Lojii : échéances de mandats, vacants, ordres de service et honoraires. Prepare the agency's weekly briefing from Lojii: mandate expiries, vacancies, work orders and fees.
  Use when the user asks for a weekly briefing, a weekly agency review, or what to look at this week (point de la semaine).
user-invocable: true
---

## Français

Pour composer, en un seul message, le point de la semaine du cabinet, à partir de plusieurs
services Lojii. Chaque rubrique dit de quel service elle vient.

### Services Lojii utilisés

- **Mandats de gestion** : outil `list_management_mandates`. Documentation « Lojii - Les API disponibles », section 5. Gérance locative ; spécification openapi-1.0.0.json · /api/proprietaire/v1/mandat_gestion (ensemble des mandats de l'agence). Mandats de gestion dont la date de fin tombe dans l'horizon choisi.
- **Mandats de copropriété** : outil `list_syndic_mandates`. Documentation « Lojii - Les API disponibles », section 4. Syndic de copropriété ; spécification openapi-1.0.0.json · /api/api/v1/mandat_copropriete. Mandats de copropriété dont la date de fin tombe dans l'horizon choisi.
- **Contrats locataires** : outil `list_tenant_contracts`. Documentation « Lojii - Les API disponibles », section 5. Gérance locative ; spécification openapi-1.0.0.json · /api/suivilocataire/v1/contrat_locataire. Les lots vacants (type Vacant).
- **Ordres de service** : outil `list_work_orders`. Documentation « Lojii - Les API disponibles », section 5. Gérance locative ; spécification openapi-1.0.0.json · /api/api/v1/os_gerance. Les ordres de service créés dans la semaine.
- **Honoraires facturés**, **Paiements d'honoraires** : outil `get_fee_totals`. Documentation « Lojii - Les API disponibles », section 6. Facturation et honoraires du cabinet ; spécification openapi-1.0.0.json · /api/api/v1/honoraire_facture ("kind" "invoiced") et /api/api/v1/honoraire_paiement ("kind" "paid"). Le total facturé et le total encaissé de la semaine, calculés par le serveur.

### Ce qu'il faut avoir avant d'appeler

Demander en une seule question, puis ne plus interrompre :

1. la semaine (par défaut : la semaine en cours ; en annoncer les dates) ;
2. l'horizon des échéances de mandats (par exemple 90 jours) : ne pas le choisir à la place
   de l'utilisateur ;
3. le ou les codes d'activité pour les honoraires (« Type d'activité (Syndic ou
   Gérance) », correspondance non définie par la documentation). Sans code, la rubrique
   honoraires est omise et le point dit pourquoi.

### Composer le point

Une rubrique par service, dans cet ordre, chacune avec la source entre parenthèses :

1. Échéances des mandats de gestion (Mandats de gestion) : numéro, date de fin, état.
2. Échéances des mandats de syndic (Mandats de copropriété) : copropriété, date de fin,
   état.
3. Lots vacants (Contrats locataires) : lots, « Date de disponibilité (si vacant) »,
   « Raison de non-location ».
4. Ordres de service de la semaine (Ordres de service) : appeler avec "created_from" et
   "created_to" aux dates de la semaine (filtre ajouté par le connecteur) ; titre, état,
   date de création, immeuble.
5. Honoraires de la semaine (Honoraires facturés, Paiements d'honoraires) : deux appels
   `get_fee_totals`, "kind" "invoiced" (facturé) puis "kind" "paid" (encaissé), avec
   "activity" et "date_from" / "date_to" aux dates de la semaine ; total facturé et total
   encaissé, côte à côte. Un chiffre n'est le total de la semaine que si "scope" vaut
   "complete". Sur "scope" "partial", l'écrire « total partiel, non disponible pour la
   semaine » et rappeler avec les mêmes entrées et le "cursor" renvoyé ; sur un refus
   [neoteem_ceiling], rappeler avec les mêmes entrées (et son "retry_cursor" s'il en
   porte un). Ne jamais présenter une somme partielle comme le total.

Une rubrique vide se dit « rien renvoyé » ; une rubrique refusée se dit « refusé », avec la
raison. Ne pas ajouter de priorité, de recommandation ou d'alerte que les données ne
portent pas.

### Ce que la documentation ne définit pas

- Ce qui est « urgent », « à risque » ou « en retard » : non défini par la documentation.
- Le reste à encaisser (facturé moins encaissé) : non défini par la documentation ; donner
  les deux totaux.

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

To compose, in one message, the agency's weekly briefing from several Lojii services. Each
section says which service it comes from.

### Lojii services used

- **Mandats de gestion**: tool `list_management_mandates`. Documentation "Lojii - Les API disponibles", section 5. Gérance locative; specification openapi-1.0.0.json · /api/proprietaire/v1/mandat_gestion (the agency's whole set of mandates). Management mandates whose end date falls within the chosen horizon.
- **Mandats de copropriété**: tool `list_syndic_mandates`. Documentation "Lojii - Les API disponibles", section 4. Syndic de copropriété; specification openapi-1.0.0.json · /api/api/v1/mandat_copropriete. Condominium mandates whose end date falls within the chosen horizon.
- **Contrats locataires**: tool `list_tenant_contracts`. Documentation "Lojii - Les API disponibles", section 5. Gérance locative; specification openapi-1.0.0.json · /api/suivilocataire/v1/contrat_locataire. Vacant lots (Vacant kind).
- **Ordres de service**: tool `list_work_orders`. Documentation "Lojii - Les API disponibles", section 5. Gérance locative; specification openapi-1.0.0.json · /api/api/v1/os_gerance. Work orders created during the week.
- **Honoraires facturés**, **Paiements d'honoraires**: tool `get_fee_totals`. Documentation "Lojii - Les API disponibles", section 6. Facturation et honoraires du cabinet; specification openapi-1.0.0.json · /api/api/v1/honoraire_facture ("kind" "invoiced") and /api/api/v1/honoraire_paiement ("kind" "paid"). The week's invoiced total and collected total, computed by the server.

### What you need before calling

Ask in a single question, then do not interrupt again:

1. the week (default: the current week; state its dates);
2. the horizon for mandate expiries (for example 90 days): do not pick it for the user;
3. the activity code(s) for fees ("Type d'activité (Syndic ou Gérance)", mapping not defined by the documentation). Without a code the fee section is omitted and the
   briefing says why.

### Composing the briefing

One section per service, in this order, each with its source in brackets:

1. Management mandate expiries (Mandats de gestion): number, end date, status.
2. Syndic mandate expiries (Mandats de copropriété): condominium, end date, state.
3. Vacant lots (Contrats locataires): lots, "Date de disponibilité (si vacant)", "Raison
   de non-location".
4. This week's work orders (Ordres de service): call with "created_from" and
   "created_to" set to the week's dates (a filter the connector adds); title, state,
   creation date, building.
5. This week's fees (Honoraires facturés, Paiements d'honoraires): two `get_fee_totals`
   calls, "kind" "invoiced" (invoiced) then "kind" "paid" (collected), with "activity" and
   "date_from" / "date_to" set to the week's dates; invoiced total and collected total, side
   by side. A figure is the week's total only when "scope" is "complete". On "scope"
   "partial", write "partial total, not available for the week" and call again with the
   same inputs and the "cursor" returned; on a [neoteem_ceiling] refusal, call again with
   the same inputs (and its "retry_cursor" if it carries one). Never present a partial sum
   as the total.

An empty section reads "nothing returned"; a refused section reads "refused", with the
reason. Do not add a priority, recommendation or alert the data does not carry.

### What the documentation does not define

- What is "urgent", "at risk" or "late": not defined by the documentation.
- Remaining to collect (invoiced minus collected): not defined by the documentation; give
  both totals.

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
