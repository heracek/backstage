---
id: switching-sqlite-postgres
title: Switching Backstage from SQLite to PostgreSQL
# prettier-ignore
description: How to get ready for deploying Backstage to production with PostgreSQL
---

The default `@backstage/create-app` database is SQLite, an in-memory database
that's perfect for initial experimentation as it requires no environment setup.

Once you're ready to deploy Backstage in production, or to have a more
persistent development setup, you can switch the Backstage database to
PostgreSQL.

Backstage uses the [Knex](http://knexjs.org/) library, making it fairly easy to
switch between database backends.

## Install PostgreSQL

First, swap out SQLite for PostgreSQL in your `backend` package:

```shell
cd packages/backend
yarn remove sqlite3
yarn add pg
```

## Add PostgreSQL configuration

Next, modify `app-config.yaml` in the root folder to add PostgreSQL
configuration for the backend:

```diff
backend:
  database:
-    client: sqlite3
-    connection: ':memory:'
+    # config options: https://node-postgres.com/api/client
+    client: pg
+    connection:
+      host:
+        $env: POSTGRES_HOST
+      port:
+        $env: POSTGRES_PORT
+      user:
+        $env: POSTGRES_USER
+      password:
+        $env: POSTGRES_PASSWORD
+      # https://node-postgres.com/features/ssl
+      #ssl: require # see https://www.postgresql.org/docs/current/libpq-ssl.html Table 33.1. SSL Mode Descriptions (e.g. require)
+        #ca: # if you have a CA file and want to verify it you can uncomment this section
+        #$file: <file-path>/ca/server.crt

```

If you have a `app-config.local.yaml` for local development, a similar update
should be made there. You can set the `POSTGRES_` environment variables prior to
launching Backstage, or remove the $env keys and simply set values directly for
development.

The Backstage App is now ready to start up with a PostgreSQL backing database.
