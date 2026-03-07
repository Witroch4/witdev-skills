# witdev-skills

Coleção de skills para agentes de IA (Claude Code, Cursor, Copilot, etc).

## Instalação

```bash
# Instalar todas as skills deste repo
npx skills add Witroch4/witdev-skills

# Instalar skill específica
npx skills add Witroch4/witdev-skills@master-front-skill -g
npx skills add Witroch4/witdev-skills@witdev-project-setup -g
```

## Skills Disponíveis

### `master-front-skill`

Master frontend skill consolidando as melhores práticas de:

- **Tailwind v4** — arquitetura 4-passos, `@theme inline`, dark mode canônico, armadilhas
- **shadcn/ui** — `cn()`, CVA, composição, extensão correta de componentes
- **Design System** — filosofia anti "AI slop", tipografia, cor, motion, composição espacial
- **Performance React/Next.js** — eliminação de waterfalls, bundle, re-renders, SWR
- **Acessibilidade** — WCAG, keyboard navigation, ARIA, padrões UX
- **Animações** — GPU-friendly, Framer Motion, Remotion

```bash
npx skills add Witroch4/witdev-skills@master-front-skill -g
```

### `witdev-project-setup`

Setup completo de novo projeto com a stack WitDev padrão:

- **PostgreSQL 17 + pgvector** — `pgvector/pgvector:pg17`, extensão inclusa
- **Redis 8** — `redis:8-alpine`, major pin + patches
- **Node.js 24 LTS** — `node:24-alpine`
- **Next.js 16** — Turbopack estável, React 19.2
- **Prisma + Driver Adapter** — TCP keepalive real (sem heartbeat)
- **Docker** — docker-compose-dev, Dockerfile.prod multi-stage, Swarm deploy
- **Scripts** — `dev.sh`, `build.sh`, `scripts/db-prepare.js`
- **Conexões Docker** — solução definitiva para "conexão fantasma" TCP/NAT

```bash
npx skills add Witroch4/witdev-skills@witdev-project-setup -g
```

## Estrutura

```
witdev-skills/
├── master-front-skill/
│   └── SKILL.md
└── witdev-project-setup/
    └── SKILL.md
```

## Contribuir

Skills seguem o formato do [skills.sh](https://skills.sh). Cada skill é uma pasta com `SKILL.md`.
