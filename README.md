# Sentinelle — site vitrine

Page unique, statique, sans dépendance ni build. Tout tient dans `index.html`
(HTML + CSS + JS en ligne), plus `fonts/` (4 fichiers) et `og.png`.
**Aucune requête vers un tiers au chargement** : les polices sont auto-hébergées.

```
index.html          la page entière
fonts/*.woff2       Faustina, IBM Plex Sans, Martian Mono — 128 Ko au total
og.png              carte de partage 1200×630
logo.svg            la marque, autonome et réutilisable (deck, presse)
image/              masters du logo fournis (SVG exporté, PNG 2720px, composant React)
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

Théorie unique, en niveaux de gris (aucune teinte) — les jetons sont déclarés
en tête de `<style>` :

```
--blanc  #FFFFFF   fond des bandes par défaut
--nuage  #F4F4F5   fond des bandes alternées (.band--papier), surface des
                   cartes sur fond blanc — l'alternance est volontairement
                   discrète : 255,255,255 contre 244,244,245
--brume  #E8E8EA   surface-2 (dots inactifs du graphique, fonds secondaires)
--ardoise #6B6D74  texte-2 (secondaire, légendes)
--suie   #313236   accent-texte / semantique (pastille « Prévu »)
--encre  #17181B   texte principal
--noir   #000000   accent fort : CTA, liens actifs, focus, surlignage
```

Contraste vérifié pour chaque paire (`--ardoise` sur `--nuage` : 4,70:1, le
plus serré du lot ; tout le reste dépasse 11:1). Un seul thème, pas de variante
sombre : `color-scheme:light` est fixé en dur, il n'y a rien à basculer selon
les préférences du système.

Pour réintroduire une couleur d'accent plus tard, il suffit de repointer
`--accent` et `--accent-texte` dans `:root` — tous les composants (bouton,
onglets actifs, liens, tableau) les consomment par le rôle, pas par la teinte.

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
