# Journal des mises a jour

Chaque entree note ce qui a ete integre a la documentation. Le plus recent reste en haut.

## 2026-07-08 — commit `c8b6cd0` → `1d5412a`

Feature 019 : import/export CSV representant, avec source/URL/justification par cellule editable, et
retrait de la file de validation admin pour les contributions.

- Reecrit [Contribution](#/contribution) : nouvelle section « Import / export CSV (representant) »
  (round-trip export -> edition hors-ligne -> import strict -> publication atomique via
  `publish_representative_import`), retrait complet de l'ancien circuit d'observation
  pending/approved/rejected (supprime), et correction du perimetre d'overwrite (fractale exacte, plus
  d'heritage par prefixe).
- Reecrit [Rôles & contributions](#/roles-et-contributions) : le tableau des roles n'a plus de ligne
  de validation admin ; nouvelle section sur le perimetre exact d'un representant (spec 019, aucun
  heritage vers les descendants) ; note sur l'heritage en lecture seule des seuils/limites.
- Reecrit [Carte de l'app](#/carte-de-l-app) (fichier retrouve non suivi, remis a jour depuis zero) :
  section `Contribuer` remplacee par les tests d'export/import/rejet/publication CSV, section `Admin`
  sans la ligne « Valider une contribution », test ajoute pour le refus d'overwrite hors perimetre
  exact.
- Mis a jour [Règles & variables](#/regles-et-variables) et [Visualisations](#/visualisations) :
  variable Annee couvre desormais 1900-2100 via import CSV, en plus des annees systeme 1993/2023/2050.
- Mis a jour [KGI & Indicateurs](#/kgi-et-indicateurs) : nouvelle section « Qui fixe les seuils et
  limites », precise que les limites de fractale sont editables par import CSV au meme titre que les
  donnees et les seuils vitaux.
- Mis a jour [Sources de données](#/sources-de-donnees) : la couche Supabase decrit desormais les
  imports CSV representant plutot que les « contributions validees ».
- Mis a jour [Accueil](#/) : le routage par role pour l'admin ne mentionne plus la validation des
  contributions.
- Mis a jour [Architecture](#/architecture) : table des flux et diagramme de sequence remplaces par le
  flux d'import CSV (`representative_import_batches`/`cells`, RPC `publish_representative_import`),
  glossaire et regle de perimetre corriges (fractale exacte, plus de prefixe), note sur la table
  `contributions` desormais lue en lecture seule pour compat historique.
- Mis a jour [Guide d'onboarding](#/onboarding) et [Vue d'ensemble](#/readme) : mentions des annees
  systeme et du perimetre par prefixe corrigees ; description des flux Supabase alignee sur l'import
  CSV.
- Mis a jour [Référence API](#/api-reference) : nouvelle entree `contributionCsv.ts`, description de
  `useContributionOverwriteFlow` et des panneaux (`ContributionPanel`, `AdminPanel`) alignee sur le
  retrait de la revue de contributions.
- `_mindmap.md` : description du noeud Contribution mise a jour (import/export CSV representant).
- `docs/.docstate.json` re-epingle sur `1d5412a` / 2026-07-08 ; sources de `contribution`,
  `roles-et-contributions`, `carte-de-l-app` et `architecture` etendues a
  `src/utils/contributionCsv.ts`, `src/hooks/gaiaContributions/useContributionOverwriteFlow.ts`,
  `supabase/migrations/20260708190000_representative_data_import.sql` et
  `specs/019-representative-data-import/`.
- Non ajoute, sur decision explicite du brief : aucune nouvelle page. L'import CSV representant est
  integre dans les pages existantes `contribution.md` et `roles-et-contributions.md`.

## 2026-07-08 — regeneration complete au commit `c8b6cd0`

Regeneration `--full` du mini-site (toutes les pages reecrites), pour rattraper le harnais e2e
Playwright (spec 018, deja au HEAD lors de la revue precedente mais volontairement laisse hors doc)
et ajouter l'inventaire testable qui manquait.

- Ajoute [Carte de l'app](#/carte-de-l-app) : inventaire testable par unite de navigation (Explorer,
  Compte, Contribution, Debats, Representant, Admin), une ligne de test par fonction, verifie contre
  les libelles reels de l'UI et les specs Playwright (`e2e/smoke-*.spec.ts`).
- Mis a jour [Accueil](#/) : ajout d'un routage par role (utilisateur normal / responsable / admin,
  niveau de roles "complet") et d'un lien vers la Carte de l'app dans « Par ou entrer ? ». Retrait de
  la section « Lire la carte » (narration de concept, hors-charte pour un hall).
- Mis a jour [Utiliser](#/user) : ajoute deux entrees manquantes a la couverture,
  [Sources de donnees](#/sources-de-donnees) et [Charte graphique](#/charte-graphique), qui
  n'etaient liees depuis aucun sommaire d'audience.
- Mis a jour [Coder sur Gaïa](#/dev) : ajoute une entree « Verifier ton changement » qui pointe vers
  la Carte de l'app et `npm run test:e2e` (local-only, read-only contre le Supabase de prod).
- Copie fraiche du moteur (`assets/styles.css`, `theme.js`, `render.js`, `diagrams.js`, `serve.sh`)
  depuis le skill de documentation, pour repartir d'une base a jour.
- `docs/.docstate.json` re-epingle sur `c8b6cd0` / 2026-07-08, `pages[].sources` etendu a toutes les
  pages du site (y compris la nouvelle Carte de l'app).
- Toujours hors doc publique, sur demande explicite du projet : WikiData/SPARQL (roadmap uniquement,
  voir [Sources de donnees](#/sources-de-donnees)) et la publication RPC du redecoupage interactif
  Batch 2 (pas en prod, voir la note dans [Carte de l'app](#/carte-de-l-app)).

## 2026-07-08 — commit `135733f` → `c8b6cd0`

Refonte interne en deux specs (016, 017) : aucun changement de comportement produit, seulement la
carte des fichiers a bouge.

- Mis a jour [Architecture](#/architecture) : `App.tsx` decrit desormais comme coquille fine
  (spec 017 « Clear App.tsx ») qui delegue a `AppHeader`, `AppViewRouter`, `ExplorerView` et aux
  hooks `useAppNavigation` / `useExplorerState` / `useLiveOverwriteDraft`.
- Mis a jour [Vue d'ensemble](#/readme) et [Guide d'onboarding](#/onboarding) : structure de
  dossiers et table des fichiers-cles alignees sur `components/app/`, `components/explorer/` et
  les nouveaux hooks d'app.
- Verifie [Visualisations](#/visualisations), [Contribution](#/contribution),
  [Sources de donnees](#/sources-de-donnees) et [Roles & contributions](#/roles-et-contributions) :
  contenu deja exact, aucun texte a changer (le hook `useGaiaContributions` garde son API publique
  malgre l'eclatement interne en flux dans `src/hooks/gaiaContributions/`, spec 016 « Split
  useGaiaContributions »).
- Verifie [Decouvrir](#/new-user), [Utiliser](#/user) et [Coder sur Gaïa](#/dev) : liens et
  contenu toujours exacts, impact de la refonte nul cote lecteur.
- `docs/.docstate.json` re-epingle sur le commit `c8b6cd0`, sources des pages touchees mises a
  jour vers les nouveaux fichiers.
- Non documente sur demande explicite : le harnais e2e Playwright (`e2e/`, spec 018) reste hors
  de la doc publique.

## 2026-07-07 — generation initiale au commit `135733f`

- Ajoute le suivi incremental de documentation via `docs/.docstate.json`.
- Declare `docs/` comme sortie unique du mini-site public.
- Ajoute cette page comme journal lisible depuis la navigation et la carte mentale.
- Remplace des routes placeholders par des liens vers des pages existantes et retire une image locale absente.
