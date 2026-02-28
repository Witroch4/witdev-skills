# agent-skills

Coleção de skills para agentes de IA (Claude Code, Cursor, Copilot, etc).

## Instalação

```bash
# Instalar todas as skills deste repo
npx skills add <SEU_USUARIO>/agent-skills

# Instalar skill específica
npx skills add <SEU_USUARIO>/agent-skills@master-front-skill

# Instalar globalmente (disponível em todos os projetos)
npx skills add <SEU_USUARIO>/agent-skills@master-front-skill -g
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
npx skills add <SEU_USUARIO>/agent-skills@master-front-skill -g
```

## Estrutura

```
agent-skills/
└── master-front-skill/
    └── SKILL.md
```

## Contribuir

Skills seguem o formato do [skills.sh](https://skills.sh). Cada skill é uma pasta com `SKILL.md`.
