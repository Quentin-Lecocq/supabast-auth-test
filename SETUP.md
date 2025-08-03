# Setup Multi-environnements pour Supabase Auth

## Vue d'ensemble

Ce guide explique comment configurer deux environnements (develop/main) avec deux bases de données Supabase distinctes pour déboguer les problèmes d'authentification Google OAuth en production.

## 1. Configuration Supabase

### Créer deux projets Supabase

1. **Projet Development**
   - Nom : `your-app-dev`
   - Base de données pour les tests

2. **Projet Production** 
   - Nom : `your-app-prod`
   - Base de données pour la production

### Pour chaque projet Supabase

1. Allez dans **Authentication > Providers**
2. Activez **Google**
3. Configurez les Client ID/Secret Google
4. Ajoutez les URLs de redirection appropriées

## 2. Configuration Google Cloud Console

### Créer les OAuth Credentials

1. Allez sur [Google Cloud Console](https://console.cloud.google.com)
2. Créez un projet ou utilisez un existant
3. Activez l'API Google+ 
4. Créez des "OAuth 2.0 Client IDs"

### URLs de redirection autorisées

Pour **Development** :
```
http://localhost:3000/auth/callback
https://your-app-dev.vercel.app/auth/callback
```

Pour **Production** :
```
https://your-app-prod.vercel.app/auth/callback
```

## 3. Configuration Vercel

### Créer deux projets Vercel

1. **Projet Dev**
   - Nom : `your-app-dev`
   - Branche connectée : `develop`
   - Domain : `your-app-dev.vercel.app`

2. **Projet Prod**
   - Nom : `your-app-prod` 
   - Branche connectée : `main`
   - Domain : `your-app-prod.vercel.app`

### Variables d'environnement par projet

**Pour le projet Dev** :
```
NEXT_PUBLIC_SUPABASE_URL=https://your-dev-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-dev-anon-key
```

**Pour le projet Prod** :
```
NEXT_PUBLIC_SUPABASE_URL=https://your-prod-project.supabase.co  
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-prod-anon-key
```

## 4. Workflow Git

### Structure des branches

```
main (production)
└── develop (développement)
    └── feature/xxx (features)
```

### Processus de déploiement

1. Développement sur `develop`
2. Tests sur `your-app-dev.vercel.app`
3. Merge vers `main` quand stable
4. Déploiement automatique sur `your-app-prod.vercel.app`

## 5. Points de vérification

### Checklist avant déploiement

- [ ] Variables d'environnement configurées sur Vercel
- [ ] URLs de redirection ajoutées dans Supabase
- [ ] Domaines autorisés dans Google Cloud Console
- [ ] Test de connexion en dev
- [ ] Test de connexion en prod

### Débogage courant

**Erreur "redirect_uri_mismatch"** :
- Vérifiez les URLs dans Google Cloud Console
- Vérifiez les URLs dans Supabase Auth settings

**Pas de redirection après login** :
- Vérifiez les variables d'environnement Vercel
- Vérifiez les logs dans Vercel Function Logs

**Connexion fonctionne en local mais pas en prod** :
- Vérifiez que HTTPS est activé
- Vérifiez le domaine de production dans les configurations OAuth