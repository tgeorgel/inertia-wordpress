<p align="center">
    <a href="https://evomark.co.uk" target="_blank" alt="Link to evoMark's website">
        <picture>
          <source media="(prefers-color-scheme: dark)" srcset="https://evomark.co.uk/wp-content/uploads/static/evomark-logo--dark.svg">
          <source media="(prefers-color-scheme: light)" srcset="https://evomark.co.uk/wp-content/uploads/static/evomark-logo--light.svg">
          <img alt="evoMark company logo" src="https://evomark.co.uk/wp-content/uploads/static/evomark-logo--light.svg" width="500">
        </picture>
    </a>
</p>

# Inertia Wordpress Adapter

> Note: This adapter is in active development and should be used with caution. There may be breaking changes between minor versions

<p align="center">
    <a href="https://github.com/evo-mark/inertia-wordpress/releases" style="display:block;height:75px">
        <img src="https://img.shields.io/badge/Get_the_Latest_Release-ff7326?style=flat-square" style="height:35px">
    </a>
</p>

---

<p align="center">
    <a href="https://vuejs.org/" target="_blank" title="Vue" style="text-decoration: none">
        <img src="https://upload.wikimedia.org/wikipedia/commons/9/95/Vue.js_Logo_2.svg" style="height:100px">
    </a>
    <a href="https://react.dev/"  target="_blank" title="React" style="text-decoration: none">
        <img src="https://upload.wikimedia.org/wikipedia/commons/a/a7/React-icon.svg" style="height:100px">
    </a>
    <a href="https://svelte.dev/"  target="_blank" title="Svelte" style="text-decoration: none">
        <img src="https://raw.githubusercontent.com/sveltejs/branding/refs/heads/master/svelte-logo.svg" style="height:100px">
    </a>
</p>

---

Inertia is a new approach to building classic server-driven web apps. We call it the modern monolith.

Inertia allows you to create fully client-side rendered, single-page apps, without the complexity that comes with modern SPAs. Simply install this plugin, bootstrap your theme using our built-in command and away you go.

This is an community Inertia adapter for applications powered by Wordpress.

