# Hoomi

Plateforme de mise en relation pour la colocation.

## Stack

- **Next.js 14** — App Router
- **TypeScript** — strict mode
- **Tailwind CSS** + **Shadcn/ui**
- **Supabase** — Auth, Postgres, Storage
- **Prisma** — ORM
- **Mapbox** — Carte interactive
- **Zustand** + **TanStack Query**

## Démarrage

```bash
npm install
cp .env.example .env.local
# Renseigner les variables d'environnement
npm run dev
```

## Déploiement

Déploiement continu sur **Vercel** depuis la branche `main`.
