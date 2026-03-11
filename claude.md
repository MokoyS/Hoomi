# 🏠 HOOMI — Brief Claude Code
> Projet : Plateforme de mise en relation pour la colocation  
> Agence : MELIOZ — Maxime Lebas  
> Stack validée : mars 2026  
> Statut : Prêt au démarrage dès signature client

---

## 🎯 Contexte du projet

Hoomi est une plateforme web permettant à des particuliers de publier et consulter des annonces de colocation. Les profils sont vérifiés avant de pouvoir publier. Le projet est développé en MVP pour valider le concept avant toute montée en charge.

**Client :** Romane Palluel  
**Contact :** Romane.palluel24@em-normandie.fr — 06 62 44 80 23  
**Charte :** Palette solaire et douce · Poppins (titres) · Montserrat (texte)  
**Option choisie :** [ A — sans messagerie | B — avec messagerie ] ← à compléter après RDV

---

## 🛠️ Stack technique — NON NÉGOCIABLE

Ne jamais dévier de cette stack sans validation explicite.

### Framework & Frontend
- **Next.js 14** — App Router uniquement (pas Pages Router)
- **TypeScript** — strict mode activé, pas de `any`
- **Tailwind CSS v3** — utility-first, pas de CSS custom sauf exception justifiée
- **Shadcn/ui** — composants copiés dans le projet via `npx shadcn@latest add`
- **Lucide React** — icônes uniquement

### État & Données
- **Zustand** — état global uniquement (user connecté, filtres, messagerie)
- **TanStack Query** — toutes les requêtes côté client (fetch, cache, loading, error)
- **Prisma** — ORM, schéma BDD, migrations
- **Supabase** — Auth + Postgres + Storage

### Formulaires & Validation
- **React Hook Form** — tous les formulaires sans exception
- **Zod** — validation côté client ET serveur via le même schéma
- Les schémas Zod sont dans `src/lib/validations/` et partagés entre front et Server Actions

### Carte
- **Mapbox GL JS** + **react-map-gl**
- Clé API dans `.env.local` uniquement — jamais en dur dans le code

### Messagerie (Option B uniquement)
- **Supabase Realtime** — pattern `supabase.channel()`
- Table `messages` avec RLS activé

### Hébergement
- **Vercel** — déploiement continu depuis la branche `main`
- Preview automatique sur chaque PR

---

## 📁 Structure de dossiers — RESPECTER STRICTEMENT

```
hoomi/
├── src/
│   ├── app/                          # App Router Next.js 14
│   │   ├── (auth)/                   # Groupe : pages non connectées
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   └── register/
│   │   │       └── page.tsx
│   │   ├── (dashboard)/              # Groupe : pages connectées
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx          # Mes annonces + mon profil
│   │   │   └── messages/             # Option B uniquement
│   │   │       └── page.tsx
│   │   ├── annonces/
│   │   │   ├── page.tsx              # Liste avec filtres
│   │   │   ├── [id]/
│   │   │   │   └── page.tsx          # Fiche annonce /annonces/[id]
│   │   │   └── nouvelle/
│   │   │       └── page.tsx          # Créer une annonce
│   │   ├── admin/                    # Back-office (accès restreint)
│   │   │   └── page.tsx
│   │   ├── api/                      # Route handlers
│   │   │   └── webhooks/
│   │   ├── layout.tsx                # Layout racine
│   │   ├── page.tsx                  # Homepage
│   │   ├── loading.tsx
│   │   └── not-found.tsx
│   ├── components/
│   │   ├── ui/                       # Composants Shadcn (générés — ne pas modifier)
│   │   ├── annonces/                 # Composants métier annonces
│   │   │   ├── AnnonceCard.tsx
│   │   │   ├── AnnonceList.tsx
│   │   │   ├── AnnonceFilters.tsx
│   │   │   └── AnnonceForm.tsx
│   │   ├── map/
│   │   │   └── MapView.tsx
│   │   ├── auth/
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── messages/                 # Option B uniquement
│   │   │   └── ChatWindow.tsx
│   │   └── layout/
│   │       ├── Header.tsx
│   │       ├── Footer.tsx
│   │       └── Sidebar.tsx
│   ├── lib/
│   │   ├── supabase/
│   │   │   ├── client.ts             # Client Supabase côté browser
│   │   │   ├── server.ts             # Client Supabase côté server (Server Components)
│   │   │   └── middleware.ts         # Auth middleware
│   │   ├── validations/              # Schémas Zod partagés front/back
│   │   │   ├── annonce.schema.ts
│   │   │   ├── user.schema.ts
│   │   │   └── message.schema.ts     # Option B
│   │   ├── actions/                  # Server Actions Next.js
│   │   │   ├── annonces.actions.ts
│   │   │   ├── auth.actions.ts
│   │   │   └── messages.actions.ts   # Option B
│   │   └── utils.ts
│   ├── stores/                       # Stores Zustand
│   │   ├── useUserStore.ts
│   │   ├── useFiltersStore.ts
│   │   └── useMessagesStore.ts       # Option B
│   ├── hooks/                        # Custom hooks
│   │   ├── useAnnonces.ts            # TanStack Query wrapper
│   │   ├── useAuth.ts
│   │   └── useMessages.ts            # Option B
│   └── types/                        # Types TypeScript globaux
│       ├── annonce.types.ts
│       ├── user.types.ts
│       └── database.types.ts         # Généré par Supabase CLI
├── prisma/
│   ├── schema.prisma                 # Schéma BDD — source de vérité
│   └── migrations/                   # Ne jamais modifier manuellement
├── public/
│   ├── fonts/                        # Poppins + Montserrat
│   └── images/
├── .env.local                        # Variables d'env — jamais committé
├── .env.example                      # Template des variables — committé
├── .gitignore
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── CLAUDE.md                         # Ce fichier
```