For more information on InertiaJS, visit the [Official Website](https://inertiajs.com/).

## Sponsoring

We're providing this community adapter free-of-charge without any paywalled features. However, all development and maintenance costs time, energy and money. So please help fund this project if you can.

[GitHub Sponsors](https://github.com/sponsors/craigrileyuk)
[Buy Me a Coffee](https://buymeacoffee.com/craigrileyuk)

## Table of Contents

- [Installation](#installation)
- [Requirements](#requirements)
- [Getting Started](#getting-started)
- [Theme Structure](#theme-structure)
  - [app.php](#appphp)
  - [controllers](#controllers)
  - [ecosystem.config.cjs](#ecosystemconfigcjs)
  - [functions.php](#functionsphp)
  - [index.php](#indexphp)
  - [package.json](#packagejson)
  - [resources](#resources)
  - [rest-api](#rest-api)
  - [style.css](#stylecss)
  - [vite.config.js](#viteconfigjs)
- [Development](#development)
- [Production](#production)
- [Server-Side Rendering](#server-side-rendering)
- [WP-CLI Commands](#wp-cli-commands)
- [Inertia Helpers](#inertia-helpers)
  - [resolveInertiaPage](#resolveinertiapage)
  - [Global Shared Props](#global-shared-props)
- [Using Templates](#using-templates)
- [Wordpress Settings](#wordpress-settings)
- [Using Inertia in Wordpress](#using-inertia-in-wordpress)
- [Modules](#modules)
- [Custom Modules](#custom-modules)

## Installation

Download the <a href="https://github.com/evo-mark/inertia-wordpress/releases">inertia-wordpress.zip</a> file from the latest release to get started. Updates will then be handled automatically through Wordpress.

## Requirements

- NodeJS 20+
- Composer
- WP-CLI
- PM2 (for Server-Side Rendering)

## Getting Started

From the root of your Wordpress application, run the following command to bootstrap a new InertiaJS theme. Available templates are: **Vue**, **React** and **Svelte**.

```sh
wp inertia:create-theme
```

Using your terminal, navigate to the newly created theme directory and run:

```sh
composer update
```

Then, depending on your package manager of choice:

```sh
npm install
# OR
pnpm install
# OR
yarn install
```

Once your theme has been created, it will not be changed again by the Inertia Wordpress plugin, so feel free to make any change you wish.

## Theme Structure

```

├── app.php
├── composer.json
├── controllers
│ ├── archive.php
│ ├── page.php
│ └── single.php
├── ecosystem.config.cjs
├── functions.php
├── index.php
├── package.json
├── resources
│ ├── css
│ │ └── style.postcss
│ └── js
│ ├── main.js
│ ├── pages
│ │ └── Archive.vue
│ │ ├── Home.vue
│ │ ├── Post.vue
│ └── ssr.js
├── rest-api
│ └── TestPost.php
├── style.css
└── vite.config.js

```

If you ran the theme bootstrapper command above, you should have something like the above.

> ### app.php

See [InertiaJS.com > Server-Side Setup > Root Template](https://inertiajs.com/server-side-setup#root-template)

> ### controllers

These files will be the bridge between Wordpress and Inertia for serving GET requests. Each file should contain a single class that extends the `EvoMark\InertiaWordpress\InertiaController` class and provides a `handle` method. The handle method must return a call to `$this->render()`.

The files in this directory are loaded in accordance with [Wordpress' template hierarchy rules](https://developer.wordpress.org/themes/basics/template-hierarchy/), albeit moved from the root of your theme to this `controllers` directory.

> ### ecosystem.config.cjs

This is an optional file provided to make running your InertiaJS SSR process easier. It is read by the PM2 process manager and keeps your process running reliably.

For more information see the page on [PM2 Ecosystem Files](https://pm2.keymetrics.io/docs/usage/application-declaration/)

> ### functions.php

The standard entry point for your theme. The bootstrapper will populate this with a small amount of code.

The code `new RestApi` is a call to [WP Rest Registration](https://evomark.co.uk/open-source-software/wp-rest-registration/) which allows you to easily register REST API endpoints that can validate input.

We'll be using these endpoints to process information submitted by our InertiaJS frontend. See `rest-api` below for more details.

> ### index.php

Intentionally left empty to prevent directory indexing

> ### package.json

A file used by NodeJS package managers to install dependencies required by your theme. Common package managers are `NPM`, `PNPM`, and `Yarn`.

> ### resources

This is where the majority of your themes frontend will live. It's no different from a standard InertiaJS project setup, so we won't go into much detail on the file structure.

> ### rest-api

Since InertiaJS sends its data via AJAX requests, we can't use our `controllers` classes to process it. Instead, we must use special REST API controllers.

All classes in this directory are automatically registered by the call the `new RestApi` in your `functions.php` file. The structure of these files should be familiar to anyone experienced with Laravel Controllers.

See the [WP REST Registration documentation](https://evomark.co.uk/open-source-software/wp-rest-registration/) for more details.

> ### style.css

Required by Wordpress.

> ### vite.config.js

Used by Vite to bundle/build your theme's JavaScript and CSS files. You shouldn't need to change anything here to get Vite working, but feel free to add more plugins.

## Development

From your theme folder, you can run:

```sh
npm run dev
```

to start the Vite development server. Any changes you make to your theme's JavaScript/CSS files should be immediately reflected on the frontend of your site.

## Production

```sh
npm run build
```

This will create a production build of your theme's files as well as an SSR entry file. When the dev server is not running, the latest build will be automatically loaded by the Inertia Wordpress plugin.

## Server-Side Rendering

Once your happy with your theme, you can run the following commands from the root of your theme directory:

```bash
npm run build
npm run ssr
```

If you have PM2 set up correctly, this should start your SSR process.

## WP-CLI Commands

The Inertia Wordpress plugin provides:

```sh
wp inertia:create-theme
wp inertia:start-ssr
wp inertia:stop-ssr
```

## Inertia Helpers

### resolveInertiaPage

While not required, this helper aids in resolving your InertiaJS pages and performs the logic necessary to resolve templates (if you're using them).

```js
createInertiaApp({
  resolve: resolveInertiaPage(
    import.meta.glob("./pages/**/*.vue", { eager: false }),
  ),
  // ...
});
```

The `resolveInertiaPage` accepts two arguments, the first is your pages glob. With `eager: true` the pages will all be bundled into a single file, whereas with `eager: false` Vite will use code-splitting when bundling your app.

The second argument the `resolveInertiaPage` accepts is your [Default Layout](https://inertiajs.com/pages#default-layouts).

```js
import Layout from './Layout'
// ...
resolve: resolveInertiaPage(
    import.meta.glob("./pages/**/*.vue", { eager: false }),
    Layout
),
```

A third argument can be an object containing an optional callback that receives the page name and page object and should return a valid Layout.

```js
import Layout1 from './Layout';
import Layout2 from './Layout2';

// ...
resolve: resolveInertiaPage(
    import.meta.glob("./pages/**/*.vue", { eager: false }),
    null, // Notice that the 2nd argument is null
    { // Notice that this 3rd argument is an object
        /**
         * @param { string } name The page name that is being loaded
         * @param { VNode } resolvedPage The resolved page vNode
         * @param { ?VNode } resolvedTemplate If the post is assigned a template, the resolved vNode
         */
        layoutCallback:  (name, resolvedPage, resolvedTemplate) => {
            if (name.startsWith('account')) return Layout2;
            else return Layout;
        }
    }
),
```

### Global Shared Props

If you check your Inertia page props, you'll see a few provided objects pre-loaded:

- **$page.props.wp.name**: The name of your site
- **$page.props.wp.homeUrl**: The URL of your site's homepage
- **$page.props.wp.restUrl**: The base URL for making REST API requests
- **$page.props.wp.user**: The user object for the currently logged in user
- **$page.props.wp.userCapabilities**: An object of the current users capabilities/permission
- **$page.props.wp.logo**: An image resource containing your site logo as set in Wordpress' Appearance->Customise menu
- **$page.props.wp.menus**: A nested object containing your registered menus, keyed by location
- **$page.props.wp.adminBar**: An array of changed HTML elements that can be updated in your admin bar

## Using Templates

Inertia Wordpress allows you to use Wordpress' built-in [Templates](https://developer.wordpress.org/themes/templates/templates/) functionality to create sub-layouts for your pages. Here's how to set them up:

1. Create a new folder for your templates, such as `resources/js/templates`
2. If you use something different, make sure you change the setting in your [Wordpress settings menu](#wordpress-settings)
3. Create your templates, making sure you follow [your framework's instructions](https://inertiajs.com/pages#creating-layouts) on layouts
4. Wordpress will now read your templates folder and make them available as templates in the admin block editor
5. Pass your templates directory as an import glob to `resolveInertiaPage` in your `app.js` and `ssr.js` files

```js
resolve: resolveInertiaPage(
    import.meta.glob("./pages/**/*.vue"),
    DefaultLayout,
    { // Notice that this 3rd argument is an object
        templates: import.meta.glob("./templates/**/*.vue")
    }
),
```

And done!

The `resolveInertiaPage` helper function will also handle situations where you already have a default layout, automatically setting it as a sub-layout of that one.

## Wordpress Settings

You can find settings for this plugin in your Wordpress admin area in the `Settings > Inertia` submenu.

## Using Inertia in Wordpress

We've moved all of the functions you'll need to a single class. All functions are static, so the syntax should be the same as with Laravel.

Some common functions:

```php
use EvoMark\InertiaWordpress\Inertia;

// Share a value with the frontend
Inertia::share(string $key, mixed $value = null);

// Delete all previously shared props
Inertia::flushShared();

// Request the client to clear browser history for Inertia visits
Inertia::clearHistory();

// Request the client to encrypt history for this visit (https only)
Inertia::encryptHistory();

// Get the current asset version
Inertia::getVersion();

// Create a lazy prop for sharing (deprecated, prefer optional)
Inertia::lazy(callable $callback);

// Create an optional prop for sharing
Inertia::optional(callable $callback);

// Create a deferred prop for sharing
Inertia::defer(callable $callback, string $group = "default");

// Create a merged prop for sharing
Inertia::merge(mixed $value);

// Create an always prop for sharing
Inertia::always(mixed $value);

// Redirect to an external URL
Inertia::location(string $url);

// Flash some data to the next response, will be found on $page.props.flash[$key]
Inertia::flash(string $key, mixed $value);

// Return a back response
Inertia::back();

// Get the current Wordpress archive
Inertia::getArchive();

// Get the current (or provided) Wordpress post
Inertia::getPost(?\WP_Post $post = null, array $args = null);
```

## Modules

Modules provide functionality that allow common plugins to interact with your Inertia-powered theme. We'll be adding more as time goes on, but for now we have:

- Advanced Custom Fields
- Contact Form 7
- The SEO Framework

You must enable each module in the Wordpress settings submenu mentioned above for its functionality to be activated.

## Custom Modules

You can now add custom modules to the Inertia Wordpress adapter.

First create a class like so:

```php
namespace YourProject;

use EvoMark\InertiaWordpress\Inertia;
use EvoMark\InertiaWordpress\Modules\BaseModule;

class YourModule extends BaseModule
{
    // The title of the module to be displayed
    protected string $title = "Advanced Custom Fields";

    // Optional URI for a module logo
    protected string $logo;

    // The main class of the plugin that the module interacts with
    protected string $class = "ACF";

    // Internal reference, alpha-numeric and lowercase
    protected string $slug = "acf";

    // Any valid entry files for the plugin relative to the wp-content/plugins directory.
    protected array|string $entry = ['advanced-custom-fields-pro/acf.php', 'acf-pro/acf.php'];

    /**
     * Called immediately if the module is enabled and plugin installed/activated
     */
    public function register()
    {
        //
    }

    /**
     * Called before shared props are returned
     */
    public function boot(): void
    {
        Inertia::share('myModule', [
            // My data
        ]);
    }
}
```

You can then register your module by doing:

```php
use EvoMark\InertiaWordpress\Inertia;
use YourProject\YourModule;

add_action('inertia_wordpress_modules', function () {
    Inertia::addModule(YourModule::class);
});
```

Don't forget to enable your module in the `Inertia -> Settings` menu once it is registering.
