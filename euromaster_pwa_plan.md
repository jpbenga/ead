# Plan d'Architecture - PWA Fiche d'Ordre de Réparation Euromaster

## 1. Vue d'ensemble du projet

### 1.1 Objectif
Développer une Progressive Web Application (PWA) permettant aux agents EAD d'Euromaster de saisir numériquement les fiches d'ordre de réparation lors de leurs interventions sur le parc automobile des entreprises clientes.

### 1.2 Contraintes techniques
- **Framework** : Angular 17+ avec architecture standalone
- **Styling** : Tailwind CSS avec thème Euromaster
- **Persistance** : localStorage avec format JSON
- **Type d'application** : PWA avec fonctionnement hors-ligne
- **Expérience utilisateur** : Multi-étapes avec progression en temps réel

### 1.3 Périmètre fonctionnel
- Saisie des 5 sections prioritaires : Véhicule, État du véhicule, Conducteur, Pneumatiques, Gardiennage
- Gestion journalière des fiches avec récapitulatif
- Modification des fiches existantes
- Export et envoi par email des fiches quotidiennes

## 2. Architecture applicative

### 2.1 Structure modulaire
```
├── Core Module (Services partagés)
├── Shared Module (Composants réutilisables)
├── Feature Modules
│   ├── Repair Order Module (Saisie des fiches)
│   ├── Dashboard Module (Vue d'ensemble journalière)
│   └── Export Module (Récapitulatif et envoi)
└── Assets (Images, styles, thème Euromaster)
```

### 2.2 Composants principaux

#### 2.2.1 Wizard de saisie (Composant orchestrateur)
- Navigation entre les 5 étapes
- Calcul de progression en temps réel
- Sauvegarde automatique à chaque étape
- Validation avant passage à l'étape suivante

#### 2.2.2 Composants par section
- **Véhicule** : Informations générales du véhicule
- **État véhicule** : Diagramme interactif avec saisie des millimètres + remarques
- **Conducteur** : Informations du conducteur selon sections stabilotées
- **Pneumatiques** : Sélection saisonnalité (été/hiver/4-saisons) pour AV et AR
- **Gardiennage** : Champs de gardiennage

#### 2.2.3 Composants transversaux
- Barre de progression globale
- Navigation entre étapes
- Indicateur de sauvegarde
- Composant de diagramme véhicule interactif

## 3. Modèle de données

### 3.1 Structure principale - RepairOrder
- **Métadonnées** : ID unique, date, agent, statut (brouillon/terminé/envoyé)
- **Véhicule** : Immatriculation, marque, modèle, kilométrage (selon sections stabilotées)
- **État véhicule** : 4 mesures en mm (AV gauche/droite, AR gauche/droite) + remarques
- **Conducteur** : Champs selon sections stabilotées de la fiche originale
- **Pneumatiques** : Saisonnalité AV et AR (été/hiver/4-saisons)
- **Gardiennage** : Champs de gardiennage selon sections stabilotées
- **Progression** : Pourcentage de complétion calculé

### 3.2 Gestion des collections
- **Fiches journalières** : Groupement par date
- **Fiches en brouillon** : Fiches non terminées modifiables
- **Historique** : Conservation des fiches envoyées

## 4. Interfaces utilisateur

