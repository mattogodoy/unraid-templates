# Unraid templates for Nametag

[Community Applications](https://ca.unraid.net/) templates for
[Nametag](https://nametag.one), an open-source personal CRM. The app itself
lives at [mattogodoy/nametag](https://github.com/mattogodoy/nametag).

## Installing Nametag on Unraid

The template installs the Nametag container only. It needs a PostgreSQL
database alongside it.

1. **PostgreSQL.** Install a PostgreSQL container from Community Applications
   (the official `postgres` image, version 14 or newer). Create a database and
   user for Nametag, and note the server's IP, port, user and password.
2. **Photo folder.** The container runs as UID 1001. In the Unraid terminal, run:

   ```bash
   mkdir -p /mnt/user/appdata/nametag/photos
   chown -R 1001:1001 /mnt/user/appdata/nametag
   ```

3. **Install.** In **Apps**, search for **Nametag** and click **Install**.
   Generate two secrets with `openssl rand -base64 32` and paste them into
   **Auth secret** and **Cron secret**. Set **App URL** to the address you open
   Nametag at, fill in the database fields, and click **Apply**.
4. **Background jobs.** Reminders, CardDAV sync, cleanup and geocoding run when
   something calls Nametag's cron endpoints. With the **User Scripts** plugin,
   create one script per job:

   ```bash
   #!/bin/bash
   NAMETAG_URL="http://192.168.1.10:3000"
   CRON_SECRET="your-cron-secret"
   curl -fsS -H "Authorization: Bearer $CRON_SECRET" "$NAMETAG_URL/api/cron/send-reminders"
   ```

   | Job | Endpoint | Suggested schedule |
   |---|---|---|
   | Reminders | `send-reminders` | `0 8 * * *` |
   | Cleanup | `purge-deleted` | `0 3 * * *` |
   | CardDAV sync | `carddav-sync` | `0 2,10,18 * * *` |
   | Geocoding | `geocode` | `*/5 * * * *` |

Redis and SMTP settings are under **Show more settings**. Leave them empty if
you don't use them. Email needs `EMAIL_DOMAIN`, `SMTP_HOST`, `SMTP_PORT`,
`SMTP_USER` and `SMTP_PASS`; see the
[email docs](https://docs.nametag.one/self-hosting/email/). For anything else,
click **Add another Path, Port, Variable, Label or Device** and use a name from
the [configuration docs](https://docs.nametag.one/self-hosting/configuration/).

Full self-hosting docs: https://docs.nametag.one/self-hosting/

## Support

Open an issue at https://github.com/mattogodoy/nametag/issues.
