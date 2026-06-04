# AccèsCulture — version FRONT (SPA)

> Plateforme communautaire et solidaire où les personnes en situation de handicap partagent leurs avis 
sur **l'accessibilité des lieux de culture** — théâtres, musées, expositions, salles de concert, médiathèques, 
patrimoine, galeries… — **ville par ville**.

Application **monopage** (Single Page Application) **100 % côté navigateur** : 
un seul fichier `index.html`, **aucun serveur**, **aucune base de données** à installer. 
Les données sont stockées localement dans le navigateur (IndexedDB + localStorage).

🔗 **Démonstration en ligne :** <https://pythonman83.github.io/AccessCulture/>

---

## Sommaire

- [Aperçu](#aperçu)
- [Fonctionnalités](#fonctionnalités)
- [Démarrage rapide](#démarrage-rapide)
- [Comptes de démonstration](#comptes-de-démonstration)
- [Structure du projet](#structure-du-projet)
- [Stockage des données](#stockage-des-données)
- [Sauvegarde, restauration & réinitialisation](#sauvegarde-restauration--réinitialisation)
- [Accessibilité](#accessibilité)
- [Technologies](#technologies)
- [Personnalisation](#personnalisation)
- [Dépannage (FAQ)](#dépannage-faq)
- [Licence & auteur](#licence--auteur)

---

## Aperçu

AccèsCulture permet à une communauté de :

- **publier des avis** d'accessibilité sur un lieu culturel (note, ville, type de lieu, points positifs et négatifs, photos) ;
- **consulter et filtrer** les avis (par ville, type de lieu, note) ;
- **échanger** sous chaque avis via des commentaires ;
- **gérer ses contributions** dans un espace personnel ;
- **administrer** l'ensemble (membres, avis, commentaires, sauvegardes) pour les comptes administrateurs.

Le tout fonctionne **hors-ligne** une fois la page chargée : aucune donnée n'est envoyée vers un serveur.

---

## Fonctionnalités

| Domaine | Détail |
| --- | --- |
| **Avis** | Dépôt, consultation, modification, suppression. Note sur 5, ville, type de lieu, texte « les plus / les moins », photos multiples. |
| **Types de lieux** | Théâtre · Musée · Exposition · Salle de concert / Opéra · Médiathèque / Bibliothèque · Monument / Patrimoine · Galerie d'art · Autre lieu culturel. |
| **Photos** | Glisser-déposer, redimensionnement **dans le navigateur** (max 1280 px, JPEG ≈ 0,82), aperçu en diaporama. Jusqu'à 8 images par avis. |
| **Commentaires** | Fil d'échange sous chaque avis ; suppression réservée à l'auteur ou à un administrateur. |
| **Comptes** | Inscription, connexion, déconnexion. Mots de passe **hachés** avant stockage. Bouton « œil » pour afficher/masquer la saisie. |
| **Espace membre** | Statistiques personnelles (nombre d'avis, note moyenne) et liste de ses contributions. |
| **Administration** | Tableau de bord, gestion des membres (rôle, activation), des avis (statut actif/masqué) et des commentaires ; export / import JSON ; réinitialisation. |
| **Bilingue** | Bascule **Français ⇄ Anglais** intégrale et instantanée (dictionnaire interne, sans rechargement, sans API externe). |
| **Temps réel** | Date et heure en continu, **météo géolocalisée** (avec accord de l'utilisateur). |
| **Confort** | Thème **clair / sombre**, bouton « retour en haut » avec son d'ascenseur, notifications (toasts), bouton « Lire plus » sur les textes longs. |

---

## Démarrage rapide

Aucune installation, aucune dépendance à télécharger.

### Option 1 — Ouverture directe

1. Téléchargez ou clonez ce dépôt.
2. Double-cliquez sur **`index.html`** : l'application s'ouvre dans votre navigateur.

> Au tout premier lancement, un **compte administrateur** et quelques **avis d'exemple** sont créés automatiquement. Rien à configurer.

### Option 2 — Serveur local (recommandé pour le développement)

Un petit serveur statique évite certaines restrictions des navigateurs et permet le rechargement automatique :

```bash
# Avec l'extension « Live Server » de VS Code : clic droit sur index.html → "Open with Live Server"

# …ou avec Python :
python -m http.server 8000
# puis ouvrez http://localhost:8000

# …ou avec Node :
npx serve
```

### Option 3 — GitHub Pages

Le projet est publiable tel quel sur **GitHub Pages** (branche `main`, dossier racine) : il s'agit d'un site purement statique.

---

## Comptes de démonstration

| Rôle | Identifiant | Mot de passe |
| --- | --- | --- |
| **Administrateur** | `boule` | `boule123` |
| **Membre** | `camille` | `demo1234` |

> ⚠️ Ces comptes sont destinés à la **démonstration**. Pensez à les modifier (ou à réinitialiser les données) avant toute mise en ligne réelle.

---

## Structure du projet

```text
AccessCulture/
├── index.html                  ← l'application complète (HTML + CSS + JavaScript)
├── README.md                   ← ce fichier
├── assets/
│   └── img/
│       └── favicon.webp        ← icône de l'application
└── images/                     ← visuels de démonstration (avis d'exemple)
    ├── favicon.png
    └── …
```

### Organisation interne de `index.html`

Bien que tout tienne dans un seul fichier, le code est organisé en blocs clairs :

| Bloc | Rôle |
| --- | --- |
| `<head>` | Métadonnées (SEO / Open Graph), polices, Bootstrap, favicon, titre. |
| `<style>` | Feuille de style complète (thème clair/sombre, palette « aubergine + or », composants, diaporamas). |
| `<body>` | Barre de navigation, zone `#view` (les vues y sont injectées), pied de page. |
| `<script>` principal | Modèle de données (IndexedDB), routage par `#/`, vues (accueil, détail, dépôt, connexion, espace, administration), amorçage et initialisation. |
| Scripts annexes | Œil du mot de passe, bouton « Lire plus », horloge, météo, traduction FR ⇄ EN + gestion du thème/langue. |

---

## Stockage des données

L'application est **indépendante** de toute autre SPA installée sur le même navigateur : elle utilise son propre espace de stockage.

| Type | Clé / Nom | Contenu |
| --- | --- | --- |
| **IndexedDB** | `AccesCultureDB` | Trois magasins d'objets : `users`, `reviews` (avis + photos en data-URL), `comments`. |
| **localStorage** | `cult.session` | Session du membre connecté. |
| **localStorage** | `cult.theme` | Thème choisi (clair / sombre). |
| **localStorage** | `cult.lang` | Langue choisie (FR / EN). |

Toutes les données restent **sur la machine de l'utilisateur**. Aucune n'est transmise à un serveur (à l'exception de l'appel météo, voir [Personnalisation](#personnalisation)).

---

## Sauvegarde, restauration & réinitialisation

Depuis le menu **Administration → onglet « Données »** :

- **Exporter** : télécharge l'intégralité de la base dans un fichier `acces-culture-export.json`.
- **Importer** : restaure une base à partir d'un fichier d'export précédent (un garde-fou empêche d'importer une sauvegarde sans administrateur actif).
- **Réinitialiser** : vide la base et recrée les données de démonstration.

Pour une remise à zéro manuelle : ouvrez les outils de développement du navigateur (**F12** → onglet **Application** → **IndexedDB** → supprimez `AccesCultureDB`).

---

## Accessibilité

L'accessibilité est au cœur du projet — c'est son sujet même.

- Contrastes conformes **WCAG AA / AAA** en thèmes clair et sombre.
- Police **Atkinson Hyperlegible** (conçue pour la lisibilité).
- Navigation **au clavier** et lien « Aller au contenu ».
- Rôles et attributs **ARIA**, zone d'annonces pour les lecteurs d'écran (`aria-live`).
- Tailles de police confortables et respect de `prefers-reduced-motion`.

---

## Technologies

- **HTML5** sémantique
- **CSS3** (variables, glassmorphism, thème clair/sombre)
- **JavaScript** « vanilla » (ES5/ES6, sans framework)
- **Bootstrap 5.3** + **Bootstrap Icons** (via CDN)
- **IndexedDB** & **localStorage** pour la persistance
- **Web Audio API** (son du bouton ascenseur)
- **Geolocation API** + **OpenWeatherMap** (météo temps réel)

---

## Personnalisation

- **Types de lieux** : modifiez le tableau `LIEUX` en haut du `<script>` principal.
- **Limites des images** : constantes `MAX_IMAGES`, `MAX_DIM`, `JPEG_QUALITY`.
- **Couleurs / thème** : variables CSS `--acc-*` dans le bloc `<style>`.
- **Météo** : le script météo utilise une clé **OpenWeatherMap**. Remplacez-la par la vôtre pour un usage en production. Si l'API ne répond pas, l'affichage reste vide **sans bloquer** l'application.

---

## Dépannage (FAQ)

**Les avis n'apparaissent pas / la page reste vide.**
Vérifiez que votre navigateur autorise IndexedDB (désactivé en navigation privée stricte sur certains navigateurs). Essayez l'**Option 2** (serveur local).

**La météo ne s'affiche pas.**
La géolocalisation a peut-être été refusée, ou la clé OpenWeatherMap a expiré. C'est sans conséquence sur le reste de l'application.

**Je veux repartir de zéro.**
Administration → Données → Réinitialiser, ou supprimez `AccesCultureDB` dans les outils de développement.

**Mes données ont disparu après avoir changé de navigateur.**
C'est normal : le stockage est **local au navigateur**. Utilisez l'export JSON pour transférer vos données.

---

## Licence & auteur

Projet pédagogique réalisé dans le cadre de la formation **DWWM (Développeur Web et Web Mobile)**.

**Auteur :** Jean-Claude Lugo — Formateur DWWM
**GitHub :** [pythonman83](https://github.com/pythonman83)

---

*AccèsCulture — l'accessibilité des lieux de culture racontée par celles et ceux qui les vivent.*
