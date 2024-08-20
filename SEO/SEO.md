# SEO Metadata

How to set up SEO tags & Open Graph metadata on a SvelteKit site.

## Setup

- Define `PUBLIC_SITE_NAME`, `PUBLIC_DEFAULT_TITLE`, &`PUBLIC_DEFAULT_DESCRIPTION` in `.env`.
- Add `$lib/assets/images/branding/og-default.png`. Create a 1200x630 PNG or JPEG.
- Add [+layout.svelte](+layout.svelte) within `src/routes/(public)/` or `src/routes/`, however you
  prefer to organize it.
- Add [meta.ts](+meta.ts) within `src/lib/`. (Optionally, update `formatTitle()` to your preferred
  title format; the default is `<title> · <siteName>`.)
- Add [app.html](app.html) within `src/`. (This version simplifies the default and moves items into
  `+layout.svelte` for better organization and because `app.html` is never minified by SvelteKit and
  will ship any comments you make in it.)
- Within each route's load function, include a `meta` property containing at least `title` and
  `description` (usually just those two) and any other properties you want to override the defaults
  for:

  ```ts
  import type { Meta } from "$lib/common/meta";

  export async function load() {
    try {
      const meta: Meta = {
        title: "Privacy Policy",
        description: `View our privacy policy`,
      };
      return { meta };
    } catch (err) {
      error(500, "Internal Server Error");
    }
  }
  ```

- Install [Super
  Sitemap](https://github.com/jasongitmail/super-sitemap?tab=readme-ov-file#installation), including
  set up of `robots.txt` as detailed in its instructions.

## Extras

- Claim ownership on [Google Search Console](https://search.google.com/search-console/welcome)
- Claim ownership on [Bing Webmaster Tools](https://www.bing.com/webmasters/about).
