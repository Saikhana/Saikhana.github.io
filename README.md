# Resume website: editing guide

A personal resume page built with plain HTML and CSS. There's no framework, build step or install. Open `index.html` in a browser to see it.

## Files

```
resume/
├── index.html          All the content (text, links, sections)
├── styles.css          All the styling (colors, layout, animations)
├── README.md           This guide
└── logos/
    ├── profile.jpeg            Original full-size photo (keep as the source)
    ├── profile-web.jpg         400x400 web version used on the page
    ├── Ericsson-Emblem.png     Experience logos
    ├── novelsoft.jpg
    ├── elte.png                Education logos
    ├── NationalUniversityMongoliaLogo.png
    └── skills/                 Skill logos (java.svg, docker.svg, ...)
```

Rule of thumb: change what the page says in `index.html`, and how it looks in `styles.css`.

## Previewing changes

1. Double-click `index.html`, or run `start index.html` from this folder in PowerShell.
2. After saving a change, refresh the browser (F5). If a change doesn't show up, press Ctrl+F5 to bypass the cache.
3. To check the phone layout, press F12 in Edge or Chrome, then click the phone/tablet icon (Ctrl+Shift+M) and pick a device.

## Editing content (`index.html`)

Each section starts with a comment such as `<!-- Experience -->`, so Ctrl+F for the section name gets you there quickly.

### Name, role, location, status

In the sidebar, inside `<div class="identity">`:

```html
<h1 class="name">Saikhantuya<br />Mihalec</h1>
<p class="role">Software Developer</p>
<p class="meta-line">Hungary</p>
<p class="meta-line meta-line--status"><span class="status-dot"></span>Open to opportunities</p>
```

The name also appears in the top bar (`topnav-name`), the browser tab (`<title>`), the footer and the photo's `alt` text. Update those too if it changes.

To remove the "Open to opportunities" line, delete that whole `<p>` line.

### Contact links (Email, GitHub, LinkedIn)

They appear in three places:

1. The top bar, in `<nav class="topnav-links">`.
2. The "Get in touch" button and the References note (`mailto:` links).
3. The print-only line, `<p class="print-only">`. This line only appears in the PDF, since the top bar is hidden when printing.

Search the file for `zsaikhantuya@gmail.com` (or the old link) to find every occurrence.

### Intro paragraph

In `<section class="block" id="about">`. Text inside `<strong>…</strong>` is shown in rose.

### Adding a job

Copy one whole `<article class="entry">…</article>` block inside the Experience section, paste it where it belongs (newest first), and change the details:

```html
<article class="entry">
  <div class="logo"><img src="logos/COMPANY-LOGO.png" alt="Company Name" width="48" height="48" /></div>
  <div class="entry-main">
    <div class="entry-head">
      <h3>Job Title</h3>
      <span class="entry-period">Jan 2027 — Present</span>
    </div>
    <p class="entry-org">Company Name</p>
    <ul class="responsibilities">
      <li>What you did.</li>
      <li>Another thing you did.</li>
    </ul>
  </div>
</article>
```

When a job ends, change `Present` to the end month.

Education entries use the same block. Education entries simply leave out the `<ul class="responsibilities">` list.

### Highlights

Each card is an `<article class="highlight">` with a title, a description and a tech line. Copy a card to add one. The cards sit side by side when there's room and wrap automatically.

### Courses and licences

One `<li>` per item in `<ul class="responsibilities courses">`. Put the newest first.

### References

Each person is a `<div class="ref">` block. Contact details are deliberately not shown on the page; the note asks people to email you for them.

### Section numbers

The pills (`01`, `02`, …) are typed by hand in each `<span class="section-title-no">`. If you add, remove or reorder a section, renumber them.

## Skills

### Logo skills

Each logo tile looks like this:

```html
<li class="skill-tile" data-label="Docker" tabindex="0"><img src="logos/skills/docker.svg" alt="Docker" width="26" height="26" /></li>
```

- `data-label` is the name shown in the hover tooltip and in the PDF.
- `alt` is for screen readers. Keep it the same as the label.

