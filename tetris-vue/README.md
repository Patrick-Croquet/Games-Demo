# Tetris en Vue.js

Ce projet est une implémentation du jeu classique Tetris en utilisant Vue.js. Il comprend les composants nécessaires pour afficher la grille de jeu, les pièces de Tetris et gérer la logique du jeu.

## Structure du projet

- **public/index.html** : Point d'entrée de l'application avec la structure HTML de base.
- **src/assets** : Dossier pour les ressources statiques (images, styles).
- **src/components/TetrisGrid.vue** : Composant pour afficher la grille de Tetris.
- **src/components/TetrisPiece.vue** : Composant représentant une pièce de Tetris.
- **src/views/Game.vue** : Vue principale du jeu, intégrant les composants de la grille et des pièces.
- **src/App.vue** : Composant racine de l'application.
- **src/main.js** : Point d'entrée JavaScript de l'application.
- **package.json** : Configuration npm pour les dépendances et scripts.
- **babel.config.js** : Configuration pour Babel.
- **vue.config.js** : Configuration spécifique à Vue.

## Installation

1. Clonez le dépôt :
   ```
   git clone <URL_DU_DEPOT>
   ```
2. Accédez au répertoire du projet :
   ```
   cd tetris-vue
   ```
3. Installez les dépendances :
   ```
   npm install
   ```

## Exécution

Pour démarrer l'application, utilisez la commande suivante :
```
npm run serve
```

Ouvrez votre navigateur et accédez à `http://localhost:8080` pour jouer à Tetris !