### 4.1 Thème visuel Euromaster
- **Couleurs principales** : Bleu Euromaster (#1e3a8a), vert (#16a34a), jaune (#fbbf24)
- **Assets** : Logo Euromaster, icônes dans répertoire dédié `/assets`
- **Design system** : Classes Tailwind personnalisées aux couleurs Euromaster
- **Layout** : Mobile-first, responsive design

### 4.2 Écrans principaux

#### 4.2.1 Dashboard journalier
- Liste des fiches du jour
- Statistiques rapides (nombre d'interventions, fiches terminées)
- Bouton "Nouvelle fiche"
- Accès aux fiches en brouillon
- Bouton récapitulatif/export journalier

#### 4.2.2 Wizard de saisie (5 étapes)
- **Étape 1** : Formulaire véhicule (20% progression)
- **Étape 2** : Diagramme véhicule interactif pour mesures + remarques (40%)
- **Étape 3** : Informations conducteur (60%)
- **Étape 4** : Sélecteurs visuels pneumatiques (80%)
- **Étape 5** : Gardiennage (100%)

#### 4.2.3 Récapitulatif journalier
- Liste détaillée des interventions de la journée
- Possibilité de modification des fiches
- Options d'export (PDF, email)
- Validation avant envoi

### 4.3 Composant spécialisé - Diagramme véhicule
- Vue de dessus du véhicule (basée sur l'image fournie)
- 4 zones cliquables correspondant aux roues
- Saisie numérique des millimètres par zone
- Validation des valeurs saisies
- Champ remarques associé

## 5. Gestion des données

### 5.1 Persistance locale
- **Format** : JSON dans localStorage
- **Structure** : Collections indexées par date et ID
- **Capacité** : Optimisation pour limites localStorage
- **Sauvegarde** : Automatique à chaque étape

### 5.2 Gestion des états
- **Brouillon** : Fiche en cours de saisie
- **Terminé** : Fiche complète, prête à l'envoi
- **Envoyé** : Fiche exportée/envoyée

### 5.3 Fonctionnalités de récupération
- Reprise des fiches en brouillon
- Modification des fiches terminées (dans la journée)
- Historique des interventions

## 6. Fonctionnalités avancées

### 6.1 Progressive Web App
- **Service Worker** : Cache des assets pour fonctionnement hors-ligne
- **Manifest** : Installation sur l'écran d'accueil mobile
- **Offline-first** : Synchronisation différée si nécessaire

### 6.2 Export et communication
- **Récapitulatif journalier** : Vue consolidée des interventions
- **Export PDF** : Génération des fiches pour archivage
- **Envoi email** : Transmission des fiches quotidiennes
- **Format standardisé** : Compatible avec les processus existants

### 6.3 Validation et contrôles
- **Validation temps réel** : Champs obligatoires, formats
- **Contrôles métier** : Cohérence des données saisies
- **Indicateurs visuels** : Champs manquants, erreurs

## 7. Expérience utilisateur

### 7.1 Navigation fluide
- **Progression visuelle** : Barre de progression permanente avec pourcentage
- **Navigation libre** : Aller-retour entre étapes (si données valides)
- **Sauvegarde transparente** : Pas de perte de données

### 7.2 Efficacité opérationnelle
- **Saisie rapide** : Interfaces optimisées pour la rapidité
- **Validation intelligente** : Prévention des erreurs
- **Récupération facile** : Accès rapide aux fiches en cours

### 7.3 Adaptabilité
- **Multi-device** : Tablette et smartphone
- **Portrait/Paysage** : Adaptation automatique
- **Accessibilité** : Respect des standards d'accessibilité

## 8. Architecture technique

### 8.1 Services Angular
- **StorageService** : Gestion localStorage avec format JSON
- **RepairOrderService** : Logique métier des fiches
- **ExportService** : Génération PDF et envoi email
- **ValidationService** : Règles de validation métier
- **ProgressService** : Calcul de la progression en temps réel

### 8.2 Guards et Interceptors
- **CanDeactivate** : Prévention perte de données
- **Offline Interceptor** : Gestion des requêtes hors-ligne

### 8.3 State Management
- **Réactif** : RxJS pour la gestion des états
- **Immutable** : Prévention des effets de bord
- **Optimisé** : Change detection OnPush

## 9. Spécifications fonctionnelles détaillées

### 9.1 Section Véhicule
- Champs selon sections stabilotées de la fiche originale
- Validation des formats (immatriculation, etc.)
- Auto-complétion marques/modèles

### 9.2 Section État du véhicule
- **Diagramme interactif** : Vue de dessus avec 4 zones cliquables
- **Saisie millimètres** : Roue AV gauche, AV droite, AR gauche, AR droite
- **Remarques** : Champ texte libre pour commentaires
- **Validation** : Valeurs numériques uniquement

### 9.3 Section Conducteur
- Champs issus des sections stabilotées uniquement
- Validation des champs obligatoires

### 9.4 Section Pneumatiques
- **Pneus AV** : Sélection saisonnalité (été/hiver/4-saisons)
- **Pneus AR** : Sélection saisonnalité (été/hiver/4-saisons)
- Interface visuelle avec icônes

### 9.5 Section Gardiennage
- Champs selon sections stabilotées de la fiche originale
- Validation des champs obligatoires

## 10. Déploiement et maintenance

### 10.1 Build et optimisation
- **Tree shaking** : Optimisation du bundle
- **Lazy loading** : Chargement à la demande
- **PWA optimizations** : Cache strategy optimale

### 10.2 Monitoring
- **Analytics** : Suivi d'usage
- **Error tracking** : Détection des erreurs utilisateur
- **Performance** : Métriques de performance

### 10.3 Structure des assets
```
src/assets/
├── images/
│   ├── euromaster-logo.svg
│   ├── euromaster-logo.png
│   ├── vehicle-diagram.svg
│   └── icons/
│       ├── tire-summer.svg
│       ├── tire-winter.svg
│       └── tire-all-season.svg
└── styles/
    ├── euromaster-theme.scss
    └── tailwind-euromaster.scss
```

## 11. Critères de réussite

### 11.1 Performance
- Temps de chargement < 3 secondes
- Navigation fluide entre étapes
- Sauvegarde instantanée

### 11.2 Fonctionnel
- 100% des sections stabilotées implémentées
- Progression temps réel fonctionnelle
- Export journalier opérationnel

### 11.3 Technique
- PWA installable
- Fonctionnement hors-ligne
- Données JSON bien structurées dans localStorage

Cette architecture garantit une application robuste, performante et adaptée aux besoins terrain des agents Euromaster tout en respectant les contraintes techniques et l'expérience utilisateur requise.