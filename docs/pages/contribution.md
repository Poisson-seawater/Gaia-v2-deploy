# Contribuer aux données

Contribuer, ce n'est pas ressaisir une valeur à la main. Gaïa sépare la modification rapide, l'import en masse et la discussion publique.

Le mécanisme disponible dépend du rôle de la personne connectée.

## Vue d'ensemble

| Mécanisme | Qui peut l'utiliser ? | Effet sur la donnée | Contrôle |
|---|---|---|---|
| **Import / export CSV** | Représentant (responsable d'une fractale exacte) | Publie plusieurs cellules d'un coup, dès validation du fichier | Validation stricte à l'import, aucune relecture admin ensuite |
| **Modification en direct** (*overwrite*) | Représentant sur son périmètre, ou admin | Remplace immédiatement la valeur affichée pour une cellule | Rejet possible après publication par un admin |
| **Débat sur une donnée** | Tout utilisateur connecté avec un profil | Aucun changement automatique | Signalement et modération après publication |

Un visiteur sans compte peut consulter les données et les débats publics. Il ne peut pas publier.

> [!note] L'onglet **Contribuer** n'est visible que pour les représentants (comptes avec au moins une
> fractale attribuée dans `fractal_responsibilities`). Un admin qui n'est pas lui-même représentant
> d'une fractale ne peut ni l'exporter ni l'importer.

## Prérequis

Il faut d'abord créer un compte et compléter son profil public. Le profil peut représenter un citoyen, une association, une entreprise, une collectivité ou un organisme de recherche.

La saisie de données reste réservée aux représentants et aux admins. Un admin nomme les représentants et définit leur périmètre dans la [Hiérarchie fractale](#/hierarchie-fractale).

## 1. Import / export CSV (représentant)

C'est le mécanisme principal pour publier plusieurs valeurs à la fois. Un représentant qui gère plusieurs fractales n'édite pas cellule par cellule dans l'app : il exporte un tableau, le corrige hors-ligne, puis le réimporte en une seule publication.

```text
[Représentant]
     │ Exporter CSV
     ▼
Fichier large : une ligne par fractale × année,
colonnes data / vital / limite + source / URL / justification par cellule
     │ édition hors-ligne (tableur)
     ▼
Importer un CSV modifié
     │ validation stricte du fichier entier
     ├── une seule cellule invalide ──► rejet total, rien n'est publié
     └── fichier valide ──► aperçu : cellules changées / inchangées / vides ignorées
                                   │ confirmation
                                   ▼
                        publish_representative_import
                        (publication atomique)
```

**Exporter.** Depuis l'onglet **Contribuer**, le bouton `Exporter CSV` télécharge un fichier couvrant
exactement les fractales attribuées au représentant — jamais leurs descendants ni leurs voisines. Le
fichier reste lisible dans un tableur : une ligne par fractale et par année, avec les valeurs de
données, les seuils vitaux et les limites de fractale déjà en place.

**Éditer.** Chaque cellule modifiable (donnée, vital, limite) porte trois colonnes qui lui sont
propres : `source_label`, `source_url` et `justification`. Une cellule laissée vide dans le fichier
n'est **jamais** interprétée comme une suppression : elle est simplement ignorée à l'import.

**Importer.** Le bouton `Choisir un fichier` relit le CSV modifié et affiche un aperçu chiffré : lignes
lues, cellules modifiées, cellules inchangées, cellules vides ignorées. Toute cellule changée sans
`source_label` ni `justification`, toute ligne hors périmètre du représentant, toute année hors de
1900–2100 ou toute ligne dupliquée (même fractale, même année) bloque **tout** le fichier — la
publication reste tout ou rien.

**Publier.** Le bouton `Publier toutes les cellules` envoie le lot validé à
`publish_representative_import`, qui écrit toutes les cellules changées en une seule transaction. Rien
n'est publié tant qu'une erreur bloquante subsiste dans le fichier ; un rapport téléchargeable détaille
chaque ligne et chaque cellule rejetée.

> [!note] Un CSV importé publie une année arbitraire entre 1900 et 2100 — pas seulement les années
> système 1993 / 2023 / 2050. Cette année devient aussitôt disponible dans **Explorer**, sans étape de
> validation admin. Voir [Règles & variables](#/regles-et-variables).

**Rétro-compatibilité.** Les anciens fichiers CSV, qui portaient leurs colonnes source, URL et
justification une seule fois **par ligne** plutôt que par cellule, restent acceptés à l'import. Gaïa les
lit comme si la même source s'appliquait à toutes les cellules changées de la ligne.

Il n'existe plus de file de validation admin pour les contributions : publier un import valide suffit,
sans relecture centrale a posteriori.

## 2. Modifier immédiatement une donnée

Une modification en direct, appelée *overwrite* dans l'interface, sert à corriger rapidement une seule valeur déjà affichée, sans passer par un fichier.

Le représentant sélectionne un territoire dans **Explorer**, puis clique sur **Modifier en direct**. Il indique la nouvelle valeur et sa source. Une URL et une justification peuvent aussi être ajoutées.

La modification devient immédiatement active. Elle est enregistrée avec :

- l'ancienne valeur ;
- la nouvelle valeur ;
- le territoire, la métrique et l'année ;
- l'auteur et la date ;
- la source et la justification.

Le représentant ne peut agir que sur ses fractales exactes. Par exemple, le responsable de `1.2.2` peut modifier `1.2.2`, mais pas `1.2.2.1` s'il n'en est pas directement responsable — le périmètre ne s'étend qu'aux fractales explicitement attribuées, pas à toute la descendance.

L'admin peut ensuite rejeter la modification depuis son espace. Dans ce cas, Gaïa retire cet overwrite et réaffiche la dernière valeur encore valide.

## 3. Ouvrir un débat sur une donnée

Un utilisateur normal ne peut pas modifier directement les mesures territoriales. Il peut toutefois contester une valeur ou proposer une correction dans un débat public.

Depuis **Explorer**, il sélectionne une donnée puis clique sur **Commenter / ouvrir un débat**. Le formulaire accepte :

- un titre ;
- une description qui devient le premier message ;
- une valeur alternative facultative ;
- jusqu'à cinq preuves de 10 Mo chacune ;
- les formats PDF, PNG, JPG, WEBP, CSV et XLSX.

Un seul débat peut rester ouvert pour une même combinaison territoire, catégorie, métrique et année. Si un débat existe déjà, l'utilisateur rejoint le fil existant.

Le débat est visible immédiatement. Il n'attend pas une validation préalable.

### Qui peut écrire ?

L'écriture est réservée :

- au créateur du débat ;
- au représentant du territoire concerné ;
- aux admins ;
- aux participants ajoutés par un admin.

Les autres visiteurs peuvent lire le fil.

### Comment un débat se termine-t-il ?

Un admin peut nullifier le débat immédiatement. Le débat devient alors une archive en lecture seule, sans modification de valeur.

Le représentant territorial tranche avec **Oui** ou **Non** :

- **Oui** : la correction est acceptée. Gaïa renvoie le représentant vers Explorer, avec le territoire et la métrique sélectionnés, puis ouvre le formulaire d'overwrite pour publier la nouvelle valeur.
- **Non** : la proposition est rejetée. Le débat est clos sans modification de valeur.

Si le créateur n'est pas satisfait d'un rejet, il peut demander une escalade admin depuis le débat. Gaïa enregistre cette escalade et tente d'envoyer un email à l'admin.

## Comment la valeur active est choisie

Gaïa commence par charger la donnée de base décrite dans les [Sources de données](#/sources-de-donnees). Le système superpose ensuite les imports CSV publiés, les overwrites encore actifs, et les anciennes contributions historiques conservées pour compatibilité.

Les changements sont appliqués par ordre chronologique. Pour une même fractale, année et cellule, la publication la plus récente prend donc le dessus — que la publication vienne d'un import CSV ou d'un overwrite direct.

```text
Donnée de base
      ▼
Imports CSV publiés + overwrites actifs (+ anciennes contributions historiques)
      │ tri chronologique, dernière publication gagne
      ▼
Valeur affichée dans les cartes et graphiques
```

La fiche du territoire affiche la source et le nom de l'auteur de la valeur active. Un import CSV ou un overwrite est signalé comme une modification effectuée par un représentant.

## Sécurité et périmètre

Les permissions sont vérifiées deux fois. L'interface masque les actions interdites, puis les règles de sécurité Supabase contrôlent à nouveau chaque écriture — y compris `publish_representative_import`, qui refuse toute cellule hors des fractales exactement attribuées à l'appelant.

Le périmètre d'un représentant ne suit plus l'héritage par préfixe pour l'import CSV : seule la fractale explicitement attribuée est exportable et importable, pas ses descendants. L'overwrite direct suit la même règle de périmètre exact.

Cette double vérification empêche un utilisateur de contourner l'interface en appelant directement l'API.

## À retenir

« **Je vois une erreur, mais je ne suis pas représentant.** » Ouvre un débat et joins tes sources. Le représentant du territoire pourra ensuite corriger la valeur.

« **J'ai plusieurs valeurs à corriger d'un coup.** » Exporte le CSV, corrige-le hors-ligne avec tes sources, puis importe-le. La publication de toutes les cellules est atomique.

« **Une seule valeur, correction urgente.** » Utilise un overwrite. La publication est immédiate, mais l'admin conserve un droit de retrait.

« **La résolution d'un débat annonce une correction.** » Cela ne suffit pas à changer les graphiques. Un import CSV ou un overwrite doit encore être publié.

Les pouvoirs de chaque acteur sont détaillés dans [Rôles & contributions](#/roles-et-contributions).
