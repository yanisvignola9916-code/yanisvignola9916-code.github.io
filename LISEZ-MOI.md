# Voyalis — site vitrine statique

235 pages HTML, aucune dépendance externe, aucun serveur applicatif.
Une page de ce site ne contacte **aucun domaine autre que le sien**.

## Voir le site en local

Les liens internes sont absolus (`/destinations/…`) : ils ne fonctionnent
pleinement que servis depuis une racine.

```
cd public
python3 -m http.server 8000
```

puis `http://localhost:8000`. Un double-clic sur `index.html` affiche la page
mais casse la navigation.

## Ce que contient le site

| Page | Nombre |
|---|---|
| Accueil | 1 |
| Fiches destination | 193 |
| Index destinations avec filtres | 1 |
| Pages région | 20 + index |
| Pages catégories « Où partir » | 8 + index |
| Comment ça marche, À propos, FAQ, Contact | 4 |
| Mentions légales, CGU, Confidentialité, Cookies | 4 |
| Passerelle questionnaire, page 404 | 2 |
| sitemap.xml, robots.txt, manifeste | 3 |

Poids total : 8,2 Mo, dont 4,6 Mo de vignettes de partage — servies uniquement
aux réseaux sociaux, jamais chargées par un visiteur. Le HTML représente 3,5 Mo
bruts, ramenés à 1,2 Mo une fois compressé.

## Choix de conception

**Aucune photo n'est requise.** Chaque carte porte une trame géométrique générée,
propre à sa région du monde, avec sa teinte d'accent. Déposer une image dans
`public/images/` la remplace, sans toucher au code. Voir
`voyalis-photos-a-telecharger.md`.

**Une seule police, celle du système.** Titres et corps emploient la police
d'interface de l'appareil — Segoe UI, SF Pro ou Roboto. Aucun fichier de police
n'est téléchargé : zéro requête, zéro octet, aucun décalage de mise en page au
chargement. Ce choix suit le logotype, qui est un sans-serif géométrique sans
ornement ; lui adjoindre un serif aurait introduit une seconde voix.

Pour employer une police de titres particulière, déposer `titres.woff2` dans
`public/polices/` — accompagnée de sa licence, que l'audit vérifie.

**Sept tailles de caractères**, espacées d'un rapport de 1,25, déclarées en
variables `--t0` à `--t6`. Le site en comptait vingt, dont neuf entre 12 et
16 px : un écart d'un demi-pixel ne produit aucune hiérarchie.

**Largeurs de texte en `ch`** et non en pixels. L'unité suit la police
réellement installée ; une largeur fixe donnait 90 caractères par ligne sur un
système et 112 sur un autre, contre un optimum de lecture entre 45 et 75.

**Vignettes de partage.** Chaque page a la sienne, construite à partir de son
propre contenu. Sans elles, un lien partagé sur WhatsApp ou LinkedIn s'affiche nu.

**Le questionnaire n'est pas publié.** `/parcours.html` explique pourquoi et
renvoie vers le catalogue. Le moteur qui l'alimente est écrit et testé ; il
attend des tarifs en temps réel plutôt que des estimations modélisées.

## À faire avant la mise en ligne

Le script d'audit signale ces trois pages comme bloquantes tant que la mention
« à compléter » y figure.

1. **Mentions légales** — raison sociale, adresse, courriel, statut ou SIREN,
   directeur de la publication, puis nom et adresse de l'hébergeur.
   Obligation légale.
2. **Page contact** — l'adresse électronique.
3. **Confidentialité** — la même adresse, pour l'exercice des droits RGPD.
4. **Domaine** — la constante `SITE` de `build/gabarit.py` vaut
   `https://voyalis.travel`. Elle alimente les URL canoniques, le sitemap et les
   vignettes de partage : une erreur ici casse les trois d'un coup.
5. **Redirections** — passer `EN_LIGNE = True` dans `build/generer.py`. Tant que
   c'est `False`, les pages dont le nom a changé sont supprimées ; une fois le
   site publié, elles doivent au contraire devenir des redirections, sinon un
   lien déjà partagé se brise.

## Publier

Le dossier `public/` se dépose tel quel sur n'importe quel hébergement statique —
Netlify, Cloudflare Pages, GitHub Pages, ou un répertoire Apache. Aucune
configuration, aucune base de données.

`404.html` est reconnue automatiquement par Netlify, Cloudflare Pages et GitHub
Pages. Sur Apache, ajouter `ErrorDocument 404 /404.html`.

Activer la compression gzip ou brotli si l'hébergeur ne le fait pas. Le gain est
de 66 % sur l'ensemble du HTML, et bien davantage sur les pages qui comptent :
l'index des destinations passe de 159 à 16 Ko, soit 90 %.

## Régénérer

```
python3 build/extraire.py          # fiches markdown -> pages.json
python3 build/generer_partage.py   # vignettes de partage (reprend là où il s'arrête)
python3 build/generer.py           # produit public/
python3 build/auditer.py           # liens, métadonnées, sitemap, ressources
```

`generer.py` échoue volontairement si une vignette manque : une balise og:image
pointant dans le vide ne produit aucune erreur visible, seulement un partage muet.

De même, ajouter une région au catalogue sans lui écrire de texte dans
`build/regions.py` fait échouer la génération, plutôt que de produire des fiches
pointant vers une page inexistante.

### Les modules

| Fichier | Rôle |
|---|---|
| `gabarit.py` | Charte, CSS, en-tête, pied de page, balises `<head>` |
| `regions.py` | Textes des 20 régions et leurs voisinages |
| `categories.py` | Les 8 catégories « Où partir » |
| `visuels.py` | Trames géométriques par région |
| `marque.py` | Monogramme, favicon, icônes, manifeste |
| `partage.py` | Composition des vignettes 1200 × 630 |
| `auditer.py` | Contrôles automatisés |

## Note sur les prix

Chaque fiche porte un encart précisant que les budgets sont des estimations
modélisées et non des relevés réels. Huit destinations font exception. Cette
mention doit rester tant que la calibration n'est pas généralisée ou remplacée
par des tarifs d'API.

## État de l'audit

Aucun lien mort, aucune page orpheline, aucune balise mal fermée, aucun
identifiant dupliqué, canoniques cohérentes, sitemap complet, aucune dépendance
externe, toutes les vignettes présentes, contrastes conformes au niveau AA.
Seules subsistent les trois pages « à compléter ».