---

## 🗄️ Schéma Prisma — Source de vérité BDD

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}

model User {
  id        String     @id @default(cuid())
  email     String     @unique
  nom       String
  telephone String?
  avatar    String?
  verifie   Boolean    @default(false)
  role      Role       @default(USER)
  annonces  Annonce[]
  messages  Message[]  // Option B
  createdAt DateTime   @default(now())
  updatedAt DateTime   @updatedAt
}

model Annonce {
  id           String   @id @default(cuid())
  titre        String
  description  String
  prix         Float
  surface      Float
  nbColocataires Int
  ville        String
  adresse      String?
  lat          Float?
  lng          Float?
  photos       String[]
  statut       Statut   @default(PENDING)
  auteur       User     @relation(fields: [auteurId], references: [id], onDelete: Cascade)
  auteurId     String
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}

model Message {  // Option B uniquement
  id         String   @id @default(cuid())
  contenu    String
  lu         Boolean  @default(false)
  expediteur User     @relation(fields: [expediteurId], references: [id])
  expediteurId String
  destinataireId String
  createdAt  DateTime @default(now())
}

enum Role {
  USER
  ADMIN
}

enum Statut {
  PENDING    // En attente de validation admin
  ACTIVE     // Publiée
  REJECTED   // Rejetée par admin
  ARCHIVED   // Archivée par l'auteur
}
```

---

## ✅ Schémas Zod — Template de référence

```typescript
// src/lib/validations/annonce.schema.ts
import { z } from 'zod'

export const annonceSchema = z.object({
  titre: z.string()
    .min(10, 'Le titre doit faire au moins 10 caractères')
    .max(100, 'Le titre ne peut pas dépasser 100 caractères'),
  description: z.string()
    .min(50, 'La description doit faire au moins 50 caractères'),
  prix: z.number()
    .min(100, 'Le prix minimum est de 100€')
    .max(5000, 'Le prix maximum est de 5 000€'),
  surface: z.number()
    .min(5, 'La surface minimum est de 5m²'),
  nbColocataires: z.number()
    .min(1).max(20),
  ville: z.string().min(2, 'Ville requise'),
  photos: z.array(z.string().url())
    .min(1, 'Au moins une photo requise')
    .max(10, 'Maximum 10 photos'),
})

export type AnnonceInput = z.infer<typeof annonceSchema>
```

---

## 🔐 Variables d'environnement — `.env.local`

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Prisma (Supabase connection pooling)
DATABASE_URL=
DIRECT_URL=

# Mapbox
NEXT_PUBLIC_MAPBOX_TOKEN=

# Resend (emails transactionnels)
RESEND_API_KEY=

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_APP_NAME=Hoomi
```

> ⚠️ Ne jamais committer `.env.local`. Committer uniquement `.env.example` avec les clés vides.

---

## 🎨 Charte graphique — Hoomi

```typescript
// tailwind.config.ts — couleurs Hoomi (charte officielle validée par Romane)
colors: {
  hoomi: {
    primary:   '#FF7A27', // Pumpkin Spice — RGB 255, 122, 39 — CTA, boutons, liens actifs
    secondary: '#FFD369', // Mustard       — RGB 255, 211, 105 — fonds, hover, accents doux
    white:     '#FFFFFF', // White         — RGB 255, 255, 255 — fond général, cartes
    dark:      '#1A1A1A', // Texte principal (non fourni — valeur MELIOZ par défaut)
    muted:     '#9E9E9E', // Texte secondaire (non fourni — valeur MELIOZ par défaut)
  }
}

// Utilisation recommandée
// primary   #FF7A27 → boutons CTA, liens, icônes actives, bordures focus
// secondary #FFD369 → backgrounds de section, badges, tags, hover states
// white     #FFFFFF → fond général, cartes annonces, modales
// dark      #1A1A1A → titres, texte corps, labels
// muted     #9E9E9E → placeholders, texte secondaire, métadonnées

// Typographie
// Titres  → Poppins   (weights: 400, 600, 700)
// Texte   → Montserrat (weights: 400, 500)
// Charger via next/font/google dans layout.tsx
```

