# Charte graphique Proto Process — référence pour applis internes

**v1.1 — 31/07/2026**

Basée sur `appro-v2.html`, reprise sur `bom-sourcing.html`. À copier-coller en base de toute nouvelle appli interne (HTML/CSS/JS autonome, sans framework).

---

## 1. Logo

Fichier `picto-pp.png`, **mutualisé dans un dossier `shared/` à la racine de chaque dépôt** — `commercial/shared/`, `achats/shared/`, `methodes-production/shared/` — et référencé en relatif depuis l'appli :

```html
<img src="../shared/picto-pp.png" alt="Proto Process" width="24" height="24"
     style="object-fit:contain" onerror="this.style.display='none'">
<span class="logo-text"><span class="lt">PROTO PROCESS</span><span class="ls">/</span><span class="lo">Nom de l'appli</span></span>
```

```css
.logo-text { display:flex; align-items:center; gap:0; font-size:13px; font-weight:500; }
.logo-text .lt { color:#fff; }
.logo-text .ls { color:#444; margin:0 3px; }
.logo-text .lo { color:var(--blue); }
```

Jamais d'ID Drive, jamais d'URL absolue (le fetch base64 n'est pas possible en session de build).

⚠ **Le `picto-pp.png` situé à la racine de `protoprocess.github.io` est celui de la page d'accueil.** Il n'est **pas** atteignable en `../shared/` depuis une appli : les applis sont servies sous `protoprocess.github.io/<dépôt>/<appli>/`, donc `../shared/` résout vers `<dépôt>/shared/`, jamais vers la racine du site. Chaque dépôt a besoin de son propre `shared/`.

⚠ **`onerror` masque silencieusement le logo si le fichier est absent.** C'est voulu — ne jamais faire planter l'appli pour un picto — mais ça a un prix : **une erreur de chemin ne se voit pas**. Après tout déplacement du fichier, recharger la page publiée et vérifier le logo à l'œil. C'est le seul contrôle possible.

**Ordre impératif en cas de migration** : créer le fichier dans `shared/` **avant** de modifier le `src` de l'appli. L'inverse laisse le logo cassé dans l'intervalle, sans aucun signal.

---

## 2. Palette (variables CSS)

```css
:root {
  /* Fond / surfaces */
  --bg: #0a0c12;
  --surface: #1a2030;
  --surface2: #161b27;
  --border: #262e42;
  --border2: #333d55;

  /* Texte */
  --text: #e2e8f0;
  --text2: #94a3b8;
  --text3: #64748b;

  /* Accent principal (orange) */
  --blue: #F07B1F;
  --blue-bg: #2a1f10;

  /* Accent secondaire, usage ponctuel uniquement */
  --pp-blue: #5BC4D8;

  /* Statuts — DEUX variantes par couleur, voir section 3 */
  --green: #86c564;         --green-bg: #EAF3DE;  --green-badge: #3B6D11;
  --amber: #eab676;         --amber-bg: #FAEEDA;  --amber-badge: #854F0B;
  --red: #f09595;           --red-bg: #FCEBEB;    --red-badge: #A32D2D;
  --pink: #ED93B1;          --pink-bg: #FBEAF0;   --pink-badge: #993556;

  --radius: 6px;
  --radius-lg: 10px;
  --mono: "SF Mono", "Cascadia Code", Consolas, monospace;
  --sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Inter, Roboto, Helvetica, Arial, sans-serif;
}

header { background: #0f1117; height: 52px; }
```

Note historique : `--blue` porte ce nom pour des raisons de compatibilité avec un fichier plus ancien où l'accent principal était bleu. La couleur réelle est l'orange PP. Dans une appli neuve, préférer directement `--accent` comme nom de variable.

Police : aucune police externe (pas de Google Fonts). Uniquement la police système + `tabler-icons` pour les icônes :
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/tabler-icons/2.44.0/iconfont/tabler-icons.min.css">
```

---

## 3. Piège à ne pas reproduire : badge vs texte nu

**Chaque couleur de statut existe en deux variantes, et confondre les deux casse la lisibilité :**

- **`--green` / `--amber` / `--red` / `--pink`** (claires) → texte affiché **directement sur fond sombre** (compteurs, messages de statut, texte dans une cellule de tableau, icônes seules). Une version foncée serait illisible sur `--bg`/`--surface`.
- **`--green-badge` / `--amber-badge` / `--red-badge` / `--pink-badge`** (foncées, ce sont les valeurs d'origine du thème clair) → texte à l'intérieur d'une **pastille à fond pastel** (`--green-bg` etc.). Une version claire serait illisible sur un fond déjà clair.

Règle simple : dès que `background: var(--X-bg)` est posé quelque part (pastille, badge, carte de statut), le texte à l'intérieur utilise `var(--X-badge)`, jamais `var(--X)` tout court. Partout ailleurs (texte nu sur fond sombre), c'est l'inverse.

**Deuxième piège** : ne jamais utiliser `var(--text)` comme **fond** d'un bouton/onglet/état actif. Dans le thème sombre, `--text` est clair — un fond clair avec texte blanc dessus devient illisible. Le fond des éléments actifs/primaires doit toujours être `var(--blue)` (l'accent), jamais `var(--text)`.

---

## 4. Composants de base

**Bouton primaire**
```css
.btn-p { padding:6px 13px; background:var(--blue); color:#fff; border:none; border-radius:var(--radius); font-size:12px; font-weight:500; }
```

**Bouton secondaire**
```css
.btn-s { padding:6px 13px; background:transparent; color:var(--text2); border:0.5px solid var(--border); border-radius:var(--radius); font-size:12px; }
```

**Onglets**
```css
.tab { padding:11px 18px; color:var(--text3); border-bottom:2px solid transparent; }
.tab.active { color:var(--blue); border-bottom-color:var(--blue); }
```

**Filtres (pills)**
```css
.filter-btn { border:1px solid var(--border); background:var(--surface); color:var(--text2); border-radius:20px; padding:5px 12px; }
.filter-btn.active { background:var(--blue); color:#fff; border-color:transparent; }
```

**Pastilles de statut** (fond pastel + texte foncé — voir section 3)
```css
.badge-ok   { background:var(--green-bg); color:var(--green-badge); }
.badge-warn { background:var(--amber-bg); color:var(--amber-badge); }
.badge-err  { background:var(--red-bg);   color:var(--red-badge); }
```

**Cartes / statut avec liseré** — préférer un liseré de couleur plutôt qu'un fond teinté en transparence (les tons pastel en alpha sur fond sombre deviennent invisibles) :
```css
.card.err { border-left: 3px solid var(--red-badge); }
.card.warn { border-left: 3px solid var(--amber-badge); }
.card.ok { border-left: 3px solid var(--green-badge); }
```

Sur une ligne de tableau (`<tr>`), un `border-left` classique décale le contenu (ajoute à la largeur de la cellule) — préférer `box-shadow: inset 3px 0 0 <couleur>` sur la première cellule, qui donne le même rendu visuel sans décalage de mise en page.

---

## 5. Mise en page — enseignements du premier retour utilisateur

- **Toujours prévoir un usage grand écran (PC) dès le départ.** Une largeur de conteneur fixe modeste (ex. 1100px) frustre vite sur un écran large. Préférer une largeur adaptative avec plafond raisonnable : `max-width: min(96vw, 1800px);`
- **Ne jamais faire dépendre la visibilité d'une info critique (ex. stock, statut) d'un défilement horizontal.** Placer les colonnes à contenu court et important (statuts, quantités) *avant* toute colonne à contenu long et variable (notes, descriptions) dans l'ordre du tableau — jamais après.
- **Une fois une étape de saisie/import terminée, replier automatiquement sa zone d'interface** (résumé compact + bouton "Modifier" pour ré-ouvrir) plutôt que de la laisser occuper de la place en permanence. Ça libère l'espace pour les résultats, qui sont ce que l'utilisateur consulte le plus longtemps.
- **Toute suggestion algorithmique approximative (matching flou, etc.) doit être conçue pour se taire par défaut plutôt que pour maximiser le rappel.** Mieux vaut rater une suggestion correcte de temps en temps que noyer l'utilisateur sous des propositions peu pertinentes — le trop-plein tue la confiance dans la fonctionnalité bien plus vite qu'un oubli occasionnel.
- **Les info-bulles/dropdowns contenant du texte technique (références, IPN, MPN) ne doivent jamais tronquer le texte** — largeur généreuse + retour à la ligne plutôt qu'ellipsis, sous peine de rendre l'info affichée inutilisable.

---

## 6. Versionnage des applis

Format `vX.Y — JJ/MM/AAAA` : **X** = nouvelle fonctionnalité, **Y** = correctif ou ajustement.

Propagé depuis une constante unique en tête de script, jamais recopié à la main :

```javascript
const APP_VERSION = "6.7";
const APP_DATE = "31/07/2026";
```

Elle alimente le titre de l'onglet du navigateur **et** le badge visible dans l'en-tête, pour que la version soit lisible côté client sans ouvrir le code.

---

## Journal des révisions

| Version | Date | Objet |
|---|---|---|
| v1.1 | 31/07/2026 | **§1 réécrit** : le picto est mutualisé dans `<dépôt>/shared/`, plus dupliqué dans chaque dossier d'appli. La charte décrivait une convention que 3 applis sur 4 n'appliquaient plus. Ajout de l'avertissement sur le picto racine de `protoprocess.github.io`, du coût caché de `onerror`, et de l'ordre impératif de migration. Ajout du §6 (versionnage). Charte déplacée de Drive vers GitHub, à côté du code qu'elle décrit. |
| v1.0 | 25/07/2026 | Version initiale, extraite de `appro-v2.html`. |
