# Sentinelle — site vitrine

Page unique, statique, sans dépendance ni build. Tout tient dans `index.html`
(HTML + CSS + JS en ligne), plus `fonts/`, `og.png` et `captures/`.
**Aucune requête vers un tiers au chargement** : les polices sont auto-hébergées.

```
index.html          la page entière
fonts/*.woff2       Faustina, IBM Plex Sans, Martian Mono — 128 Ko au total
og.png              carte de partage 1200×630 (noir/blanc)
logo.svg            la marque, autonome et réutilisable (deck, presse)
image/              masters du logo fournis (SVG exporté, PNG 2720px, composant React)
captures/           2 maquettes d'écran affichées dans « Aperçu produit »
```

## À faire au déploiement

Une seule chose, mais elle est obligatoire : **remplacer `https://EXEMPLE.tld`
par l'URL finale**. Elle apparaît 6 fois dans `index.html` (bloc signalé en tête
de `<head>`, plus le JSON-LD).

```sh
sed -i 's#https://EXEMPLE.tld#https://votre-domaine.tld#g' index.html
grep -c 'EXEMPLE.tld' index.html   # doit retourner 0
```

Sans ça : pas de `canonical`, et surtout **aucun aperçu au partage** — Slack,
LinkedIn et WhatsApp ignorent une `og:image` en chemin relatif.

## Mettre en ligne

**Netlify (glisser-déposer, ~2 min)**
1. Ouvrir https://app.netlify.com/drop
2. Y déposer **le dossier** qui contient `index.html` (avec `fonts/` et `og.png`)
3. L'URL `https://<nom>.netlify.app` est immédiate. Elle se renomme dans
   *Site configuration → Change site name*, et un domaine perso s'ajoute dans
   *Domain management*.

**Vercel** : https://vercel.com/new → *Deploy static* → déposer le dossier.

**GitHub Pages** : pousser ce dossier sur un dépôt, puis *Settings → Pages →
Deploy from branch → main / (root)*.

Vérifier l'aperçu social après coup sur https://www.opengraph.xyz/.

## Langues

Le contenu existe en français et en anglais dans la même page (`<span class="fr">`
et `<span class="en">`), la bascule se fait par l'attribut `data-lang` sur `<html>`.

La langue est résolue par un **script en tête de `<head>`**, avant le premier
rendu : un visiteur anglophone ne voit jamais la page peindre en français puis
basculer. Ne pas déplacer ce bloc en bas de page.

Le français est la version par défaut ; un visiteur dont le navigateur n'est pas
en français arrive directement en anglais, et son choix est mémorisé.
Pour forcer le français pour tout le monde, supprimer dans le script du `<head>`
la ligne :

```js
else if((navigator.language||'').slice(0,2)!=='fr'){l='en';}
```

Le titre de l'onglet, la `meta description` et les `aria-label` suivent la bascule
(cf. `applique()` dans le script de bas de page).

> Note SEO : les deux langues coexistent dans le DOM, donc Google indexe les deux
> textes dans le même document. C'est le prix du site en un seul fichier. Si le
> référencement devient un objectif, il faudra scinder en `/fr/` et `/en/` avec
> des `hreflang`.

## Palette

Théorie unique, en niveaux de gris (aucune teinte) — fond noir dominant,
dans l'esprit de palantir.com. Les jetons sont déclarés en tête de `<style>` :

```
--noir    #000000   fond des bandes par défaut, CTA au survol
--suie    #0A0A0B   fond des bandes alternées (.band--papier) — l'alternance
                    est volontairement discrète : 0,0,0 contre 10,10,11
--carbone #1B1C1E   surface des cartes / fenêtres produit
--fer     #2B2C2F   surface-2 (dots inactifs du graphique, fonds secondaires)
--argent  #9A9CA2   texte-2 (secondaire, légendes)
--brume   #D6D7DA   accent-texte / semantique (pastille « Prévu »)
--blanc   #FFFFFF   texte principal, accent fort : CTA, liens actifs, focus
```

Contraste vérifié pour chaque paire (`--argent` sur `--suie` : 7,21:1, le plus
serré du lot ; tout le reste dépasse 14:1). Un seul thème, pas de variante
claire : `color-scheme:dark` est fixé en dur, il n'y a rien à basculer selon
les préférences du système.

