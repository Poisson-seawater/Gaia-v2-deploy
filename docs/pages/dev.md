# Coder sur Gaïa

Tu veux contribuer au code. Cette page te dit ce qu'est le projet techniquement, et dans quel ordre lire la doc pour être opérationnel vite. La stack en une ligne : **React 19 + TypeScript + Vite**, carte en **D3-Geo**, données générées localement par le code + une couche **Supabase** pour la contribution.

## 1. Vue d'ensemble

Commence ici. Ce que fait le projet, la stack complète, la structure des dossiers et les scripts npm.

→ [Vue d'ensemble](#/readme)

## 2. Architecture

Le cœur : le modèle fractal, le flux de données (`fractalTree` → `craftData` → `metrics`), le pipeline d'ingestion et la couche Supabase.

→ [Architecture](#/architecture)

## 3. Mise en route

Le pas-à-pas concret : cloner, installer, variables d'environnement, lancer le serveur de dev, ta première modif.

→ [Guide d'onboarding](#/onboarding)

## 4. Référence API

Le dictionnaire que tu gardes ouvert en codant : modules exportés, composants React, utilitaires.

→ [Référence API](#/api-reference)

## 5. Vérifier ton changement

Une fois le code modifié, la [Carte de l'app](#/carte-de-l-app) sert de check-list de non-régression :
une ligne de test par fonction, à rejouer manuellement (ou via `npm run test:e2e`, le harnais Playwright
local-only et read-only contre le Supabase de prod).

→ [Carte de l'app](#/carte-de-l-app)
