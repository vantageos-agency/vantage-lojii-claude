# Lojii pour Claude

[Français](#français) | [English](#english)

## Français

Lojii dans Claude : consultez les données Lojii de votre cabinet en langage courant — acteurs et immeubles, lots, portefeuilles, mandats de gestion et de syndic, baux et vacants, comptes locataires et propriétaires, budgets, contrats fournisseurs, ordres de service, dossiers de copropriété et honoraires du cabinet. Lecture seule ; chaque personne se connecte avec son propre accès Lojii.

Dans claude.ai : **Customize > Plugins > Add marketplace** (Personnaliser > Plugins >
Ajouter une marketplace), collez `https://github.com/vantageos-agency/vantage-lojii-claude`, puis installez **lojii**.

Ce que vous obtenez :

- Le connecteur Lojii (`https://lojii.vantagepeers.com/mcp`). Claude vous demande de vous connecter la
  première fois qu'il l'utilise, avec votre propre accès Lojii : vous voyez les données
  de votre cabinet, selon vos droits. Aucune clé n'est stockée dans le plugin.
- Lecture seule : le plugin ne modifie rien dans Lojii.
- Compétences, une par métier du cabinet :
  - `demarrer`: Démarrer avec Lojii : vérifier que le connecteur Lojii répond et dire ce qu'il permet de consulter.
  - `trouver-un-acteur`: Trouver une personne, une société ou un immeuble dans Lojii et lire sa fiche d'identité.
  - `lots-d-un-immeuble`: Lister les lots d'un immeuble, ou retrouver un lot précis, dans Lojii.
  - `portefeuilles`: Lister les portefeuilles de gestion du cabinet, leurs agences et les collaborateurs qui les gèrent.
  - `mandats-et-echeances`: Consulter les mandats de gestion et de syndic et leurs échéances dans Lojii.
  - `baux-et-vacants`: Consulter les baux, les lots vacants et les candidatures dans Lojii.
  - `compte-locataire`: Lire la situation comptable d'un locataire dans Lojii : quittancé, réglé, reste à payer.
  - `compte-proprietaire`: Lire les écritures du compte d'un propriétaire dans Lojii.
  - `budgets`: Consulter les budgets propriétaire dans Lojii : prévu, appelé, réalisé, et le détail par rubrique.
  - `contrats-fournisseurs`: Consulter les contrats fournisseurs de la gérance dans Lojii.
  - `ordres-de-service`: Suivre les ordres de service et les travaux de la gérance dans Lojii.
  - `dossier-copropriete`: Ouvrir le dossier d'une copropriété dans Lojii : fiche, copropriétaires, conseil syndical, mandat de syndic.
  - `honoraires-du-cabinet`: Suivre les honoraires du cabinet dans Lojii : facturés, lettrés, encaissés, sur une période.
  - `point-hebdomadaire`: Préparer le point hebdomadaire du cabinet à partir de Lojii : échéances de mandats, vacants, ordres de service et honoraires.

Pour commencer, demandez « Qu'est-ce que Lojii peut faire pour moi ? » ou lancez la
compétence `demarrer`.

À savoir :

- Les mandats de gestion se cherchent par l'identifiant de l'immeuble, pas par son nom :
  Claude retrouve l'identifiant lui-même. Une valeur que Lojii ne fournit pas (un taux
  d'honoraires ou de GLI, par exemple) s'affiche « non fourni par Lojii », jamais « 0 ».
- Ordres de service : filtres par état, période de création et immeuble. Lojii ne les
  propose pas : le connecteur lit la liste entière (une dizaine de secondes) puis filtre.

Les compétences s'appuient sur la documentation officielle « Lojii - Les API
disponibles » et sur la spécification OpenAPI de l'éditeur (openapi-1.0.0.json). Quand
la documentation ne définit pas une notion, Claude le dit au lieu de l'interpréter.

Support : hello@vantageos.agency. Licence propriétaire : usage réservé aux clients sous contrat, voir `LICENSE`.

## English

Lojii for Claude: read your agency's Lojii data in plain words — people and buildings, lots, portfolios, management and syndic mandates, leases and vacancies, tenant and owner accounts, budgets, supplier contracts, work orders, condominium files and agency fees. Read-only; each person signs in with their own Lojii access.

Add it in claude.ai: **Customize > Plugins > Add marketplace**, paste
`https://github.com/vantageos-agency/vantage-lojii-claude`, then install **lojii**.

What it gives you:

- The Lojii connector (`https://lojii.vantagepeers.com/mcp`). Claude asks you to sign in the first time
  it uses it, with your own Lojii access: you see your agency's data, within your
  rights. No key is stored in the plugin.
- Read-only: the plugin changes nothing in Lojii.
- Skills: `demarrer`, `trouver-un-acteur`, `lots-d-un-immeuble`, `portefeuilles`, `mandats-et-echeances`, `baux-et-vacants`, `compte-locataire`, `compte-proprietaire`, `budgets`, `contrats-fournisseurs`, `ordres-de-service`, `dossier-copropriete`, `honoraires-du-cabinet`, `point-hebdomadaire`.

Good to know:

- Management mandates are searched by building identifier, not by building name: Claude
  looks the identifier up itself. A value Lojii does not provide (a management fee or GLI
  rate, for example) is shown as "not provided by Lojii", never as "0".
- Work orders: filters by state, creation period and building. Lojii does not offer them:
  the connector reads the whole list (about ten seconds), then filters.

The skills follow the official documentation "Lojii - Les API disponibles" and the
publisher's OpenAPI specification (openapi-1.0.0.json). Where the documentation does
not define a notion, Claude says so instead of interpreting it.

Support: hello@vantageos.agency. Proprietary licence: use reserved to clients under contract, see `LICENSE`.