Pour repasser en thème clair (l'inverse existait avant ce commit), il faut
retourner chaque jeton ET les ~20 références directes à `--noir`/`--blanc`
dans les composants (CTA, onglets actifs, liens, surlignage, dots du
graphique — cf. l'historique git) : ce ne sont pas de simples alias, le sens
de l'accent s'inverse avec le fond.

## Carte CEMAC

Le bloc « Zone couverte » (section « Le constat ») est une carte SVG inline
des 6 États membres, Cameroun en évidence. **Les frontières sont projetées
depuis de vraies données géographiques** (`world.geo.json`, projection
équirectangulaire recentrée sur la zone, simplifiée par un algorithme de
Douglas-Peucker) — pas dessinées à main levée. Le script de génération n'est
pas conservé dans le dépôt ; si les tracés doivent être régénérés ou un autre
pays ajouté, repartir d'une source de frontières publiques plutôt que de
retoucher les `<path>` à la main.

## Aperçu produit

La section « Aperçu produit » (entre « Comment ça marche » et « Pourquoi
l'IA ») affiche deux maquettes d'écran en `captures/` :

```
captures/apercu-graphe.png     graphe causal — 17 tickets reliés à une cause
captures/apercu-registre.png   registre des motifs — table à 5 lignes
```

Ce sont des **maquettes construites pour l'occasion** (mêmes polices, mêmes
jetons de couleur que le site, rendues via Chrome headless), pas des captures
d'un produit en production — Sentinelle est en préfiguration, cf. plus bas.
Chaque image porte sa propre mention « Maquette » / « données reconstituées »
en légende, en plus du figcaption bilingue autour d'elle. Ne pas retirer ces
mentions ni présenter ces images comme des captures réelles sans avoir
d'abord un produit qui les justifie.

## Logo

La marque est un double S anguleux incliné à 30°. Elle est **inscrite en dur
dans `index.html`** (deux `<polyline>` en `currentColor`) : pas de requête, et
la couleur suit celle du texte parent.

Deux points à connaître avant d'y toucher :

- **Le `viewBox` d'origine (`0 0 680 400`) est trompeur** : le tracé n'occupe
  que 26 % de sa largeur et 54 % de sa hauteur. La version intégrée est recalée
  sur l'encombrement réel, `0 0 207 245`. Réutiliser les points d'origine sans
  ce recalage donne une marque minuscule et décentrée.
- **Le trait de 8 unités est trop fin en petit** : il rendrait 0,63 px dans
  l'en-tête. Le trait est donc épaissi selon l'usage — 20 dans l'en-tête (20 px
  de haut), 16 sur la carte sociale, 34 dans le favicon. `logo.svg` conserve
  le trait 8 d'origine, prévu pour les grands formats.

**Le favicon n'utilise qu'un seul S.** Sous ~20 px les deux tracés fusionnent en
un bloc illisible ; le S simple reste net jusqu'à 16 px. C'est une réduction
propre au favicon — la marque complète reste dans l'en-tête, sur `og.png` et
dans `logo.svg`. Pour revenir au double S, ajouter la seconde `polyline` dans le
`data:` URI du `<link rel="icon">`.

## Accessibilité

- Lien d'évitement, `:focus-visible` sur tous les éléments focusables.
- Onglets du « flux » : `role="tablist"` complet, flèches ←→ **et** ↑↓,
  `aria-orientation` mis à jour selon la largeur.
- **Sans JavaScript, les quatre étapes du flux s'empilent** au lieu de disparaître.
  Les panneaux sont visibles dans le HTML ; c'est le script qui les replie.
- Palette vérifiée : toutes les paires texte/fond passent WCAG AA (≥ 4,5:1).

## Contenu réglementaire

Les chiffres cités sont publics : **521 établissements de microfinance en zone
CEMAC fin 2024, dont 384 au Cameroun** (le graphique en compte exactement 521,
dont 384 en accent — s'il est modifié, les deux doivent rester cohérents).

Le tableau de la section « Ancrage réglementaire » résume **par catégorie** les
obligations du **Règlement COBAC R-2020/06 du 30 juillet 2020**, relatif au
traitement des réclamations des consommateurs des produits et services bancaires
dans la CEMAC. Aucun numéro d'article n'est cité, pour ne rien affirmer qui ne
soit vérifié.

La colonne « État » indique **« Prévu »**, pas « Couvert » : le produit est en
préfiguration et la colonne décrit le périmètre de la v1, pas des fonctions
déployées. À ne repasser à « Couvert » qu'obligation par obligation, une fois
réellement en production.

Les réclamations montrées sont reconstituées et ne proviennent d'aucun
établissement identifiable.
