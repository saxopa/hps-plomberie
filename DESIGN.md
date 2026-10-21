# HPS Plomberie Sanitaire : notes de design

## 1. Recherche préliminaire : les tics des sites générés par IA (octobre 2026)

Sources lues : Paralect, « Signs of AI slop in design: 21 patterns » ; 925 Studios, « AI Slop Fonts and Gradients » ; Sailop, « AI Slop in 2026: State of the AI-Generated Web » ; consignes frontend d'Anthropic (skill frontend-design). Aucune étude ne mesure Opus 5.5 précisément : la liste ci-dessous croise ces sources avec les défauts que le modèle reconnaît lui-même produire quand la consigne est vague.

### Défauts propres à Claude / Opus (à proscrire ici)
| Tic | Pourquoi c'est un tic | Décision HPS |
|---|---|---|
| Fond crème #F4F1EA + serif à fort contraste + accent terre cuite #D97757 | Couleur d'interface de Claude lui-même | Fond faïence froid, pas de serif, pas de terre cuite |
| Fond quasi-noir + un seul accent vert acide ou vermillon | Look « tech » appliqué à tout | Fond clair, encre bleu pétrole |
| Mise en page « journal » : filets fins, rayon 0, colonnes denses | Fausse sophistication | Mise en page aérée, une colonne |
| Petit libellé EN CAPITALES espacées au-dessus de chaque titre | Chrome de gabarit | Aucun surtitre |
| Méta-chaînes avec points médians « A · B · C » | Idem | Phrases normales |
| Tiret cadratin espacé, densité de tirets supérieure à l'écrit humain | Signature d'écriture LLM | Zéro tiret cadratin dans le texte du site |
| Flèche « → » ajoutée aux boutons et liens | Idem | Verbes d'action seuls |
| Police monospace pour les petites données | Idem | Une seule famille |
| Un mot du titre en italique ou en couleur | Effet « premium » automatique | Titres d'une seule couleur |
| Marqueurs 01 / 02 / 03 sur un contenu qui n'est pas une suite | Numérotation décorative | Pas de numéros |

### Défauts génériques (tous outils)
- Inter partout, indigo-500 de Tailwind, dégradé violet-bleu derrière le hero, bouton dégradé « Get Started ».
- Trois cartes arrondies identiques en rangée, même rayon et même ombre grise partout.
- Ordre de sections type SaaS : hero, features, témoignages, tarifs, FAQ, CTA, footer.
- Pastilles vides de sens (« NEW », point vert « LIVE »), emojis en guise d'icônes.
- Verre dépoli, halos, néons, grilles de fond lumineuses.
- Apparition animée de chaque bloc au défilement, animations en boucle.
- Gris clair sur blanc pour le texte secondaire.
- Preuve sociale invérifiable : chiffres ronds en grand, faux témoignages, logos génériques.
- Texte qui promet une transformation sans dire ce qui change ; tricolons ; précautions oratoires.

## 2. Public visé

Particuliers de Cayenne, Matoury, Rémire-Montjoly et alentours, surtout propriétaires de maison individuelle (la fosse septique est fréquente hors centre). Tranche probable : 35 à 70 ans, sur un téléphone Android de milieu de gamme, souvent dans l'urgence (fuite, WC bouché, fosse qui déborde). Conséquences :

- Le numéro est la chose la plus importante de la page : il est en très grand dans le hero et toujours accessible (barre fixe en bas sur mobile).
- Texte en 18 px minimum, cibles tactiles de 48 px minimum, police Atkinson Hyperlegible Next (dessinée pour les personnes malvoyantes ; chiffres sans ambiguïté).
- Une seule page, pas de sous-menus. Le menu ne contient que quatre destinations.
- Le formulaire se remplit surtout en touchant des choix, peu de saisie.
- WhatsApp d'abord : c'est le canal du quotidien en Guyane.

## 3. Système

### Couleurs (contrastes vérifiés avant de coder)
| Nom | Hex | Rôle |
|---|---|---|
| Faïence | #F3F6F5 | Fond |
| Joint | #D3DCDB | Lignes de carrelage, décor uniquement (1,28:1, jamais pour du texte) |
| Encre | #0E2F3A | Texte, fonds sombres (12,99:1 sur faïence) |
| Vert-de-gris | #17705F | Patine du cuivre : liens, WhatsApp (5,49:1 sur faïence, blanc dessus 5,97:1) |
| Vert-de-gris clair | #7FD3BE | Accent sur fond encre (8,05:1) |
| Jaune chantier | #F5C518 | Bouton Appeler, toujours avec texte encre (8,67:1) |

### Typographie
Atkinson Hyperlegible Next, variable 400 à 800, une seule famille, auto-hébergée (34 Ko). Le numéro de téléphone sert d'élément graphique principal : 800, très grand, chiffres tabulaires.

### Mise en page
Une colonne alignée à gauche, largeur de lecture limitée à 36 em. Le hero est un mur de faïence (lignes de joint en CSS) avec le numéro posé dessus.

```
┌──────────────────────────────┐
│ HPS              [Appeler] ≡ │
├──────────────────────────────┤
│ ┼──┼──┼──┼  carrelage        │
│ Plombier à Cayenne, Matoury  │
│ et Rémire-Montjoly           │
│ 0694 00 00 00   (énorme)     │
│ [WhatsApp] [Demander devis]  │
├──────────────────────────────┤
│ ███ Vidange de fosse ███████ │  fond encre, mis en avant
├──────────────────────────────┤
│ En urgence  │ Travaux        │  listes, pas de cartes
├──────────────────────────────┤
│ Où j'interviens + adresse    │
├──────────────────────────────┤
│ Formulaire de devis          │
├──────────────────────────────┤
│ [ Appeler ][ WhatsApp ]  fixe│
└──────────────────────────────┘
```

### Principes
- L'audace est dépensée à un seul endroit : le numéro qui se remplit d'eau à l'arrivée sur la page. Tout le reste est calme.
- Une seule animation non déclenchée (l'entrée du hero). Les autres mouvements répondent à une action : le menu qui pivote en volte-face, le panneau e-mail qui s'ouvre.
- Chaque service est un lien qui pré-remplit le formulaire.
- Pas de témoignages ni de chiffres tant qu'ils ne sont pas réels.
- `prefers-reduced-motion` respecté, focus clavier visible.

### Performance
Objectif : page affichée en moins de 0,5 s, y compris depuis l'Amérique du Sud (CDN Fastly de GitHub Pages, point de présence à São Paulo). HTML unique avec CSS et JS en ligne, aucune image dans le hero, une police préchargée. Pas de React : un framework ajouterait 40 Ko et plus de JS à exécuter avant l'affichage, pour une seule page.
