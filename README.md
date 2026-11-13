# QSL Card Workshop

A browser-based design tool for building and printing amateur radio QSL
cards. Everything runs client-side — pick a template, fill in your station
details, customize colors/fonts/layout, and export print-ready JPG/PNG/PDF
files. No account, no server-side processing, no data leaves your computer.

This repo runs the whole thing inside a small Docker container, so you don't
need to install anything except Docker itself. A ready-made container image
is built automatically by GitHub Actions every time this repo changes, so
you don't even need to build it yourself.

## Quickest way to try it

If you just want to run it and don't care about editing the code, skip
cloning the repo entirely. Install Docker Desktop (step 1 below), then run:

```
docker run -d -p 8080:80 --name qsl-card-workshop --restart unless-stopped ghcr.io/babdor/qsl_card:latest
```

and open <http://localhost:8080>. That's it. To stop it later:
`docker stop qsl-card-workshop && docker rm qsl-card-workshop`.

For anything beyond "just run it" (changing a default, adding a template),
use the full steps below instead.

## Run it (step by step)

These steps work on Windows, Mac, or Linux. If you get stuck on any step,
paste the exact error message into an AI assistant (like Claude or ChatGPT)
along with "I'm trying to run a Docker container" — that's enough context
for it to help you.

### 1. Install Docker Desktop

If you don't already have Docker:

- Go to <https://www.docker.com/products/docker-desktop/>
- Download and install Docker Desktop for your operating system
- Open Docker Desktop once after installing, so it finishes setting up and
  is running in the background (you'll see a whale icon in your system
  tray / menu bar)

### 2. Get this project onto your computer

If you already have this folder on your computer, skip to step 3.

Otherwise, download/clone this repository so you have a folder containing
`Dockerfile`, `docker-compose.yml`, `index.html`, and the `css/`, `js/`,
`fonts/` folders all together.

### 3. Open a terminal in this folder

- **Windows:** open the folder in File Explorer, click the address bar,
  type `cmd`, and press Enter.
- **Mac:** open the folder in Finder, right-click it, choose
  "New Terminal at Folder" (or open Terminal and type `cd ` followed by
  dragging the folder into the window, then press Enter).
- **Linux:** right-click inside the folder in your file manager and choose
  "Open Terminal Here", or `cd` into it manually.

### 4. Start the app

Run this command:

```
docker compose up -d
```

What this does: it fetches the pre-built web server image (or builds one
locally from the `Dockerfile` if the pre-built one isn't reachable), starts
it, and leaves it running in the background (`-d` = "detached"). The first
run takes a minute or two; after that it starts in a couple of seconds.

### 5. Open it in your browser

Go to:

```
http://localhost:8080
```

You should see the QSL Card Workshop. That's it — you're running it.

### 6. Stop it

When you're done, from the same folder run:

```
docker compose down
```

This stops and removes the container. Your design files live in your own
browser's local storage, not in the container, so this is safe to do any
time — nothing is lost (as long as you keep using the same browser/machine;
see "Saving your work" below).

## Everyday use

- Start again later: `docker compose up -d`
- Check it's running: `docker compose ps`
- See logs if something looks wrong: `docker compose logs -f`
- Use a different port (e.g. if 8080 is already taken on your machine):
  edit `docker-compose.yml` and change `"8080:80"` to e.g. `"9000:80"`,
  then re-run `docker compose up -d` and visit `http://localhost:9000`.

## Saving your work

The workshop autosaves your in-progress card to your browser's local
storage, so refreshing the page won't lose anything. But that storage is
tied to one browser on one machine. For anything you want to keep safely,
use the app's own **Save design** button to download a `.json` file you can
reopen later (including on a different computer), and export the finished
card as JPG/PNG/PDF when you're ready to print or email it.

## Project layout

```
index.html       The page itself — structure only, no styling or logic
css/styles.css    All visual styling (layout, colors, buttons, etc.)
js/app.js         The entire app: card rendering, editor UI, export, etc.
fonts/            19 self-hosted font files used by the built-in templates,
                  loaded by js/app.js via the FontFace API
Dockerfile        Builds a tiny nginx image that serves the files above
nginx.conf        Web server config (caching, gzip)
docker-compose.yml One-command way to run (or build) the container
.github/workflows/docker-publish.yml
                  Builds the image and publishes it to GitHub Container
                  Registry on every push to main and every version tag
```

Originally this was a single ~580KB HTML file with all the CSS, JavaScript,
and 19 fonts (as base64 text) inlined into one document. It's been split
into the files above — same app, same behavior, just organized so each
piece can be edited, cached, and loaded on its own.

## Running it without Docker

Because this is a static site, you don't strictly need Docker — any static
file server works. For example, from this folder:

```
python3 -m http.server 8080
```

then open `http://localhost:8080`. Docker is just the more reliable,
reproducible way to run it, especially if you want to hand this off to
someone else or deploy it somewhere.

## Making changes

Edit `index.html`, `css/styles.css`, or `js/app.js` directly, then rebuild:

```
docker compose up -d --build
```

The `--build` flag tells Docker to pick up your file changes instead of
reusing the previous image.
