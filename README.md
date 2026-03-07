# witdev-skills

Coleção de skills para agentes de IA (Claude Code, Cursor, Copilot, etc).

## Instalação

```bash
# Instalar skill específica
npx skills add Witroch4/witdev-skills@<skill-name> -g

# Exemplos
npx skills add Witroch4/witdev-skills@witdev-project-setup -g
npx skills add Witroch4/witdev-skills@master-front-skill -g
```

## Skills Disponíveis

### WitDev

| Skill | Descrição |
|-------|-----------|
| `witdev-project-setup` | Setup completo: PostgreSQL 17/pgvector, Redis 8, Node.js 24, Next.js 16, Docker, Prisma, BullMQ. Inclui solução para "conexão fantasma" TCP/NAT em Docker. |
| `master-front-skill` | Frontend master: Tailwind v4, shadcn/ui, design system, performance React/Next.js, acessibilidade, animações. |

### Frontend & UI

| Skill | Descrição |
|-------|-----------|
| `frontend-design` | Interfaces production-grade com alto nível de design. |
| `tailwind-theme-builder` | Setup Tailwind v4 + shadcn/ui com `@theme inline`, dark mode, migração v3→v4. |
| `shadcn-ui` | Integração e uso avançado de shadcn/ui. |
| `web-design-guidelines` | Auditoria de UI/UX, acessibilidade WCAG, boas práticas de design web. |
| `design-md` | Analisa projetos e sintetiza design system em DESIGN.md. |

### React & Next.js

| Skill | Descrição |
|-------|-----------|
| `vercel-react-best-practices` | Guidelines de performance React/Next.js do Vercel Engineering. |
| `swr` | SWR para data fetching, caching, revalidação e optimistic updates. |

### IA & APIs

| Skill | Descrição |
|-------|-----------|
| `gemini-api-dev` | Aplicações com Gemini API: multimodal, function calling, structured outputs. |

### DevOps & Infra

| Skill | Descrição |
|-------|-----------|
| `docker-expert` | Docker multi-stage builds, otimização de imagens, segurança, Compose, deploy em produção. |

### Mobile

| Skill | Descrição |
|-------|-----------|
| `vercel-react-native-skills` | React Native e Expo: performance, animações, módulos nativos. |

### Outros

| Skill | Descrição |
|-------|-----------|
| `python-performance-optimization` | Profiling e otimização de código Python com cProfile e memory profilers. |
| `remotion-best-practices` | Criação de vídeos com Remotion (React). |
| `skill-creator` | Criar, modificar e avaliar skills de agentes. |
| `find-skills` | Descobrir e instalar skills do ecossistema open agent skills. |

## Estrutura

```
witdev-skills/
├── witdev-project-setup/
├── master-front-skill/
├── frontend-design/
├── tailwind-theme-builder/
├── shadcn-ui/
├── web-design-guidelines/
├── design-md/
├── vercel-react-best-practices/
├── swr/
├── gemini-api-dev/
├── docker-expert/
├── python-performance-optimization/
├── remotion-best-practices/
├── skill-creator/
└── find-skills/
```

## Contribuir

Skills seguem o formato do [skills.sh](https://skills.sh). Cada skill é uma pasta com `SKILL.md`.
