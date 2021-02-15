# Anlage Wellness

This website uses [Gatsby v2](https://www.gatsbyjs.org/blog/2018-09-17-gatsby-v2/) and [Netlify CMS](https://www.netlifycms.org). It follows the [JAMstack architecture](https://jamstack.org) by using Git as a single source of truth, and [Netlify](https://www.netlify.com) for continuous deployment, and CDN distribution.

## Features

- A simple landing page with blog functionality built with Netlify CMS
- Editable Pages: Landing, About, Product, Blog-Collection and Contact page with Netlify Form support
- Create Blog posts from Netlify CMS
- Tags: Separate page for posts under each tag
- Basic directory organization
- Uses Bulma for styling, but size is reduced by `purge-css-plugin`
- Blazing fast loading times thanks to pre-rendered HTML and automatic chunk loading of JS files
- Uses `gatsby-image` with Netlify-CMS preview support
- Separate components for everything
- Netlify deploy configuration
- Stripe Subscription Support
- Netlify functions for handling authentication and stripe subscriptions (see `lambda` folder)
- Perfect score on Lighthouse for SEO, Accessibility and Performance

## Prerequisites

- Node (I recommend using v8.2.0 or higher)
- [Gatsby CLI](https://www.gatsbyjs.org/docs/)
- [Netlify CLI](https://github.com/netlify/cli)

# Getting Started
## How Does this Work?

Gatsby auto-generates pages based on template files. Pages are rendered using markdown files and templates in the `pages/` directory with the `gatsby-transform-remark` [plugin described in this documentation](https://www.gatsbyjs.com/docs/how-to/routing/adding-markdown-pages/). Netlify Identity Admins can make content changes to the site by going to `https://anlage-wellness.com/admin` and logging in with their Netlify Identity information. The admin UI is created using the file `static/admin/config.yml` and more details can be found in [Understanding the CMS](#understanding-the-cms)

## Access Locally

```
$ yarn
$ netlify dev # or ntl dev
```

This uses the new [Netlify Dev](https://www.netlify.com/products/dev/?utm_source=blog&utm_medium=netlifycms&utm_campaign=devex) CLI feature to serve any functions you have in the `lambda` folder at `<your-local-domain>/.netlify/functions/<function-name>`

Rename `.env.template` to `.env` to define environment variables for these functions locally. Note that this file shouldn't be checked into the repo because it can contain secrets used by the function.

To test the CMS locally, you'll need to run a production build of the site:

```
$ npm run build
$ netlify dev # or ntl dev
```

## Authorization

The site uses Netlify Identity with their recommended custom wrapper [gotrue-js](https://github.com/netlify/gotrue-js) for signup, login, signout, and getting the current user. When a user upgrades their Stripe subscription, their Netlify Identity "role" is also upgraded using this library. The role is named to reflect the subscription tier they're in and can be used in the future to determine what data they have access to on the site (blog info, check-in forms, useful wellness tools, etc)

If certain routes in the app should be protected (ex. /app/dashboard) from unauthenticated users, see [Gatsby Private Routes](https://www.gatsbyjs.com/docs/how-to/routing/client-only-routes-and-user-authentication/)

## Media Libraries (installed, but optional)

Media Libraries have been included in this starter as a default. If you are not planning to use `Uploadcare` or `Cloudinary` in your project, you **can** remove them from module import and registration in `src/cms/cms.js`. Here is an example of the lines to comment or remove them your project.

```javascript
import CMS from 'netlify-cms-app'
// import uploadcare from 'netlify-cms-media-library-uploadcare'
// import cloudinary from 'netlify-cms-media-library-cloudinary'

import AboutPagePreview from './preview-templates/AboutPagePreview'
import BlogPostPreview from './preview-templates/BlogPostPreview'
import ProductPagePreview from './preview-templates/ProductPagePreview'
import IndexPagePreview from './preview-templates/IndexPagePreview'

// CMS.registerMediaLibrary(uploadcare);
// CMS.registerMediaLibrary(cloudinary);

CMS.registerPreviewTemplate('index', IndexPagePreview)
CMS.registerPreviewTemplate('about', AboutPagePreview)
CMS.registerPreviewTemplate('products', ProductPagePreview)
CMS.registerPreviewTemplate('blog', BlogPostPreview)
```

Note: Don't forget to also remove them from `package.json` and `yarn.lock` / `package-lock.json` using `yarn` or `npm`. During the build netlify-cms-app will bundle the media libraries as well, having them removed will save you build time.
Example:
```
yarn remove netlify-cms-media-library-uploadcare
```
OR
```
yarn remove netlify-cms-media-library-cloudinary
```
# Understanding the CMS

[Netlify CMS Config Documentation](https://www.netlifycms.org/docs/configuration-options/)

Follow the [Netlify CMS Quick Start Guide](https://www.netlifycms.org/docs/quick-start/#authentication) to set up authentication, and hosting.

# Troubleshooting

Windows users might encounter `node-gyp` errors when trying to npm install.
To resolve, make sure that you have both Python 2.7 and the Visual C++ build environment installed.

```
npm config set python python2.7
npm install --global --production windows-build-tools
```

[Full details here](https://www.npmjs.com/package/node-gyp 'NPM node-gyp page')

MacOS users might also encounter some errors, for more info check [node-gyp](https://github.com/nodejs/node-gyp). We recommend using the latest stable node version.

# Purgecss

This plugin uses [gatsby-plugin-purgecss](https://www.gatsbyjs.org/packages/gatsby-plugin-purgecss/) and [bulma](https://bulma.io/). The bulma builds are usually ~170K but reduced 90% by purgecss.
