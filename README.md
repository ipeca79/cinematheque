# 🎬 Cinémathèque privée

Interface web pour naviguer et partager votre collection de films.

## Structure du projet

```
cinematheque/
├── index.html      ← Interface complète (un seul fichier)
├── movies.json     ← Votre liste de films (à personnaliser)
└── README.md
```

## Déploiement sur GitHub Pages

1. Créez un repo GitHub (ex: `cinematheque`)
2. Uploadez `index.html` et `movies.json`
3. Allez dans **Settings → Pages → Source : main branch → Save**
4. Votre site sera disponible sur `https://votrenom.github.io/cinematheque`

## Mettre à jour la liste de films

### Depuis TinyMediaManager

1. Dans TMM : **Outils → Exporter**
2. Choisissez un template JSON ou utilisez la sortie NFO
3. Reformatez selon le schéma ci-dessous (ou demandez un script de conversion)

### Format `movies.json`

Chaque film est un objet JSON avec les champs suivants :

```json
{
  "title": "Titre du film",
  "originaltitle": "Original Title",
  "year": 2023,
  "rating": 7.5,
  "votes": 125000,
  "plot": "Résumé du film...",
  "tagline": "Accroche du film",
  "runtime": 120,
  "genres": ["Action", "Thriller"],
  "country": "France",
  "languages": ["Français", "Anglais"],
  "subtitles": ["Français", "Anglais"],
  "poster": "https://... ou chemin/vers/poster.jpg",
  "fanart": "https://... (optionnel, image de fond)",
  "director": "Nom du réalisateur",
  "actors": ["Acteur 1", "Acteur 2"],
  "studio": "Nom du studio",
  "awards": "Palme d'Or 2023"
}
```

**Seul `title` est obligatoire.** Tous les autres champs sont optionnels.

### Posters locaux

Vous pouvez mettre les affiches dans un dossier `posters/` et référencer :
```json
"poster": "posters/film-titre.jpg"
```

### Import depuis NFO TinyMediaManager

TMM génère des fichiers `.nfo` (XML) par film. Voici un script Python minimal pour convertir :

```python
import os, json, xml.etree.ElementTree as ET

movies = []
for root_dir, dirs, files in os.walk('.'):
    for f in files:
        if f.endswith('.nfo'):
            tree = ET.parse(os.path.join(root_dir, f))
            r = tree.getroot()
            get = lambda tag: r.findtext(tag) or ''
            movie = {
                'title': get('title'),
                'originaltitle': get('originaltitle'),
                'year': int(get('year')) if get('year').isdigit() else None,
                'rating': float(get('rating')) if get('rating') else None,
                'plot': get('plot'),
                'tagline': get('tagline'),
                'runtime': int(get('runtime')) if get('runtime').isdigit() else None,
                'genres': [g.text for g in r.findall('genre')],
                'country': get('country'),
                'director': get('director'),
                'studio': get('studio'),
                'actors': [a.findtext('name') for a in r.findall('actor')],
                'poster': get('thumb'),
            }
            movies.append({k: v for k, v in movie.items() if v})

with open('movies.json', 'w', encoding='utf-8') as out:
    json.dump(movies, out, ensure_ascii=False, indent=2)

print(f'{len(movies)} films exportés')
```

## Fonctionnalités

- 🎬 Affichage en grille avec affiches
- 🔍 Recherche plein texte (titre, réalisateur, acteurs)
- 🎭 Filtres : genre, année, pays, langue, sous-titres
- 📊 Tri : titre, année, note
- ℹ️ Fiche détaillée au clic (résumé, casting, récompenses)
- ❤️ Favoris persistants (localStorage)
- 📤 Export favoris en TXT, CSV, Markdown, JSON
- 📱 Responsive mobile

## Personnalisation

Ouvrez `index.html` et modifiez les variables CSS dans `:root` pour changer les couleurs, ou la variable `.logo` pour renommer la cinémathèque.