---

## 🔒 Règles de sécurité — OBLIGATOIRES

### Row Level Security (RLS) Supabase
- RLS activé sur **toutes** les tables sans exception
- Un user ne peut modifier que ses propres annonces (`auteurId = auth.uid()`)
- Un user ne voit que ses propres messages (Option B)
- Seul le rôle ADMIN peut changer le statut d'une annonce

### Server Actions
- Toute mutation passe par une Server Action Next.js
- Chaque Server Action valide avec Zod avant d'écrire en BDD
- Vérifier l'authentification au début de chaque action (`await auth()`)

### Variables d'environnement
- `SUPABASE_SERVICE_ROLE_KEY` uniquement côté serveur — jamais exposé au client
- Toute variable exposée au client doit avoir le préfixe `NEXT_PUBLIC_`

---

## 🚀 Commandes de démarrage

```bash
# Installation
npx create-next-app@latest hoomi --typescript --tailwind --app --src-dir --import-alias "@/*"
cd hoomi

# Dépendances core
npm install @supabase/supabase-js @supabase/ssr
npm install @prisma/client prisma
npm install zustand @tanstack/react-query
npm install react-hook-form @hookform/resolvers zod
npm install react-map-gl mapbox-gl
npm install resend

# Shadcn/ui
npx shadcn@latest init
npx shadcn@latest add button input label card badge toast dialog

# Option B — messagerie uniquement
# (Supabase Realtime est inclus dans @supabase/supabase-js — pas d'install supplémentaire)

# Prisma
npx prisma init
# → Coller le schéma dans prisma/schema.prisma
npx prisma migrate dev --name "init"
npx prisma generate

# Dev
npm run dev
```

---

## 📋 Checklist de démarrage — Dans cet ordre

- [ ] Créer le projet Next.js avec les flags ci-dessus
- [ ] Configurer `.env.local` avec les clés Supabase + Mapbox + Resend
- [ ] Initialiser Prisma et lancer la migration `init`
- [ ] Configurer Tailwind avec les couleurs Hoomi
- [ ] Installer et configurer Shadcn/ui
- [ ] Créer les clients Supabase (`lib/supabase/client.ts` et `server.ts`)
- [ ] Mettre en place le middleware auth (`lib/supabase/middleware.ts`)
- [ ] Créer les schémas Zod dans `lib/validations/`
- [ ] Créer le store Zustand user (`stores/useUserStore.ts`)
- [ ] Configurer TanStack Query dans le layout racine
- [ ] Commencer par la page `/annonces` — c'est la page principale

---

## ⚙️ Comportement attendu de Claude Code

- **Toujours TypeScript strict** — pas de `any`, pas de `// @ts-ignore`
- **Toujours Zod** pour valider les données — côté client ET serveur
- **Toujours Server Actions** pour les mutations — pas de route `/api` sauf webhooks
- **Toujours RLS** sur les nouvelles tables Supabase
- **Composants Server par défaut** — `"use client"` uniquement si nécessaire (interactivité, hooks)
- **Nommage** — composants en PascalCase, fichiers en kebab-case, fonctions en camelCase
- **Pas de `console.log`** en production — utiliser des error boundaries
- Quand tu crées un formulaire, **toujours** utiliser React Hook Form + Zod + Shadcn Form
- Quand tu fetch des données côté client, **toujours** utiliser TanStack Query
- Quand tu as besoin d'état global, **toujours** utiliser Zustand

---

## 🔄 Workflow Git

```
main          → production (Vercel déploie automatiquement)
develop       → intégration
feature/xxx   → développement de chaque feature
```

```bash
# Démarrer une feature
git checkout -b feature/annonces-list

# Commit convention
git commit -m "feat: liste des annonces avec filtres ville/prix"
git commit -m "fix: correction filtre surface"
git commit -m "chore: ajout dépendance react-map-gl"
```

Quand tu commit, ne te cite nul part dans le projet et dans le repo github

---

## 📞 Contact MELIOZ

**Maxime Lebas** — Développeur & référent projet  
Email : Contact@agencemelioz.com  
Tél : +33 6 33 56 99 62  
RDV : [cal.eu/agence-melioz](https://cal.eu/agence-melioz)

---

*© MELIOZ 2026 — Document confidentiel projet Hoomi*