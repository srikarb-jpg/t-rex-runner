# T-Rex Runner Lab

Play and hack the Chrome "no internet" dinosaur game inside one small
container. The whole game is plain HTML, CSS and JavaScript: change a number,
refresh the browser, and see what happens.

The game itself is from [wayou/t-rex-runner](https://github.com/wayou/t-rex-runner),
which extracted it from Chromium; this repo packages it as a ready-to-use dev
container. See [Credits](#credits).

## Setup

You need **Docker Desktop** and **VS Code** installed.

1. **Start Docker Desktop** and wait until it says it's running (whale icon
   in the taskbar/menu bar stops animating).

2. **Install the Dev Containers extension** in VS Code: open the Extensions
   view (`Ctrl+Shift+X`, or `Cmd+Shift+X` on Mac), search **Dev Containers**,
   and install the one by Microsoft.

3. **Get the code** into a normal folder, *not* inside OneDrive, iCloud or
   Dropbox:

   ```bash
   git clone https://github.com/srikarb-jpg/t-rex-runner.git
   ```

   No git? On the GitHub page click **Code → Download ZIP** and unzip it.

4. **Open the `t-rex-runner` folder in VS Code** (**File → Open Folder…**).

5. **Reopen in Container.** Click **Reopen in Container** in the pop-up at the
   bottom right. If you missed it, press `F1`, type
   `Dev Containers: Reopen in Container`, and press Enter.

   The first time takes a minute or two while the container downloads. When
   it's done, the bottom-left corner of VS Code says
   **Dev Container: T-Rex Runner**.

6. **Check it works.** Open a terminal in VS Code (**Terminal → New
   Terminal**). It opens *inside* the container. Run:

   ```bash
   python -m http.server 8000
   ```

   You should see `Serving HTTP on 0.0.0.0 port 8000 ...` and then nothing
   else. That's correct: the server is running and waiting for your browser.

7. **Play.** Open **http://localhost:8000** in your browser and press
   **Space**.

You're set up. Next time, just open the folder in VS Code and it reconnects
to the container automatically.

## Tutorial

Every command below runs in a terminal **inside the container**. If the
bottom-left corner of VS Code doesn't say **Dev Container**, you're on your
own machine; run `Dev Containers: Reopen in Container` first.

### 1. Start and stop the game

```bash
python -m http.server 8000     # start; leave this terminal running
```

Open **http://localhost:8000**. Each time the browser loads the page, the
terminal prints a line like `"GET / HTTP/1.1" 200`. That's the browser
fetching the game files, not an error.

To stop the server, click in the terminal and press `Ctrl+C`.

Use `localhost`, not the `0.0.0.0` address the terminal shows. If the page
doesn't load, open the **Ports** tab next to the Terminal tab and check that
port `8000` is listed; if not, click **Forward a Port** and type `8000`.

### 2. Find your way around

| File | What it's for |
| --- | --- |
| `index.html` | the page the browser loads |
| `index.js` | **all the game logic; most of your changes go here** |
| `index.css` | page styling |
| `assets/default_100_percent/100-offline-sprite.png` | every picture in the game (dino, cacti, birds, clouds) in one image |
| `assets/default_200_percent/200-offline-sprite.png` | the same image at double size, used on high-resolution screens |
| `.devcontainer/devcontainer.json` | tells VS Code which container to build |

### 3. Change the game speed

Open `index.js` and find `Runner.config = {` (press `Ctrl+F`). These lines
control the run:

```js
    Runner.config = {
        ACCELERATION: 0.001,   // how quickly it speeds up
        ...
        INVERT_DISTANCE: 700,  // score at which night mode starts
        ...
        MAX_SPEED: 13,         // top speed
        ...
        SPEED: 6,              // starting speed
```

Change `SPEED` to `12`, save (`Ctrl+S`), and refresh the browser. The server
doesn't need restarting; it hands the browser whatever is saved on disk.

If a change doesn't show up, do a hard refresh (`Ctrl+Shift+R`, or
`Cmd+Shift+R` on Mac) so the browser doesn't reuse an old copy.

### 4. Change how the dino jumps

Find `Trex.config = {` in `index.js`:

```js
    Trex.config = {
        ...
        GRAVITY: 0.6,                // how hard the dino falls back down
        ...
        INIITAL_JUMP_VELOCITY: -10,  // how hard the dino jumps (more negative = higher)
```

Try `GRAVITY: 0.2` for a moon jump, or `INIITAL_JUMP_VELOCITY: -16` to leap
over everything. Yes, `INIITAL` is misspelled; that's how it is in Chrome's
source, so leave the spelling alone or the game won't find the setting.

`Runner.config` also has `GRAVITY` and `INITIAL_JUMP_VELOCITY` lines, but
changing those does nothing when the game starts. Use the ones in
`Trex.config`.

### 5. Make it your own

Some ideas, roughly from easiest to hardest:

- Change `INVERT_DISTANCE` so night mode starts almost right away.
- Change the page background or add a title in `index.css` and `index.html`.
- Redraw the dino in `assets/default_100_percent/100-offline-sprite.png`
  with any image editor. Keep the image the same size and draw over the
  existing shapes in the same positions; the game cuts each picture out of
  this sheet by its exact location.
- Read `Trex.prototype` in `index.js` to see how jumping and ducking work,
  then add something new, such as a double jump.

### 6. Save your work to GitHub

Your files live in the `t-rex-runner` folder on your own computer, so they're
safe even if the container is deleted or rebuilt. To save a change to GitHub:

```bash
git add .
git commit -m "Describe what you changed"
git push
```

To push, the repo has to be yours. If you cloned someone else's copy, create
an empty repo on GitHub (no README) and point your folder at it first:

```bash
git remote set-url origin https://github.com/YOUR-USERNAME/t-rex-runner.git
git push -u origin main
```

### 7. Leave the container

Stop the server with `Ctrl+C`, then click the **Dev Container** box in the
bottom-left corner and choose **Reopen Folder Locally**. The container stops
on its own shortly after.

---

## Instructor notes

The container is `mcr.microsoft.com/devcontainers/python:3.12`. Python is only
used for its built-in web server; nothing is installed on top of it, so there's
no build step beyond downloading the image.

To publish a playable version, go to the repo's **Settings → Pages**, set the
source to the `main` branch, and save. GitHub gives you a link anyone can open
to play.

---

## Credits

- **The game** is the Chrome offline dinosaur game, extracted from
  [Chromium](https://www.chromium.org/) by
  [wayou/t-rex-runner](https://github.com/wayou/t-rex-runner) and licensed
  under the BSD 3-Clause License. The license text is in [`LICENSE`](LICENSE);
  keep it in this repo.
- The original repo also lists fun forks (Kumamon runner, a self-playing bot
  and more); their preview GIFs are still in the `assets` folder.
