# Procare Medical Equipment

This organization hosts the code our team works on together. Use the index below to find the right repo.

## Tools

Internal utilities, scripts, and developer tooling.

- [n8n Debug Toolkit](https://github.com/Procare-Medical-Equipment/n8n-Debug-Toolkit) — debug n8n workflows through a Claude interface + CLI.

## Apps

Customer-facing or internal applications.

- [Procare Staff Dashboard](https://github.com/Procare-Medical-Equipment/procare-staff-dashboard) — internal staff operations dashboard.
- [COMET Reseller Registration](https://github.com/Procare-Medical-Equipment/comet-reseller-registration) — reseller registration app.
- [Website 2026](https://github.com/Procare-Medical-Equipment/website-2026) — company website (in progress).

## Agents

Automation and AI agents.

- [ProCare AI Agent](https://github.com/Procare-Medical-Equipment/ProCare-AI-Agent-Repo) — components and workflows for building and versioning ProCare's AI agents.

---

## Contributing

How you push changes depends on the kind of repo:

- **Web apps** (e.g. the Staff Dashboard) — push your work to the app's **`main`** branch. `main` is the **staging** environment and deploys there automatically. Promoting to **production** is a **pull request into the `production` branch**, which requires review before it merges.
- **Everything else (non-web-app repos)** — **fork the repo** to your personal account and open a **pull request** back into the org repo.

Either way, production is reached only through a reviewed PR — never a direct push.

If you can't push, or **Procare-Medical-Equipment** doesn't appear as a fork target, you're not a member of the org yet — ask an admin to add you.
