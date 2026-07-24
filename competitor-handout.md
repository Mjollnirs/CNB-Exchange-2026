# Competitor Handout

**CNB Exchange 2026 (International Exchange Event 2026 of the China National Training Base for Web Technologies)**

You can solve the tasks by developing them on your own machine.

## Gitea, git

The Gitea service is available at the following address: [https://git.foredu.cn](https://git.foredu.cn)

For **all six** tasks, you can start from a selected template repo on Gitea.

The available template repos:

- react
- react-ts
- vue
- vue-ts
- node
- node-ts
- laravel
- vanilla-js
- node-ts-prisma

To log in, you must use the username and password (a 4-digit PIN code) you have been given.
After logging in, create a new repo for the next task.

- Give the name of the new repo using the following pattern: `module-Y`, where `Y` is the module's letter (eg. `module-a`, `module-b`). **Make sure you set the repo's name carefully because if you make a mistake, the automatic deployment will not work!**
- Under the template field, select the appropriate template (e.g. `react-ts`). Select `Git Content (Default Branch)` for `Template Items`.

Once the new repo is created, clone it to your own workstation.

### Using npm modules

The npm modules will be accessible via a local npm cache. This means that even though there will be no internet access to the machines, you will be able to add the available npm modules to the projects as usual, and the `npm install` command issued on the cloned template projects will install all the npm modules needed for your project.

The available npm modules:

- express
- mysql2
- vue-router
- react-router, react-router-dom
- axios
- sass
- tailwindcss
- prisma, @prisma/client
- express-validator
- zod
- bootstrap
- _and all the types (`@types/`) needed for the TS projects_

## Laravel projects, composer install

The Laravel project contains all the necessary files, so you will not need to `composer install`. Your workstation has PHP 8 installed, so you can use the `php artisan` commands in the Laravel project.

## Deployment

When you commit and push your work, the deployment will start automatically. You can follow the process in the Gitea interface under the Action tab. Once the deployment is complete, your project will be available at https://wsXX-YYYY-module-Z.foredu.cn, where `X` is your workstation number, `YYYY` is your 4 digit pin, and `Z` is the module letter.

Competitors can also check the container logs of their deployed projects at [https://logs.foredu.cn/](https://logs.foredu.cn/). To log in, use your PIN.

## Database access

You will have your own database on the MySQL database server (`db.foredu.cn`) available on the local network. You will need to use this database for development, and the same database will provide the data for your projects deployed to the server. A database dump will be provided to get the initial data. During the marking, the database will be restored to its original state using the same dump. Your backend solution (Module A) uses this database.

You can use PHPMyAdmin to manage your databases at [https://pma.foredu.cn](https://pma.foredu.cn). Log in with the same credentials you use for Gitea. The database naming convention is `wsXX_module-y`, where `wsXX` is your username and `y` is the module letter (e.g. `ws07_module-b`).

## Prisma shadow database (`prisma migrate dev`)

`prisma migrate dev` needs a temporary **shadow database** that Prisma can create and drop while diffing migrations. On the competition server you **cannot** create or drop databases, so you must point Prisma at an **existing** database instead.

Use your **`module-b`** database as the shadow database (naming: `wsXX_module-b`). Pick a module database you are not using for live data in the task you are working on; `module-b` is a practical default because Module B does not use a database.

Add `shadowDatabaseUrl` to the `datasource` block in `schema.prisma` (replace `XX`, `YYYY`, and the module letter in `DATABASE_URL` with your own values):

```prisma
datasource db {
  provider          = "mysql"
  url               = env("DATABASE_URL")
  shadowDatabaseUrl = "mysql://wsXX:YYYY@db.foredu.cn:3306/wsXX_module-b"
}
```

Use the same username (`wsXX`) and PIN (`YYYY`) as for your normal database connection. Only the database name at the end should differ from `DATABASE_URL` when you reuse `module-b` as the shadow DB.

**Note:** `prisma migrate deploy` (used in production-style deploys) does not need a shadow database. This configuration is only required if you run `prisma migrate dev` locally during the competition.

## Fix Laravel mixed HTTP/HTTPS issue

In a Laravel production deployment, CSS and other assets may be linked with `http` by default. That conflicts with the site's `https` base URL and can cause mixed-content errors in the browser.

To enforce HTTPS for all generated URLs, add the following to `AppServiceProvider`:

```php
use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\URL;

public function boot(): void
{
    if (App::isProduction()) {
        URL::forceHttps();
    }
}
```
