# Carte de l'app

Tout ce que Gaïa sait faire aujourd'hui, écran par écran. Chaque ligne est un test de 30 secondes :
une action, un résultat attendu. De quoi vérifier qu'une fonction marche sans lire une ligne de code.

Les tests marqués d'un rôle (*admin*, *responsable*) nécessitent une session connectée avec ce rôle —
crée les comptes de test dans `.env.ux.local` (lecture seule, jamais commité). Un refus attendu compte
aussi comme un test : « connecté en responsable, cette action admin doit rester bloquée ».

## Explorer (vue par défaut)

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Carte géographique | Affiche les territoires en choroplèthe, coloriés par empreinte. | Ouvrir l'app : le titre `Visualisation Empreinte Gaïa` et la carte apparaissent. | [Visualisations](#/visualisations) |
| Sélection d'un territoire | Un clic sélectionne un territoire et ouvre sa fiche. | Cliquer une région : la fiche de détail s'ouvre à droite. | [Hiérarchie fractale](#/hierarchie-fractale) |
| Descente fractale | Un double-clic entre dans le territoire, révèle ses enfants. | Double-cliquer l'Europe : les régions filles apparaissent. | [Hiérarchie fractale](#/hierarchie-fractale) |
| Fil d'Ariane | Remonte vers les territoires parents. | Cliquer un ancêtre du fil d'Ariane : la carte remonte d'un niveau. | [Hiérarchie fractale](#/hierarchie-fractale) |
| Bascule Treemap | Passe de la carte géographique aux rectangles proportionnels à la population. | Cliquer le bouton `Treemap` : il passe actif (`aria-pressed=true`) et la vue change. | [Visualisations](#/visualisations) |
| Vue 1D — Benchmark | Compare les territoires en barres, largeur ∝ population. | Ouvrir le mode 1D : des barres apparaissent, une par territoire du niveau affiché. | [Visualisations](#/visualisations) |
| Vue 2D — Isocourbes | Affiche un fond d'isocourbes pour l'empreinte énergie hors mobilité. | Ouvrir le mode 2D sur la catégorie logement / empreinte énergie : les courbes s'affichent. | [Visualisations](#/visualisations) |
| Vue 3D | Affiche le territoire sélectionné en relief SVG. | Ouvrir le mode 3D : la vue se dessine ; le bouton débat devient inactif. | [Visualisations](#/visualisations) |
| Colonne 3D fractale | Mini-vue 3D de la fractale active, dans le menu gauche. | Sélectionner un territoire : la colonne latérale se met à jour avec sa forme. | [Visualisations](#/visualisations) |
| Filtres Empreinte / Catégorie / Année | Change la donnée lue sans changer de territoire. Les années disponibles s'étendent dynamiquement si des imports représentant en ont publié de nouvelles. | Ouvrir les filtres `Catégorie` et `Année` (titres visibles) : cliquer une option recolore la carte. | [Règles & variables](#/regles-et-variables) |
| Ouvrir un débat sur une donnée | Lance la contestation publique d'une valeur affichée. | Depuis une fiche territoire, cliquer `Commenter / ouvrir un débat` : le formulaire s'ouvre pré-rempli. | [Contribution](#/contribution) |
| Modifier en direct *(responsable/admin)* | Publie immédiatement une nouvelle valeur (overwrite) sur une fractale exacte du représentant. | Connecté en responsable, ouvrir une de ses fractales exactes : le bouton `Modifier en direct (Overwrite)` est visible et ouvre le formulaire. | [Contribution](#/contribution) |
| Modifier en direct refusé hors périmètre *(responsable)* | Bloque l'overwrite sur une fractale non attribuée, même descendante d'une fractale tenue. | Connecté en responsable, ouvrir une fractale non attribuée : le bouton `Modifier en direct` reste absent ou refuse la publication. | [Rôles & contributions](#/roles-et-contributions) |

## Compte

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Inscription | Crée un compte email + mot de passe. | Depuis `Connexion`, remplir email/mot de passe et valider : un profil est créé. | [Rôles & contributions](#/roles-et-contributions) |
| Connexion | Restaure la session sur un compte existant. | Se connecter avec un compte de test : le bouton d'en-tête affiche `Mon compte`. | [Rôles & contributions](#/roles-et-contributions) |
| Réinitialisation du mot de passe | Envoie un lien de réinitialisation par email. | Cliquer `Mot de passe oublié` : un message de confirmation d'envoi s'affiche. | [Rôles & contributions](#/roles-et-contributions) |
| Modifier son profil | Met à jour le type de profil (citoyen, association, entreprise…) et sa localisation. | Dans `Compte`, changer le type de profil et enregistrer : le nouveau profil s'affiche. | [Rôles & contributions](#/roles-et-contributions) |

## Contribuer *(responsable)*

L'onglet `Contribuer` n'apparaît que pour un compte représentant (au moins une fractale exacte
attribuée). C'est le circuit d'import/export CSV — le circuit unique de contribution en masse, sans file
de validation admin.

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Exporter CSV | Télécharge un fichier large couvrant exactement les fractales attribuées, toutes cellules et sources incluses. | Connecté en responsable, ouvrir `Contribuer` puis cliquer `Exporter CSV` : un fichier `gaia-representant-*.csv` se télécharge, une ligne par fractale × année. | [Contribution](#/contribution) |
| Importer un CSV modifié | Relit le fichier édité et affiche un aperçu chiffré des changements. | Cliquer `Choisir un fichier`, sélectionner un CSV édité : les compteurs `Lignes` / `Modifiées` / `Inchangées` / `Vides ignorées` s'affichent. | [Contribution](#/contribution) |
| Rejet d'un fichier invalide | Bloque toute publication si une cellule change sans source ni justification, si une ligne sort du périmètre, ou si l'année sort de 1900–2100. | Importer un CSV avec une cellule changée sans `source_label` : le bandeau `Erreurs bloquantes` liste la ligne et le champ, et `Publier toutes les cellules` reste désactivé. | [Contribution](#/contribution) |
| Publier un import valide | Publie toutes les cellules changées en une seule transaction atomique. | Importer un CSV valide puis cliquer `Publier toutes les cellules` : le message `Import publié` s'affiche, les valeurs changent dans Explorer. | [Contribution](#/contribution) |
| Télécharger le rapport | Donne le détail ligne par ligne d'un aperçu ou d'un rejet. | Après un import, cliquer `Télécharger le rapport` : un fichier `.report.txt` se télécharge. | [Contribution](#/contribution) |
| Rétro-compatibilité ligne | Accepte un ancien CSV à source unique par ligne (pas par cellule). | Importer un fichier généré avant spec 019 (colonnes source de ligne) : il est lu et validé sans erreur de format. | [Contribution](#/contribution) |

## Débats

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Liste des débats | Index public de tous les débats ouverts. | Cliquer l'onglet `Débats` : le titre `Débats` liste les fils existants. | [Contribution](#/contribution) |
| Créer un débat | Ouvre un nouveau fil sur une donnée contestée, avec preuves. | Depuis la liste, cliquer `Ouvrir un débat` : le formulaire (titre, description, preuves) s'affiche. | [Contribution](#/contribution) |
| Écrire un message | Ajoute un message au fil d'un débat. | Ouvrir un débat où l'on a le droit d'écrire : le champ `Votre message…` est actif. | [Contribution](#/contribution) |
| Joindre une preuve | Attache un fichier (PDF, image, CSV, XLSX) au débat. | Créer un débat en ajoutant un fichier : le fichier apparaît dans la liste de preuves. | [Contribution](#/contribution) |
| Signaler / rejeter un débat *(admin)* | Marque un débat comme problématique, ou le rejette. | Connecté en admin, section `Débats signalés` : signaler un débat le fait apparaître dans la file de modération. | [Rôles & contributions](#/roles-et-contributions) |
| Nullifier un débat *(admin)* | Clôt le débat en archive lecture seule, sans changer de valeur. | Depuis le débat, cliquer `Nullifier` : le fil passe en lecture seule. | [Rôles & contributions](#/roles-et-contributions) |
| Décision Oui/Non *(responsable)* | Le représentant du territoire tranche la proposition du débat. | Connecté en responsable sur le bon périmètre, ouvrir le débat : les boutons `Oui` / `Non` sont visibles. | [Rôles & contributions](#/roles-et-contributions) |
| Escalade créateur *(admin)* | Le créateur insatisfait d'un `Non` demande un arbitrage admin. | Après un `Non`, demander une escalade : elle apparaît dans `Escalades créateur` côté admin. | [Rôles & contributions](#/roles-et-contributions) |

## Représentant *(responsable)*

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Territoires gérés | Liste toutes les fractales exactes attribuées au représentant. | Ouvrir `Espace Représentant` : le titre `Liste de vos territoires gérés` affiche le périmètre. | [Rôles & contributions](#/roles-et-contributions) |
| Historique des modifications | Trace toutes les modifications en direct déjà publiées. | Dans `Espace Représentant`, la section `Historique des modifications directes` liste les overwrites passés. | [Contribution](#/contribution) |
| Fixer un seuil vital fractal | Définit le minimum vital propre à une fractale du périmètre exact. | Dans `Seuils vitaux`, fixer une valeur pour une fractale gérée : elle s'applique à cette fractale. | [KGI & Indicateurs](#/kgi-et-indicateurs) |
| Débats ouverts sur le périmètre | Liste les débats concernant les fractales gérées. | La section `Débats citoyens ouverts sur vos périmètres` liste les fils actifs. | [Contribution](#/contribution) |
| Accès admin masqué *(responsable)* | Un responsable non-admin ne voit ni le bouton ni les surfaces réservées à l'admin. | Connecté en responsable, `Espace Admin` est absent de l'en-tête. | [Rôles & contributions](#/roles-et-contributions) |

## Admin *(admin)*

Depuis spec 019, il n'y a plus de file de validation des contributions dans l'espace admin : les
imports CSV et overwrites se publient directement (voir [Contribuer](#/contribution) ci-dessus).

| Fonction | Ce qu'elle doit faire | Pour vérifier | Détail |
|---|---|---|---|
| Rejeter un overwrite | Retire une modification en direct déjà publiée. | Onglet `Modifications (Overwrites)` : rejeter un overwrite le retire de la liste active. | [Contribution](#/contribution) |
| Modérer les débats signalés | Traite les débats signalés par la communauté. | Onglet `Débats signalés` : le compteur correspond aux débats + escalades ouvertes. | [Contribution](#/contribution) |
| Limites mondiales | Fixe le seuil limite planétaire par empreinte × catégorie. | Onglet `Limites mondiales` : modifier une limite met à jour l'historique. | [KGI & Indicateurs](#/kgi-et-indicateurs) |
| Constructeur de chaîne (annuaire) | Attribue ou révoque un représentant, fractale exacte par fractale exacte. | Onglet `Constructeur de chaine` : ancrer un compte sur une fractale L9 l'ajoute à l'annuaire. | [Rôles & contributions](#/roles-et-contributions) |
| Redécoupage — fusions | Fusionne des territoires voisins en un seul. | Onglet `Redécoupage`, section `Redécoupages actifs` : une fusion validée apparaît dans la liste. | [Hiérarchie fractale](#/hierarchie-fractale) |
| Redécoupage — proposition topologique | Prépare une proposition de redécoupage relue avant publication. | Onglet `Redécoupage`, bloc `Proposition topologique reviewable` : une proposition en cours s'affiche avant publication. | [Hiérarchie fractale](#/hierarchie-fractale) |
| Équilibrage | Rapport de contrôle démographique/géographique des fractales. | Onglet `Équilibrage` : le rapport liste les fractales hors bornes (6–18 enfants). | [Hiérarchie fractale](#/hierarchie-fractale) |
| Importation (ingestion) | Affiche la procédure d'import en masse depuis un tableur `.ods`. | Onglet `Importation (Ingestion)` : le bloc `Procédure d'importation` détaille les étapes. | [Sources de données](#/sources-de-donnees) |

> [!note] Le redécoupage interactif « coupe à la carte » (`TopologySplitEditor`, Spec 13) est visible
> dans l'onglet `Redécoupage` et sa RPC de publication est déployée en production (migration
> `20260627164000_topological_redistricting`, vérifiée le 2026-07-13). Les vérifications locales et E2E
> restent en lecture seule: elles n'exécutent jamais une publication topologique.

## Automatismes planifiés

Aucun automatisme planifié (cron, rappel programmé) n'est câblé dans le code aujourd'hui. La seule
tâche différée est l'envoi d'un email d'escalade (fonction Supabase Edge `send-debate-escalation`),
déclenché **à la demande** par l'action « Escalade créateur » ci-dessus — pas sur un horaire.

## Parcours de validation complet (~10 min)

1. **Explorer** — ouvrir l'app, vérifier la carte et les filtres `Catégorie` / `Année`. Basculer en
   Treemap, puis en vue 1D. Sélectionner un territoire.
2. **Compte** — se connecter avec un compte représentant de test (`.env.ux.local`). Vérifier que
   `Mon compte` apparaît en en-tête.
3. **Débats** — depuis le territoire sélectionné, ouvrir un débat (sans l'envoyer, ou avec un compte de
   test dédié). Vérifier la liste dans l'onglet `Débats`.
4. **Contribuer** — ouvrir `Contribuer`, exporter un CSV, l'éditer avec une valeur et sa source, le
   réimporter et vérifier l'aperçu (cellules modifiées). Publier, puis confirmer le nouveau chiffre dans
   Explorer.
5. **Représentant** — ouvrir `Espace Représentant`, vérifier que seules les fractales exactes
   attribuées apparaissent dans `Liste de vos territoires gérés`, et que l'import de l'étape 4 apparaît
   dans l'historique.
6. **Admin (si le compte l'est aussi)** — ouvrir `Espace Admin`, parcourir les onglets `Modifications
   (Overwrites)`, `Débats signalés`, `Limites mondiales`, `Redécoupage`. Vérifier qu'aucun onglet de
   validation des contributions n'apparaît.
7. **Rôle borné** — se reconnecter avec un compte responsable non-admin. Confirmer que le bouton
   `Espace Admin` est absent et qu'aucune surface réservée à l'admin n'est accessible.
