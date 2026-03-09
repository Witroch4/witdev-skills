---
name: chatwit-upstream-sync
description: "Safely merge upstream Chatwoot updates into the Chatwit fork while preserving all customizations (SocialWise, JusMonitorIA, rich messages, branding). Use this skill when the user asks to update from upstream Chatwoot, sync the fork, pull latest Chatwoot changes, check for upstream updates, or merge Chatwoot into Chatwit. Also trigger when the user mentions 'upstream', 'chatwoot updates', 'sync fork', 'atualizar do chatwoot', or 'merge upstream'."
category: devops
risk: unknown
source: witdev
date_added: "2026-03-09"
---

# Chatwit Upstream Sync

Safely merge upstream Chatwoot repository updates into the Chatwit fork, preserving all custom integrations and branding.

## Context

Chatwit is a fork of Chatwoot with custom integrations (SocialWise, JusMonitorIA, rich messages, branding). When Chatwoot releases updates, we need to merge them without losing our customizations. This is a delicate operation because both codebases modify some of the same files.

## Protected Customization Areas

These are the areas where Chatwit has custom code that must NEVER be lost during a merge:

### SocialWise Integration
- `lib/integrations/socialwise/` — Core SocialWise services
- `lib/integrations/socialwise_flow/` — Flow engine (debounce, processors)
- `config/initializers/socialwise_bot.rb` — Agent Bot auto-provisioning
- `config/initializers/socialwise_cache.rb` — Cache preload
- `app/jobs/socialwise_*.rb` — Background jobs
- `app/controllers/api/v1/accounts/integrations/socialwise_*.rb` — API controllers

### JusMonitorIA Integration
- `lib/integrations/jusmonitoria/` — Processor, response, webhook forwarder
- `config/initializers/jusmonitoria_bot.rb` — Bot auto-provisioning

### Rich Messages & UI
- `app/javascript/dashboard/components-next/message/bubbles/WhatsAppInteractive.vue`
- `app/javascript/dashboard/components-next/message/bubbles/RichCards.vue`
- `app/javascript/dashboard/components-next/message/Message.vue` — Content type routing
- `app/services/messages/whatsapp_renderer_mapper.rb`
- `app/services/messages/instagram_renderer_mapper.rb`

### WhatsApp Enhancements
- `app/services/whatsapp/incoming_message_base_service.rb` — QUICK_REPLY extraction via `extract_interactive_data`
- `app/services/whatsapp/providers/whatsapp_cloud_service.rb` — Template dispatch
- `app/services/whatsapp/populate_template_parameters_service.rb` — quick_reply parameters
- `app/jobs/whatsapp_typing_job.rb` — Typing indicator
- `app/listeners/whatsapp_typing_listener.rb` — Typing events

### Instagram/Facebook Enhancements
- `app/builders/messages/instagram/base_message_builder.rb` — Postback/quick_reply payload
- `app/builders/messages/facebook/message_builder.rb` — Postback/quick_reply + message_content
- `app/services/instagram/rich_message_service.rb`
- `app/services/facebook/rich_message_service.rb`

### Webhook Customizations
- `app/listeners/webhook_listener.rb` — `include_access_token` support
- `app/javascript/dashboard/routes/dashboard/settings/integrations/Webhooks/WebhookForm.vue`
- `app/views/api/v1/accounts/webhooks/_webhook.json.jbuilder`

### Redis Keys
- `lib/redis/redis_keys.rb` — SocialWise debounce keys

### Branding
- `public/brand-assets/logo.svg` — Always keep Chatwit version
- `public/brand-assets/logo_dark.svg` — Always keep Chatwit version
- `public/brand-assets/logo_w.svg`, `logo_dark_w.svg`, `logo_thumbnail_w.svg` — Chatwit variants

### Config & Docs
- `config/integration/apps.yml` — SocialWise + JusMonitorIA integrations
- `config/locales/en.yml` — Custom i18n keys (additive)
- `chatwitdocs/` — All documentation

## Workflow

Execute these steps in order. Communicate progress clearly at each stage.

### Phase 1: Pre-flight Checks

```bash
# 1. Verify we're in the right repo
git remote -v  # Must show origin (Witroch4/chatwit) and upstream (chatwoot/chatwoot)

# 2. Check for uncommitted changes
git status

# 3. If there are uncommitted changes, ask the user what to do:
#    - Commit them first (recommended)
#    - Stash them
#    - Abort until they're handled
```

If `upstream` remote doesn't exist, add it:
```bash
git remote add upstream https://github.com/chatwoot/chatwoot.git
```

### Phase 2: Fetch & Analyze

```bash
# 1. Fetch latest upstream
git fetch upstream

# 2. Count divergence
git log --oneline HEAD..upstream/develop | wc -l   # Upstream commits ahead
git log --oneline upstream/develop..HEAD | wc -l   # Our commits ahead

# 3. Show upstream highlights (latest 20 commits)
git log --oneline upstream/develop -20

# 4. Check latest version tags
git tag --sort=-version:refname | grep -E "^v?4\." | head -5
```

### Phase 3: Conflict Analysis (Critical)

Before touching anything, analyze which of our customized files overlap with upstream changes:

```bash
BASE=$(git merge-base HEAD upstream/develop)

# Files modified by us (custom code)
git diff --name-only $BASE HEAD | sort > /tmp/our_files.txt

# Files modified by upstream
git diff --name-only $BASE upstream/develop | sort > /tmp/upstream_files.txt

# Intersection = potential conflicts
comm -12 /tmp/our_files.txt /tmp/upstream_files.txt
```

