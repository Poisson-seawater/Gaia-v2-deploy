# Rôles & contributions

Gaïa distingue deux niveaux d'utilisateurs connectés, plus le visiteur anonyme. Chaque niveau donne un pouvoir différent sur les données et les débats — et un compte peut cumuler plusieurs responsabilités de représentant.

Cette page explique les permissions. Les circuits de publication sont détaillés dans [Contribution](#/contribution).

## Les niveaux d'accès

| Rôle | Comment on l'obtient | Ce qu'il peut faire |
|---|---|---|
| **Visiteur** | Aucun compte requis | Explorer les cartes et lire les débats publics. |
| **Utilisateur** | Création de compte et profil | Ouvrir un débat sur une donnée. Il peut écrire dans les débats qu'il a créés ou auxquels il a été invité. |
| **Représentant** *(responsable de fractale)* | Attribution directe par un admin, fractale par fractale | Exporter/importer le CSV et publier des overwrites sur ses fractales exactes ; décider des débats de son périmètre. |
| **Admin Gaïa** | Promotion manuelle en base | Nomme les représentants, rejette les overwrites, modère les débats, gère les seuils et limites mondiaux, pilote le redécoupage territorial. |

## Le périmètre d'un représentant : fractale exacte, pas d'héritage

> [!note] Depuis l'import CSV représentant (spec 019), le périmètre suit la **fractale exacte
> attribuée** — il ne s'étend plus automatiquement aux descendants. C'est un changement par rapport à
> l'ancien comportement par préfixe.

- Être attribué sur `1.2.2` donne le droit d'agir sur `1.2.2`, et seulement `1.2.2` — pas sur `1.2.2.1`
  ni sur ses autres descendants, sauf attribution séparée pour chacun.
- Un compte peut détenir **plusieurs** attributions exactes en même temps : par exemple `1.2.2` et
  `1.2.5`, sans lien de parenté entre elles.
- Une action sur une fractale non explicitement attribuée est refusée, y compris si elle est
  géographiquement incluse dans une fractale détenue.
- La nomination reste ancrée à un niveau L9 puis peut s'étendre vers les niveaux supérieurs, mais
  chaque niveau franchi doit être attribué explicitement pour donner un droit d'édition dessus (voir
  « Nomination des représentants » ci-dessous).

Dans l'onglet **Contribuer**, l'export et l'import CSV ne couvrent que les fractales exactes du
représentant. Dans **Explorer**, le bouton de modification directe suit la même règle pour un
représentant. Un admin peut publier un overwrite direct sur tout territoire, mais n'a ni l'export ni
l'import CSV sans attribution exacte de représentant.

> [!note] L'**héritage** existe encore, mais seulement en **lecture** pour les seuils vitaux et les
> limites : un descendant sans valeur propre utilise celle du parent le plus proche qui en a une. Cela
> ne donne aucun droit d'édition — voir [KGI & Indicateurs](#/kgi-et-indicateurs).

## Import / export CSV et modifications en direct

Un représentant publie de deux façons, détaillées dans [Contribution](#/contribution) :

- **Import CSV** : exporte un fichier large pour ses fractales exactes, l'édite hors-ligne avec une
  source, une URL et une justification par cellule changée, puis le réimporte. La publication est
  atomique — tout ou rien.
- **Overwrite** : corrige une seule valeur depuis la fiche du territoire dans **Explorer**, avec effet
  immédiat.

Il n'existe plus de file de validation admin pour les contributions : l'admin ne relit plus les valeurs
avant publication. Il conserve un contrôle *a posteriori* sur les overwrites (il peut les rejeter) et
sur les débats (modération, nullification).

Les anciennes lignes `contributions` — issues de l'ancien circuit d'observation avec validation admin —
restent lues par l'application pour préserver l'historique, mais aucune nouvelle contribution de ce type
ne peut plus être créée.

Toutes les modifications restent dans l'historique avec leur auteur, leur source, l'ancienne valeur et
la nouvelle valeur.

## Débats citoyens

```text
[Utilisateur connecté]
     │ ouvre un débat sur une donnée
     ▼
[Débat public]  ← visible immédiatement
     │
     ├── discussion et preuves
     ├── signalement éventuel
     └── résolution ou rejet administratif
```

1. Tout utilisateur connecté avec un profil peut ouvrir un débat depuis **Explorer**.
2. Le débat est visible dès sa création. La modération intervient donc après publication.
3. Le créateur, le représentant territorial, les admins et les participants invités peuvent écrire.
4. Un admin peut nullifier un débat sans modification de valeur.
5. Le représentant territorial décide **Oui** ou **Non**. **Oui** renvoie vers l'overwrite prérempli ; **Non** clôt le débat sans modification.
6. Après un **Non**, le créateur peut se dire satisfait ou demander une escalade admin.

## Nomination des représentants

1. L'admin ouvre **Espace Admin**, puis **Constructeur de chaîne**.
2. L'annuaire liste les comptes et leurs responsabilités actuelles.
3. L'admin ancre d'abord la personne sur une fractale L9.
4. Il peut ensuite la promouvoir vers le niveau parent, un niveau à la fois — chaque promotion ajoute
   une attribution exacte à ce niveau, elle ne remplace pas les attributions déjà tenues.
5. Une révocation suit l'ordre inverse, du niveau le plus haut vers L9.

Les imports CSV et overwrites déjà publiés restent dans l'historique après une révocation.

## Cas particuliers

« **Un représentant peut-il publier directement ?** » Oui, de deux façons : un overwrite pour une
valeur isolée, ou un import CSV pour plusieurs cellules d'un coup. Aucune des deux n'attend de
validation admin.

« **Un utilisateur normal peut-il proposer une donnée ?** » Il ne peut pas l'appliquer directement. Il
peut proposer une valeur et joindre des preuves dans un débat.

« **Être représentant d'un territoire rend-il automatiquement représentant de ses sous-territoires ?**
» Non, depuis spec 019. Chaque fractale doit être attribuée explicitement — voir la note ci-dessus sur
le périmètre exact.

« **Qui modère un débat sur un territoire sans représentant ?** » L'admin peut le signaler, le rejeter ou le résoudre.

« **Le statut représentant est-il un rôle de profil ?** » Non. Il est dérivé des attributions
enregistrées dans `fractal_responsibilities`. Un compte est représentant dès qu'au moins une fractale
lui est attribuée.
