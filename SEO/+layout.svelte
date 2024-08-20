<script lang="ts">
// Layout used for public pages.
// - I moved the entire head from `app.html` into this file because comments and whitespace are
//   removed from this file during a build, but not app.html.

let { children } = $props();

import { afterNavigate, beforeNavigate } from "$app/navigation";
import { page } from "$app/stores";
import {
  PUBLIC_DEFAULT_DESCRIPTION,
  PUBLIC_DEFAULT_TITLE,
  PUBLIC_SHOW_BLOG,
  PUBLIC_SITE_NAME,
} from "$env/static/public";
import { getMeta } from "$lib/common/meta";

import Footer from "./footer.svelte";
import Header from "./header.svelte";

import faviconSVG from "$lib/assets/images/branding/favicon.svg?url";
import iconPNG192 from "$lib/assets/images/branding/icon-192.png?url";
import defaultOGImage from "$lib/assets/images/branding/og-default.png?url";

// Workaround for SvelteKit smooth scrolling bug. See:
// https://github.com/sveltejs/kit/issues/2733#issuecomment-1590212088
// https://github.com/sveltejs/kit/pull/8724/files
let scrollBehavior: string;
beforeNavigate(() => {
  scrollBehavior = getComputedStyle(document.documentElement).scrollBehavior;
  document.documentElement.style.scrollBehavior = "auto";
});

afterNavigate(() => {
  if (scrollBehavior) {
    document.documentElement.style.scrollBehavior = scrollBehavior;
  }
});

// Some logic is required after route meta is defined (e.g. "use `ogDescription` if specified,
// otherwise use `description`, otherwise use `defaultDescription`, and then put this into a
// formatTitle function"). Using a function is more testable than ternaries within the template.
const meta = $derived.by(() =>
  getMeta({
    siteName: PUBLIC_SITE_NAME,
    defaultTitle: PUBLIC_DEFAULT_TITLE,
    defaultDescription: PUBLIC_DEFAULT_DESCRIPTION,
    defaultOGImage,
    routeMeta: $page.data?.meta ?? {},
    url: $page.url,
    pageParam: $page.params?.page ?? "",
  }),
);
</script>

<svelte:head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width"  />
  <title>{meta.title}</title>
  <meta name="description" content={meta.description}  />
  <link rel="canonical" href={meta.canonicalUrl} />

  <!--
		Icons
		- https://evilmartians.com/chronicles/how-to-favicon-in-2021-six-files-that-fit-most-needs
		  (Usually kept up to date.)
		- `.ico` is fallback for RSS readers & browsers that don't support SVG:
		   https://caniuse.com/link-icon-svg
    - `manifest.webmanifest` includes links to 192x192 & 512x512 PNGs.
    - `apple-touch-icon` is 180x180, but we can use our 192x192 instead.
	-->

  <link rel="icon" href="/favicon.ico" sizes="32x32" />
  <link rel="icon" href={faviconSVG} type="image/svg+xml" />
  <link rel="apple-touch-icon" href={iconPNG192} />
  <link rel="manifest" href="/manifest.webmanifest" />

  <!--
	  Open Graph
	  - https://ahrefs.com/blog/open-graph-meta-tags/
    - https://developers.facebook.com/docs/sharing/webmasters#markup
    - 1200x630px; `og:height` & `og:width` tags are optional; excluding for minimalism.
    - For `og:type`, use `article` for blog posts & `website` for the rest (blog index, pages, etc).
	  - `og:url` is always the canonical URL.
    - `og:title` & `og:description` are usually same as page title & meta description, but if those
	    are full of SEO keywords, then the og versions could be different.
    -->

  <meta property="og:type" content={meta.ogType} />
  <meta property="og:title" content={meta.ogTitle} />
  <meta property="og:description" content={meta.ogDescription} />
  <meta property="og:image" content={meta.ogImage} />
  <meta property="og:url" content={meta.ogUrl} />

  <!--
    Twitter
    - Twitter uses OG's url, title, description, & image tags.
		  https://developer.twitter.com/en/docs/twitter-for-websites/cards/guides/getting-started
	-->
  <meta name="twitter:card" content="summary_large_image" />
</svelte:head>

<Header />
{@render children()}
<Footer />
