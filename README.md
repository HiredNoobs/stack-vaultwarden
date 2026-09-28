# stack-vaultwarden

Vaultwarden deployment for k8s.

A single instance StatefulSet runs on a node labelled `vaultwarden_core_vaultwarden`. It uses SQLite, so it can't run more than once, if more nodes are labelled it still runs once, on any of them. The data (database, attachments, sends and its keys) is on a `$VAULTWARDEN_STORAGE_CLASS` volume, so it survives restarts and follows the pod to another labelled node. Longhorn (`stack-longhorn`) has to be deployed first, `deploy` stops if it isn't running.

It's reached through nginx as `https://vaultwarden.$DOMAIN`, as a websocket service so clients sync changes straight away (`/notifications/hub`). The `vaultwarden` Service has no LoadBalancer IP.

`clean` keeps the data volume, `purge` removes it and every account and vault item with it.

## Accounts

Signups are off (`VAULTWARDEN_SIGNUPS_ALLOWED` in `stack.env`) and the admin panel is disabled (no `ADMIN_TOKEN`). To create accounts, e.g. on a fresh deployment:

1. Set `VAULTWARDEN_SIGNUPS_ALLOWED="true"` and deploy, `deploy` warns while it's on.
2. Create the accounts at `https://vaultwarden.$DOMAIN`.
3. Set it back to `"false"` and deploy again.
