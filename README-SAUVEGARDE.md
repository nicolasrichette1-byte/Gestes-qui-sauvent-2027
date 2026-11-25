# Enregistrer les données du site (guide pas à pas)

## Le problème constaté

Le site est hébergé sur GitHub Pages, qui sert uniquement des fichiers statiques :
aucune donnée saisie (propositions de sessions, validations, jour fermé, nom…) ne peut
y être conservée entre deux visites sans un service de stockage en ligne.

Le site est donc livré avec trois niveaux de sauvegarde automatiques :

| Mode | Quand | Données |
|------|-------|---------|
| **Firebase (partagé)** | après avoir suivi ce guide | visibles par tout le monde, en temps réel |
| **Local** (par défaut) | sans configuration | conservées dans le navigateur de l'appareil uniquement |
| **Mémoire** | si le navigateur bloque tout stockage | perdues à la fermeture de la page |

Un bandeau discret sur le site indique le mode actif.

## Activer l'enregistrement partagé (gratuit, ~5 minutes)

1. Créer un projet gratuit sur [console.firebase.google.com](https://console.firebase.google.com)
   (par exemple `gestes-qui-sauvent-2027`). Aucune carte bancaire n'est demandée.

2. Dans le menu **Build → Authentication → Get started**, activer le fournisseur
   **Anonyme**.

3. Dans le menu **Build → Firestore Database → Create database** :
   - choisir une localisation proche (par ex. `eur3`), mode « production »,
   - ouvrir l'onglet **Rules** et coller ces règles :

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /artifacts/{appId}/public/data/{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

4. Dans **Paramètres du projet → Vos applications → Web (</>)**, enregistrer une
   application Web, puis copier les trois valeurs affichées dans le fichier
   `firebase-config.js` du dépôt :

   ```js
   window.FIREBASE_CONFIG = {
     apiKey: 'AIza…',
     authDomain: '………firebaseapp.com',
     projectId: 'gestes-qui-sauvent-2027'
   };
   ```

5. Committer et pousser le fichier modifié : GitHub Pages publie la nouvelle
   version automatiquement. Les prochaines saisies sont désormais enregistrées
   en ligne et partagées entre DAFPIC, Croix-Rouge et Groupama en temps réel.

## Remarques

- Ces trois valeurs ne sont pas des secrets : une application Web Firebase est
  toujours identifiable publiquement. C'est le jeu de règles Firestore ci-dessus
  qui protège les données.
- Le plan gratuit (Spark) est largement suffisant pour 10 sessions et quelques
  validations.
- Le site reste 100 % fonctionnel sans Firebase : il enregistre alors dans le
  navigateur (mode local), pratique pour tester en solo.
