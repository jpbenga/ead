# Guide de Développement - PWA Fiche d'Ordre de Réparation Euromaster

Ce document est un guide pratique qui détaille, étape par étape, la création de l'architecture de la PWA Euromaster avec les commandes npm et ng (Angular CLI) nécessaires.

## Étape 1 : Initialisation du Projet et Dépendances

Nous commençons par créer un nouveau projet Angular, puis nous ajoutons Tailwind CSS et le support PWA.

### Installer Angular CLI (si ce n'est pas déjà fait)

```bash
npm install -g @angular/cli
```

### Créer le projet Angular 17+ (en mode standalone)

```bash
ng new euromaster-pwa --standalone --style=scss
cd euromaster-pwa
```

### Ajouter le support PWA au projet

```bash
ng add @angular/pwa
```

Cette commande configure le Service Worker et le manifeste de l'application.

### Installer Tailwind CSS

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init
```

### Configurer Tailwind CSS

Ouvrez le fichier `tailwind.config.js` et configurez la section content pour qu'elle inclue tous vos fichiers de composants :

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{html,ts}",
  ],
  theme: {
    extend: {
      colors: {
        'euromaster-blue': '#1e3a8a',
        'euromaster-green': '#16a34a',
        'euromaster-yellow': '#fbbf24',
      }
    },
  },
  plugins: [],
}
```

Ouvrez le fichier `src/styles.scss` et ajoutez les directives Tailwind :

```scss
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Étape 2 : Création de l'Architecture des Dossiers

Nous allons maintenant créer la structure de dossiers principale pour organiser notre code de manière logique.

```bash
# Se placer dans le dossier src/app
cd src/app

# Créer les dossiers principaux
mkdir core
mkdir features
mkdir shared
mkdir models
```

### Structure des dossiers

- **core/** : Pour les services uniques et globaux (ex: localStorage)
- **features/** : Pour les fonctionnalités principales (dashboard, wizard de saisie)
- **shared/** : Pour les composants, pipes et directives réutilisables
- **models/** : Pour les interfaces et types TypeScript

## Étape 3 : Génération des Services du Core

Ces services contiendront la logique métier principale et la gestion des données.

```bash
# Générer les services dans le dossier core/
ng generate service core/storage/local-storage
ng generate service core/repair-order/repair-order
ng generate service core/export/export
ng generate service core/validation/validation
ng generate service core/progress/progress
```

## Étape 4 : Génération des Composants Shared

Ces composants seront réutilisés à travers différentes fonctionnalités de l'application.

```bash
# Générer les composants réutilisables
ng generate component shared/components/progress-bar
ng generate component shared/components/step-navigation
ng generate component shared/components/save-indicator

# Générer le composant spécialisé pour le diagramme du véhicule
ng generate component shared/components/vehicle-diagram
```

## Étape 5 : Génération des Features (Fonctionnalités)

Nous créons ici les composants qui représentent les écrans principaux de l'application.

### 5.1 Dashboard (Tableau de bord)

```bash
# Page principale du tableau de bord
ng generate component features/dashboard-page

# Composant pour afficher la liste des fiches journalières
ng generate component features/dashboard-page/components/daily-list
```

### 5.2 Wizard de Saisie (Repair Order)

C'est le cœur de l'application, avec un composant "orchestrateur" et un composant pour chaque étape.

```bash
# Composant orchestrateur du wizard
ng generate component features/repair-order-wizard

# Composants pour chaque étape du wizard
ng generate component features/repair-order-wizard/components/vehicle-step
ng generate component features/repair-order-wizard/components/vehicle-state-step
ng generate component features/repair-order-wizard/components/driver-step
ng generate component features/repair-order-wizard/components/tires-step
ng generate component features/repair-order-wizard/components/guarding-step
```

### 5.3 Export et Récapitulatif

```bash
# Composant pour la page de récapitulatif journalier
ng generate component features/export-summary
```

## Étape 6 : Définition des Modèles de Données

Créez manuellement le fichier qui contiendra les interfaces TypeScript décrivant la structure de vos données.

**Créez le fichier :** `src/app/models/repair-order.model.ts`

Ajoutez le contenu suivant dans ce fichier :

```typescript
// src/app/models/repair-order.model.ts

export interface VehicleState {
  frontLeft: number | null;
  frontRight: number | null;
  rearLeft: number | null;
  rearRight: number | null;
  remarks: string;
}

export interface Tires {
  front: 'summer' | 'winter' | 'all-season' | null;
  rear: 'summer' | 'winter' | 'all-season' | null;
}

export interface RepairOrder {
  id: string; // ex: YYYY-MM-DD-HHMMSS
  creationDate: Date;
  agentId: string;
  status: 'draft' | 'completed' | 'sent';
  progress: number;

  vehicle: {
    registration: string;
    brand: string;
    model: string;
    mileage: number;
  };

  vehicleState: VehicleState;

  driver: {
    name: string;
    phone: string;
  };

  tires: Tires;

  guarding: {
    // ... champs de gardiennage
  };
}
```

## Étape 7 : Configuration du Routage (app.routes.ts)

Enfin, connectons toutes ces pages dans le fichier de routage principal.

Ouvrez `src/app/app.routes.ts` et configurez les routes pour naviguer entre le tableau de bord, le wizard de saisie et le récapitulatif.

```typescript
// src/app/app.routes.ts

import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: 'dashboard',
    loadComponent: () => import('./features/dashboard-page/dashboard-page.component').then(m => m.DashboardPageComponent),
    title: 'Tableau de bord'
  },
  {
    path: 'new-order',
    loadComponent: () => import('./features/repair-order-wizard/repair-order-wizard.component').then(m => m.RepairOrderWizardComponent),
    title: 'Nouvelle Fiche'
  },
  {
    path: 'edit-order/:id',
    loadComponent: () => import('./features/repair-order-wizard/repair-order-wizard.component').then(m => m.RepairOrderWizardComponent),
    title: 'Modifier Fiche'
  },
  {
    path: 'summary',
    loadComponent: () => import('./features/export-summary/export-summary.component').then(m => m.ExportSummaryComponent),
    title: 'Récapitulatif Journalier'
  },
  {
    path: '',
    redirectTo: '/dashboard',
    pathMatch: 'full'
  },
  {
    path: '**',
    redirectTo: '/dashboard'
  }
];
```

## Conclusion

Avec ce guide, vous disposez d'un squelette de projet complet et bien structuré, prêt pour l'implémentation de la logique métier dans chaque fichier généré.

### Architecture finale du projet

```
src/app/
├── core/
│   ├── storage/
│   ├── repair-order/
│   ├── export/
│   ├── validation/
│   └── progress/
├── features/
│   ├── dashboard-page/
│   ├── repair-order-wizard/
│   └── export-summary/
├── shared/
│   └── components/
├── models/
└── app.routes.ts
```

Cette structure modulaire facilite la maintenance, les tests et l'évolution future de l'application PWA Euromaster.