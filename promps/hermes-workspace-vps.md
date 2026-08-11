Recreate my Hermes deployment from scratch on my Hostinger VPS.

VPS:
- VPS ID: 1727262
- VPS IP: 2.25.166.24
- Hostname: srv1727262.hstgr.cloud

Target domain:
- hermes.apmnslabs.cloud
- DNS must use an A record:
  hermes.apmnslabs.cloud → 2.25.166.24
- Use HTTPS through Traefik and Let's Encrypt.
- Do not use the temporary Hostinger hostname as the public URL.
- Do not expose Hermes directly on a public application port after Traefik is configured.

Architecture:
1. Keep Traefik as the only public reverse proxy.
2. Use one canonical Docker Compose project named:
   hermes-workspace
3. Run Hermes Agent and Hermes Workspace as separate services in the same Compose project.
4. Configure the services so Workspace can communicate with Agent internally.
5. Connect the application to the existing external Traefik network:
   traefik-proxy
6. Do not create a second Traefik instance.
7. Do not create duplicate Hermes projects.
8. Do not publish unnecessary ports directly to the Internet.

Traefik requirements:
- Enable Traefik for the public Hermes service.
- Every required router label must use exactly:
  Host(`hermes.apmnslabs.cloud`)
- Use the websecure entrypoint.
- Enable the Let's Encrypt certificate resolver.
- Route traffic to the actual HTTP port used by the Hermes Workspace Web UI.
- Verify the internal target port from the running container or application logs instead of guessing it.
- Use labels equivalent to:
  traefik.enable=true
  traefik.http.routers.hermes.rule=Host(`hermes.apmnslabs.cloud`)
  traefik.http.routers.hermes.entrypoints=websecure
  traefik.http.routers.hermes.tls.certresolver=letsencrypt
  traefik.http.services.hermes.loadbalancer.server.port=<actual_workspace_http_port>

Environment configuration:
- Put provider credentials in the Hermes Agent service or project-wide environment, not only in the Workspace service.
- Configure OpenAI correctly.
- Configure Kimi through Hermes’ supported OpenAI-compatible provider setup.
- Do not invent provider variable names. Use the Hermes setup/authentication flow if the installed version requires it.
- Never print, expose, or paste API keys in logs or chat.
- Set COOKIE_SECURE=1 when accessed through HTTPS.
- Configure the internal Agent/API URL used by Workspace.
- Ensure Workspace and Agent use compatible versions and configuration.

Provider verification:
- Verify that OpenAI authentication succeeds.
- Verify that Kimi authentication succeeds.
- Confirm that requests are not being sent to OpenRouter unless explicitly configured.
- Check logs for:
  HTTP 401
  Missing Authentication header
  provider not configured
  no LLM provider configured
  connection refused
- Test one simple request with OpenAI and one with Kimi.
- Do not claim success until both providers return a valid response.

Deployment procedure:
1. Inspect the VPS and list existing Hermes, Workspace, and Traefik projects.
2. Identify which project is currently active.
3. Do not delete or overwrite any existing project, volume, database, API key, or configuration without showing me the exact resource and asking for confirmation.
4. If old projects are duplicated, recommend which single project should remain.
5. Create or update the canonical project only after the configuration is validated.
6. Deploy the containers.
7. Confirm both containers are running and healthy.
8. Confirm Traefik sees the router and that the router rule is exactly:
   hermes.apmnslabs.cloud
9. Confirm DNS resolution and HTTPS access.
10. Confirm the Hermes Web UI loads through:
    https://hermes.apmnslabs.cloud
11. Confirm Agent-to-Workspace communication.
12. Confirm OpenAI and Kimi requests independently.
13. Report any remaining issue with the exact container, service, or configuration section responsible.

Important:
- Use the existing Traefik network and avoid port conflicts.
- Do not route the domain to the old temporary hostname.
- Do not put API keys only in Workspace.
- Do not use direct HTTP access for normal login.
- Do not delete old deployments until I explicitly approve the exact deletion target.
- Before making any destructive change, provide a backup/export recommendation and list what would be lost.
