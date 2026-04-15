# Coolify CLI Skill

Use this skill for ALL Coolify operations: deploying apps, managing env vars, checking status, restarting services, viewing logs.

**ALWAYS use the `coolify` CLI. Never call the Coolify REST API directly or use the browser UI unless the CLI doesn't support the operation.**

## Setup (already done on more-serv)

- CLI: `/usr/local/bin/coolify` (v1.5.0)
- Context: `more-serv` (default) → `https://coolify.moretti.cc`
- Token: `~/.clawdbot/credentials/coolify-api.token`
- Config: `~/.config/coolify/config.json`

Verify connection: `coolify context verify`

## Key App UUIDs (more-serv)

| App | UUID | Domain |
|-----|------|--------|
| fitralph | `gswkgwwwgog0wcws0csgcgsg` | fit.moretti.cc |
| mytimeshare-docs | `u84ggwg0og04s0wo848w80c0` | mytimeshare.moretti.cc |
| MyTimeshare MVP v2 | `lwcgkcsko0s4ss8gg4ck4s0g` | demo.mytimeshare.moretti.cc |
| anticon-talk | `yws0c8sg0gc8skgc4sc4scc8` | anticon.moretti.cc |
| giogiohouse | `r8okgc08oo8wk080c8cwkkoc` | giogiohouse.moretti.cc |
| urology-chat | `ksogww0swwoogoc0kckkw8gw` | urology.moretti.cc |
| xirumi-clone | `rkogg8kw04go4c8w4kg0g0gw` | weddingagent.moretti.cc |

Run `coolify app list` to refresh UUIDs.

## Common Commands

### List & Status
```bash
coolify app list                          # All apps + status
coolify app get <uuid>                    # App details
coolify resources list                    # All resources
coolify server list                       # Servers
```

### Deploy
```bash
coolify deploy name <app-name>            # Deploy by name (easiest)
coolify deploy uuid <uuid>                # Deploy by UUID
coolify deploy name <app-name> --force    # Force rebuild
coolify deploy list                       # Recent deployments
coolify deploy get <deployment-uuid>      # Deployment details
coolify app deployments logs <uuid>       # Deployment logs
coolify app deployments logs <uuid> -f    # Follow live logs
```

### App Lifecycle
```bash
coolify app start <uuid>
coolify app stop <uuid>
coolify app restart <uuid>
coolify app logs <uuid>                   # Runtime logs
coolify app logs <uuid> -f                # Follow runtime logs
```

### Environment Variables
```bash
coolify app env list <uuid>
coolify app env create <uuid> --key KEY --value VALUE
coolify app env update <uuid> KEY --value NEW_VALUE
coolify app env delete <uuid> KEY
coolify app env sync <uuid> --file .env   # Sync from .env file
```

### Services (Coolify-managed like Postgres, Redis)
```bash
coolify service list
coolify service restart <uuid>
coolify service env list <uuid>
```

### Databases
```bash
coolify database list
coolify database get <uuid>
coolify database restart <uuid>
```

## Output Formats

```bash
coolify app list --format=json            # JSON for scripting
coolify app list --format=table           # Default human-readable
coolify app list --format=pretty          # Pretty JSON for debugging
```

## Workflows

### Deploy an app after pushing code
```bash
coolify deploy name <app-name>
# Then watch logs:
coolify app deployments logs <uuid> -f
```

### Force rebuild (e.g. Coolify cached old image)
```bash
coolify deploy name <app-name> --force
```

### Update an env var and redeploy
```bash
coolify app env update <uuid> MY_VAR --value new-value
coolify deploy uuid <uuid>
```

### Check why an app is down
```bash
coolify app get <uuid>                        # Check status
coolify app logs <uuid>                       # Runtime logs
coolify app deployments list <uuid>           # Recent deployments
coolify app deployments logs <uuid>           # Last deployment logs
```

## Notes

- **Coolify caches by commit SHA** — if no code changed, use `--force` to trigger rebuild
- **Deployment may not auto-trigger on push** — manually trigger with `coolify deploy name <name>` if nothing starts
- **Services need `coolify` network** — if a service is down after restart, check Traefik. CLI can restart but can't add to Docker networks (do that manually if needed: `docker network connect coolify <container>`)
- **`running:unknown`** status is normal for healthy apps — Coolify reports unknown when no health check is configured
