# Pocket Marketing Pages

## What

This repository holds [Pocket](https://getpocket.com) marketing pages in multiple locales. The content is built using [Eleventy](https://www.11ty.dev/) static site generator.

## Why

Separation of concerns: Mozilla's Marketing team can update content of marketing pages without requiring Pocket Operations team's involvement.

## How

getpocket.com will transparently proxy requests to Netlify and return this content to the requesting user. E.g.

1.  User with `Accept-Language: es` requests https://getpocket.com/about
2.  getpocket.com web server requests content from https://pocket-static-production.netlify.com/redirect/about/
3.  Netlify returns content from https://pocket-static-production.netlify.com/es/about/
4.  getpocket.com web server returns received content to User.

The sites generated by this repo are not supposed to be accessed directly but they don't need to be private. In this spirit `robots.txt` has been configured to prevent search engine indexing.

## About the Locale Handling

Netlify's has support for language redirects based on Accept-Language headers. Unfortunately their support is very limited and fails (a) to parse Accept-language headers which declare multiple locales despite this is a very common scenario (e.g. "en-US,en") and (b) to separate per country idioms like fr-CA from fr-FR and zh-CN from zh-TW. According to Netlify's support forums as of Jan 2021 both those two issues are being worked on as part of a major engineering effort and there's no clear delivery timeline.

Due to the current Netlify's status we decided to implement our own locale redirector, based on Netlify functions. The redirect function parses Accept-Language headers and adds to a URL the corresponding locale string, if it doesn't already contain one. So for example `/about/` will converted to `/en/about` for a user with Accept-Language set to `en` but `/fr-fr/about/` will remain intact for the same user making that request.

To use the locale redirect function one should add `/redirect/` before the requested path. For example `/about/` becomes `/redirect/about/`.

## Local installation

To run the site locally via Eleventy's built in web server:

```
npm install
npm run serve
```

## Staging

https://pocket-static-staging.netlify.app/

Opening a PR against the master branch will generate a staging deploy preview.

Merging a PR to the master branch will trigger a staging deployment.

## Production

https://pocket-static-production.netlify.app/

You can trigger a production deployment by pushing to the production branch. For example, assuming your local master is the version you want in production: `git push origin master:production`

**Note:** the Pocket team will determine when new pages on Netlify production are proxied from the Pocket production site
