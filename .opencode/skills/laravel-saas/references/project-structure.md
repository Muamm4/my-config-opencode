# Project Structure

## Directory Layout

```
~/saas/
├── app/                          # Laravel application
│   ├── Http/
│   ├── Models/
│   ├── Providers/
│   └── ...
├── resources/js/                  # React frontend
│   ├── Pages/                    # Inertia pages
│   │   ├── Dashboard.tsx
│   │   └── ...
│   ├── components/               # Shared components
│   │   ├── ui/                   # ShadCN components
│   │   └── ...
│   └── layouts/                  # Layout components
├── routes/                       # Laravel routes
│   ├── web.php
│   └── api.php
├── database/                     # Migrations & seeders
│   ├── migrations/
│   └── seeders/
├── composer.json                 # PHP dependencies
├── package.json                  # Node dependencies
├── vite.config.ts               # Vite configuration
└── .env                         # Environment variables
```

## Key Paths

| Path | Description |
|------|-------------|
| `~/saas/resources/js/components/ui/` | ShadCN UI components |
| `~/saas/resources/js/Pages/` | Inertia page components |
| `~/saas/routes/` | Laravel route definitions |
| `~/saas/database/migrations/` | Database migrations |