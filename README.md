# Image Rendering Test — GitHub / Bitbucket README

This README is a **test fixture** for the Code Exchange user story: verifying that images embedded in a repo's `README.md` via different upload/reference methods render correctly when pulled into the Community (ComDev) Code Exchange page.

Each section below uses a distinct image-embedding technique. Use this matrix to confirm which methods render, which fall back to a broken-image icon, and which need admin link-editing.

---

## 1. Drag-and-drop upload (GitHub issue/PR/README editor)

When you drag an image directly into the GitHub web editor (README, issue, or PR comment box), GitHub uploads it to its user-content CDN and auto-inserts markdown pointing to a generated URL, e.g.: <img width="800" height="450" alt="architecture-diagram" src="https://github.com/user-attachments/assets/09cfbe4f-e481-4540-a6a5-2063fef7b4de" />


```markdown
![Dropped screenshot](https://github.com/user-attachments/assets/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
```

- **Pros:** No need to commit the binary to the repo; quick for issues/PRs.
- **Test note:** Older repos may instead show the legacy `user-images.githubusercontent.com` domain — worth testing both, since some proxies/CSPs allow one but not the other.

## 2. Relative path to a committed image file

The most common method: the image file is committed into the repository (e.g., under `/images` or `/docs/assets`) and referenced with a relative path.
![Architecture diagram](./images/dashboard-preview.png)
```markdown
![Architecture diagram](./images/architecture-diagram.png)
```

- **Pros:** Version-controlled with the code; works offline/locally.
- **Test note:** This is the case most likely to break in an external renderer (like ComDev) because the relative path must be resolved against the repo's raw content root — confirm the Code Exchange page rewrites these correctly.

## 3. Absolute raw content URL

Instead of a relative path, the raw file URL is used directly.

**GitHub:**
![Logo](https://raw.githubusercontent.com/aditibalur-0407/GitHubRepoReadmeTesting/refs/heads/main/images/logo.png)
```markdown
![Logo](https://raw.githubusercontent.com/<org>/<repo>/main/images/logo.png)
```

**Bitbucket:**
```markdown
![Logo](https://bitbucket.org/<workspace>/<repo>/raw/main/images/logo.png)
```

- **Pros:** Portable — renders correctly regardless of the consuming platform's path-resolution logic.
- **Test note:** Good positive-control case; if this fails to render in ComDev, the issue is platform-wide, not a path-resolution bug.

## 4. HTML `<img>` tag (with sizing attributes)

Markdown alone can't control image width/height, so authors often drop to raw HTML:

<img src="./images/feature-demo.gif" alt="Feature Demo" width="600" /> 

```html
<img src="./images/dashboard-preview.png" alt="Dashboard preview" width="600" />
```

- **Test note:** Confirms whether the Code Exchange renderer sanitizes/strips HTML `<img>` tags (some Markdown-to-HTML sanitizers do) or preserves them.

## 5. Badges / dynamically generated images (shields.io)

Build-status and version badges are technically remote images pulled from a third-party service at render time:
![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge)
![Coverage](https://img.shields.io/badge/coverage-95%25-orange)
![Dynamic JSON Badge](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fapi.github.com%2Frepos%2Foctocat%2FHello-World&query=%24.stargazers_count&style=flat&label=stars)
```markdown
![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
```

- **Test note:** These render live from an external CDN — good for testing whether the Community page allows outbound image requests to third-party domains, not just GitHub/Bitbucket's own CDNs.

## 6. Bitbucket wiki-style embed / attachment link

Bitbucket README/wiki pages sometimes reference attachments uploaded through the Bitbucket UI rather than committed to the repo tree:

```markdown
![Uploaded diagram](https://bitbucket.org/<workspace>/<repo>/downloads/diagram.png)
```

- **Test note:** Downloads/attachments are a separate storage area from the repo tree in Bitbucket — a common source of broken links if the Code Exchange integration only fetches repo-tree content.

## 7. Animated GIF for demo/preview

```markdown
![Feature demo](./images/feature-demo.gif)
```

- **Test note:** Confirms GIF (not just static PNG/JPG) support, and whether autoplay/loop behavior survives the embed.

## 8. Video embed (fallback-to-thumbnail case)

Neither GitHub nor Bitbucket Markdown natively embeds `<video>` in READMEs the way HTML pages do; the common workaround is a linked thumbnail:

[![Watch the demo](./images/video-thumbnail.png)](https://www.youtube.com/watch?v=nVNIoQUcFI4)
```markdown
[![Watch the demo](./images/video-thumbnail.png)](https://youtu.be/xxxxxxxxxxx)
```

- **Test note:** This is a *fallback* case for the user story — confirms the admin link-editing / fallback requirement when true video embedding isn't supported and a clickable thumbnail is substituted instead.


