# Sentinelle — site vitrine

Page unique, statique, sans dépendance ni build. Tout tient dans `index.html`
(HTML + CSS + JS en ligne). Seule ressource externe : les polices Google Fonts
(Faustina, IBM Plex Sans, Martian Mono).

## Mettre en ligne

**Netlify (glisser-déposer, ~2 min)**
1. Ouvrir https://app.netlify.com/drop
2. Y déposer **le dossier** qui contient `index.html`
3. L'URL `https://<nom>.netlify.app` est immédiate. Elle se renomme dans
   *Site configuration → Change site name*, et un domaine perso s'ajoute dans
   *Domain management*.

**Vercel** : https://vercel.com/new → *Deploy static* → déposer le dossier.

**GitHub Pages** : pousser ce dossier sur un dépôt, puis *Settings → Pages →
Deploy from branch → main / (root)*.

## Ce qu'il faut retoucher avant publication

| Où | Quoi |
|---|---|
| `mailto:` (3 occurrences) | remplacer par un lien direct vers le pitch deck s'il est hébergé quelque part |
| `<meta property="og:url">` | à ajouter une fois l'URL finale connue |
| Bandeau `og:image` | aucune image sociale pour l'instant : les partages afficheront titre + description seuls |

## Langues

Le contenu existe en français et en anglais dans la même page (`<span class="fr">`
et `<span class="en">`), la bascule se fait par l'attribut `data-lang` sur `<html>`.
Le français est la version par défaut ; un visiteur dont le navigateur n'est pas
en français arrive directement en anglais, et son choix est mémorisé.
Pour forcer le français pour tout le monde, supprimer dans le script la ligne :

```js
else if((navigator.language||'').slice(0,2)!=='fr'){langue('en');}
```

## Contenu réglementaire

Les chiffres cités sont publics (521 établissements de microfinance en zone CEMAC
fin 2024, dont 384 au Cameroun ; 6 règlements COBAC en vigueur). Le tableau de la
section « Ancrage réglementaire » résume les obligations de R-2020/01 **par
catégorie** : aucun numéro d'article n'est cité, pour ne rien affirmer qui ne soit
vérifié. Les réclamations montrées sont reconstituées et ne proviennent d'aucun
établissement identifiable.