For each overlapping file, analyze the nature of changes:

```bash
# For each conflicting file, show what each side changed
for f in $(comm -12 /tmp/our_files.txt /tmp/upstream_files.txt); do
  echo "=== $f ==="
  echo "OURS: $(git diff $BASE HEAD -- "$f" | grep "^+" | grep -v "^+++" | wc -l) lines added"
  echo "UPSTREAM: $(git diff $BASE upstream/develop -- "$f" | grep "^+" | grep -v "^+++" | wc -l) lines added"
done
```

Present a clear risk table to the user:

| File | Our Changes | Upstream Changes | Risk |
|------|------------|-----------------|------|
| file.rb | What we added | What they changed | Low/Medium/High |

### Phase 4: Backup

```bash
# Create backup branch BEFORE merging
git branch backup/pre-upstream-sync-$(date +%Y%m%d) HEAD
```

Tell the user: "Backup created at `backup/pre-upstream-sync-YYYYMMDD`. You can always recover with `git checkout backup/pre-upstream-sync-YYYYMMDD`."

### Phase 5: Merge

```bash
git merge upstream/develop
```

### Phase 6: Conflict Resolution

Resolve each conflict following these rules:

#### Auto-resolve (keep upstream):
- `AGENTS.md` — Upstream doc, we don't customize
- `public/404.html`, `public/422.html`, `public/500.html` — Error pages

#### Auto-resolve (keep ours / Chatwit):
- `public/brand-assets/logo.svg` — Always Chatwit branding
- `public/brand-assets/logo_dark.svg` — Always Chatwit branding

#### Smart merge (keep BOTH sides):

For files where we added code AND upstream added different code, combine both:

- **`db/schema.rb`** — Keep upstream version number (higher), keep all columns from both sides
- **`lib/redis/redis_keys.rb`** — Keep our debounce keys AND upstream's new keys
- **`config/locales/en.yml`** — Additive, keep all keys from both sides
- **`app/views/api/v1/accounts/webhooks/_webhook.json.jbuilder`** — Keep both fields
- **`WebhookForm.vue`** — Keep both data properties

#### Careful merge (understand both sides):

These files need manual analysis because both sides modify the same logic:

- **`incoming_message_base_service.rb`** — Upstream may refactor core methods. Our `extract_interactive_data` method is self-contained and should be preserved. The `create_message` method must include `content_attrs.merge(extract_interactive_data(message))` to combine upstream's content attributes with our interactive data extraction.

- **`webhook_listener.rb`** — Our `include_access_token` logic wraps the payload. Upstream may change `WebhookJob.perform_later` signature. Combine: keep our access token injection AND upstream's new parameters.

- **`instagram/base_message_builder.rb`** and **`facebook/message_builder.rb`** — Our postback/quick_reply payload extraction is additive. Upstream may add echo support. Keep both.

- **`Message.vue`** — Our additions (WhatsAppInteractive and RichCards content type routing) are in separate `if` blocks. Upstream changes are usually in different sections (sender logic, bot detection). Both should coexist.

#### Resolution pattern for Python/Ruby conflicts:

```python
# Use Python to safely replace conflict markers
with open(filepath, 'r') as f:
    content = f.read()

# Replace the conflict block with the combined version
content = content.replace(conflict_block, resolved_block)

with open(filepath, 'w') as f:
    f.write(content)
```

After resolving each file:
```bash
git add <resolved-file>
# Verify no conflict markers remain
grep -n "<<<<<<\|=======\|>>>>>>>" <resolved-file>
```

### Phase 7: Verify & Commit

```bash
# 1. Check no remaining conflicts
git diff --name-only --diff-filter=U

# 2. If clean, commit
git commit -m "merge(upstream): sync with chatwoot upstream/develop (vX.Y.Z)

Upstream updates include:
- [list key upstream features/fixes]

Chatwit customizations preserved:
- SocialWise Flow integration
- JusMonitorIA integration
- Rich messages (WhatsApp/Instagram/Facebook)
- QUICK_REPLY button payload extraction
- Webhook access token inclusion
- Chatwit branding
- Redis debounce keys"

# 3. Verify
git log --oneline -3
```

### Phase 8: Summary Report

Present a clear summary:

```
## Upstream Sync Complete

**Version:** vX.Y.Z (N commits merged)
**Conflicts resolved:** X files
**Backup:** backup/pre-upstream-sync-YYYYMMDD

### Conflict Resolution Summary
| File | Resolution |
|------|-----------|
| ... | ... |

### Key Upstream Features Added
- ...

### Chatwit Customizations Preserved
- [checkbox] SocialWise Flow
- [checkbox] JusMonitorIA
- [checkbox] Rich Messages
- [checkbox] WhatsApp enhancements
- [checkbox] Branding
```

## Important Safety Rules

1. **ALWAYS create a backup branch before merging** — This is non-negotiable
2. **NEVER force-resolve all conflicts to one side** — Each conflict needs individual analysis
3. **NEVER lose our `extract_interactive_data` method** — It's the core of QUICK_REPLY support
4. **NEVER overwrite Chatwit logos with Chatwoot logos** — Brand assets are always ours
5. **If unsure about a conflict, ask the user** — It's better to pause than to lose code
6. **After merging, verify no conflict markers remain** — `grep -rn "<<<<<<" . --include="*.rb" --include="*.vue" --include="*.yml"`
7. **Do NOT push automatically** — Let the user decide when to push
