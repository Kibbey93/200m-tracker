# RS3 200m Tracker

A single-page site that looks up a RuneScape 3 player on the official hiscores and,
given your XP/hour for each skill, tells you how long until each one hits 200m XP.

- Search by username (Normal, Ironman or Hardcore Ironman hiscores)
- Enter an XP/hour rate per skill; rates are saved in your browser
- Per-skill hours left, days left at your chosen hours/day, and totals
- Click column headers to sort (e.g. by hours left)
- Shareable links: `index.html?player=YourName`

## Hosting on GitHub Pages

1. Merge this into your default branch (e.g. `main`).
2. Repo **Settings → Pages → Build and deployment**: Source "Deploy from a branch",
   branch `main`, folder `/ (root)`.
3. The site appears at `https://<your-username>.github.io/200m-tracker/`.

## About the lookup

The RuneScape hiscores don't send CORS headers, so a browser page can't call them
directly. The page tries a few public CORS proxies in turn (corsproxy.io,
allorigins, codetabs). If they're all down, open the "Lookup not working?" panel to
paste your hiscores manually, or point it at your own proxy, for example this
Cloudflare Worker (free tier is plenty):

```js
export default {
  async fetch(req) {
    const target = new URL(req.url).searchParams.get("url");
    if (!target || !target.startsWith("https://secure.runescape.com/")) {
      return new Response("Bad request", { status: 400 });
    }
    const res = await fetch(target);
    return new Response(res.body, {
      status: res.status,
      headers: { "Content-Type": "text/plain", "Access-Control-Allow-Origin": "*" },
    });
  },
};
```

Then enter `https://<your-worker>.workers.dev/?url=` as the custom proxy.