To add a skill with a logo:

1. Find the icon on [devicon.dev](https://devicon.dev) and note its folder name (for example `react`).
2. Download it into `logos/skills/` from PowerShell:
   ```powershell
   Invoke-WebRequest "https://cdn.jsdelivr.net/gh/devicons/devicon@v2.16.0/icons/react/react-original.svg" -OutFile "logos/skills/react.svg"
   ```
   If that file doesn't exist, try `-plain.svg` instead of `-original.svg`.
3. Copy a `<li class="skill-tile">` line and change the file name, `data-label` and `alt`.

The current icons come from Devicon v2.16.0 (MIT license).

### Text-only skills

Skills without a well-known logo are text chips in `<div class="skills skills--extra">`, one `<span class="skill">` each.

## Images

### Changing your photo

The page uses `logos/profile-web.jpg`, a 400x400 crop made from `profile.jpeg`. Don't use a big original photo directly: the browser has to shrink it a lot, which looks jagged and loads slowly.

To make a new web version, save the new photo as `logos/profile.jpeg`, then run this in PowerShell from the `resume` folder:

```powershell
Add-Type -AssemblyName System.Drawing
$src  = [System.Drawing.Image]::FromFile("$PWD\logos\profile.jpeg")
$side = [math]::Min($src.Width, $src.Height)
$x = [int](($src.Width - $side) / 2)
$y = [int](($src.Height - $side) * 0.3)   # 0.3 keeps the crop a bit above centre (face)
$out = New-Object System.Drawing.Bitmap 400, 400
$g = [System.Drawing.Graphics]::FromImage($out)
$g.InterpolationMode = 'HighQualityBicubic'
$g.DrawImage($src, (New-Object System.Drawing.Rectangle 0,0,400,400), (New-Object System.Drawing.Rectangle $x,$y,$side,$side), 'Pixel')
$codec = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() | Where-Object { $_.MimeType -eq 'image/jpeg' }
$ep = New-Object System.Drawing.Imaging.EncoderParameters 1
$ep.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter ([System.Drawing.Imaging.Encoder]::Quality), 90L
$out.Save("$PWD\logos\profile-web.jpg", $codec, $ep)
$g.Dispose(); $out.Dispose(); $src.Dispose()
```

Phone photos are sometimes saved sideways. If the result comes out rotated, rotate the original in the Photos app, save it, and run the script again.

If the photo file is missing, the page shows "SM" initials on a gradient instead.

### Company and school logos

Put the file in `logos/` and point the entry's `<img src="logos/...">` at it. A square image works best. Wide logos get cropped, because the box crops images to fill the square.

Use file names without spaces (for example `my-logo.png`).

## Styling (`styles.css`)

### Colors

All the main colors are variables at the very top of `styles.css`, inside `:root { … }`. Change a value there and it updates everywhere.

| Variable | What it colors |
| --- | --- |
| `--bg` | Page background base color |
| `--ink`, `--ink-soft`, `--ink-mute` | Text: headings, body text, small grey text |
| `--accent`, `--accent-ink` | Rose text such as company names and links |
| `--grad` | Pink-to-lilac gradient (bullets, status dot, photo blob) |
| `--cta`, `--cta-hover` | Hot-rose buttons: "Get in touch", "Email" and the section number pills |
| `--cta-solid` | Color of the arrow inside "Get in touch" |
| `--cta-ink` | Text color on those buttons (white) |

The soft glows in the background are in the `body { background-image: … }` rule, just below `:root`.

### Font

The font is Plus Jakarta Sans from Google Fonts, loaded in the `<head>` of `index.html` and set in `--font`. To change it, replace both the Google Fonts link and the `--font` value.

### Layout sizes

- `--sidebar-w`: sidebar width (310px)
- `--nav-h`: top bar height (56px)
- `.shell { max-width: 1120px }`: overall page width

## Decorations and animations

### Photo frame

In `index.html`, inside `<div class="portrait">`:

- `.portrait-blob`: the pink/lilac blob behind the photo
- `.portrait-orbit`: the thin oval line, with five sparkles (`spark--a` to `spark--e`)
- `.avatar`: the morphing blob-shaped photo

In `styles.css`:

- Sparkle speed: the `2.8s` in the `.portrait-orbit .spark` animation. Smaller is faster.
- Sparkle size and glow: the `@keyframes sparkle` rule.
- Photo morph speed: the `9s` in `.avatar`, and the `11s` in `.portrait-blob`.

### Swaying flower

The flower is drawn in SVG at the bottom of `index.html` (`<div class="floral-deco">`) and styled by `.floral-deco` in `styles.css`.

- Size: `--flower-w: 230px`
- Visibility: `opacity: 0.72`
- Position: the `right:` value. It's set so the stem sits on the right edge of the content, with about half the flower behind the sections.
- Sway speed: the `5.5s` and `4s`–`4.6s` values in `.sway-stem`, `.sway-bloom` and the rules below them.
- The flower is hidden on phones (below 880px wide).

### Reduced motion

People who turn off animations in their system settings see everything still. That's the `@media (prefers-reduced-motion: reduce)` rule. If you add a new animation, add its selector there too.

## Phone and tablet layout

At 880px wide and below:

- The sidebar sits above the content instead of beside it.
- The profile (photo, name, role, location, status) is centered.
- The flower is hidden.

The section links in the top bar are hidden below 1000px wide, and the name in the top bar is hidden below 440px wide.

On desktop, the sidebar stays in place while you scroll, but only on screens at least 900px tall. On shorter screens it scrolls with the page, so nothing gets cut off.

## Printing / saving as PDF

Press Ctrl+P and choose "Save as PDF". The print styles at the bottom of `styles.css` (`@media print`):

- hide the top bar, buttons, flower, photo frame and animations;
- show the contact line under your name;
- show each skill logo with its name next to it.

In the print dialog, turn on "Background graphics" to keep the colors.

## The original photo

`logos/profile.jpeg` isn't shown on the page. It's the full-size source used to make `profile-web.jpg`. Keep it if you might make a new web photo later. Before publishing online, consider moving it out of this folder, since it's 1.4 MB and visitors don't need it.

## Publishing on GitHub Pages

The folder is ready to upload as-is:

- `.gitignore` keeps `logos/profile.jpeg` (the 1.4 MB original photo) and junk files off GitHub. The photo stays on your computer.
- `.nojekyll` tells GitHub Pages to publish the files exactly as they are.
- Every image path matches its file name exactly, including upper/lower case. GitHub's servers are case-sensitive, unlike Windows. Keep this in mind when you add new images.

### 1. Create the repository on GitHub

On github.com, click **New repository**:

- **Name:** `resume` (site at `https://saikhana.github.io/resume/`) or `Saikhana.github.io` (site at `https://saikhana.github.io/`).
- **Public.** Free GitHub Pages requires a public repository, so all these files are visible to anyone.
- Don't add a README, `.gitignore` or license, since the folder already has them.

### 2. Upload from your computer

Copy this whole folder to the other device. Then open a terminal in the folder and run these commands, replacing `REPO` with your repository name:

```bash
git init
git add .
git commit -m "Initial resume site"
git branch -M main
git remote add origin https://github.com/Saikhana/REPO.git
git push -u origin main
```

If git asks who you are before the commit, set your name and email once. Use the email linked to your GitHub account:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

When you push, git asks you to sign in to GitHub (usually through a browser window).

### 3. Turn on Pages

In the repository on GitHub: **Settings → Pages → Build and deployment**. Set Source to **Deploy from a branch**, the branch to `main`, and the folder to `/ (root)`, then click **Save**. After a minute or two the site is live at the address from step 1.

### Updating the site later

Edit the files, then run:

```bash
git add .
git commit -m "Describe what you changed"
git push
```

The live site updates about a minute later.

Everything the page needs is in this folder, except the font, which loads from Google Fonts. Any other static host also works, for example dragging the folder onto [app.netlify.com/drop](https://app.netlify.com/drop).
