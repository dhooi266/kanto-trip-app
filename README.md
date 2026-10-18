# 慢游旧时光 · Old Japan, Slowly

Mobile travel app for an 8-day Tokyo · Kamakura · Izu trip (23–30 Jan 2027).
Chinese/English, ¥/RM currency switch, day-by-day timeline, in-app route maps,
food and lodging notes, budget and checklist.

## Run it locally
Double-click `index.html`, or from this folder:

```
npx serve .
```

then open the address it prints (on your phone too, if it's on the same Wi-Fi).

## Work on it with Claude Code
From this folder, run:

```
claude
```

Claude Code reads `CLAUDE.md` automatically, so it knows how the app is structured.
Example requests:
- "Add our booked ryokan name and price to day 4 and 5"
- "Replace the SVG map with a Leaflet map using OpenStreetMap tiles"
- "Add a photo for each stop from the photos/ folder"

## Put it online (optional)
It's one static file, so any static host works:
- **Netlify Drop**: drag this folder onto app.netlify.com/drop
- **GitHub Pages**: push to a repo, then Settings → Pages
- **Vercel**: `npx vercel` in this folder

On your phone, open the hosted link and use "Add to Home Screen" so it opens like an app.
