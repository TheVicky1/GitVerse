# Profile README Embeds: Showcase Your GitHub Achievements

This guide shows how to add your GitVerse achievement report, badges, and links to your GitHub Profile README (the special `<username>/<username>` repository) or to your personal portfolio and blog.

Every snippet below is ready to copy and paste. Just replace `YOUR_USERNAME` with your GitHub username.

---

## 1. Introduction

### Why showcase your achievements?

- **Visibility:** Recruiters, maintainers, and other developers see your open-source activity right away, without digging through your contribution graph.
- **Credibility:** Achievements like Pull Shark or Pair Extraordinaire show that you contribute merged work and collaborate with others.
- **Engagement:** A clickable badge invites visitors to explore your full report, which keeps them on your profile longer.
- **Motivation:** Seeing your current tier next to the next one is a simple reminder of what to work toward.

### Link to your personal GitVerse report

Every public GitHub user has a shareable GitVerse report at this address:

```text
https://mygitverse.vercel.app/result?u=<username>
```

For example, the report for the user `octocat` is:

```text
https://mygitverse.vercel.app/result?u=octocat
```

> **Tip:** You can also look up any user from the check page at [mygitverse.vercel.app/check/](https://mygitverse.vercel.app/check/). Signing in there lets you include private contributions, but the shareable `result?u=` link only shows public data.

---

## 2. Markdown Embed Recipes

Paste any of these recipes into the `README.md` of your `<username>/<username>` repository.

### Recipe 1: Direct Shield Badge

A lightweight blue [Shields.io](https://shields.io) badge that links to your GitVerse report:

```markdown
[![GitHub Achievements](https://img.shields.io/badge/GitHub-Achievements-blue?logo=github)](https://mygitverse.vercel.app/result?u=YOUR_USERNAME)
```

**Preview:**

[![GitHub Achievements](https://img.shields.io/badge/GitHub-Achievements-blue?logo=github)](https://mygitverse.vercel.app/result?u=YOUR_USERNAME)

You can change the text and color by editing the badge URL. The format is `https://img.shields.io/badge/<LEFT_TEXT>-<RIGHT_TEXT>-<COLOR>?logo=github`, for example:

```markdown
[![GitVerse Report](https://img.shields.io/badge/GitVerse-View%20My%20Report-e2b53a?logo=github&logoColor=white)](https://mygitverse.vercel.app/result?u=YOUR_USERNAME)
```

> **Note:** Use `%20` for spaces, and write a literal dash as `--` inside badge text.

### Recipe 2: Trophy Case & Quick Link Button

A larger call-to-action button that uses the GitVerse `check-button.svg` image:

```html
<a href="https://mygitverse.vercel.app/result?u=YOUR_USERNAME">
  <img src="https://raw.githubusercontent.com/TheVicky1/GitVerse/main/docs/check-button.svg" alt="Track My Achievements on GitVerse" width="400" />
</a>
```

**Preview:**

<a href="https://mygitverse.vercel.app/result?u=YOUR_USERNAME">
  <img src="https://raw.githubusercontent.com/TheVicky1/GitVerse/main/docs/check-button.svg" alt="Track My Achievements on GitVerse" width="400" />
</a>

> **Tip:** Adjust the `width` attribute to make the button larger or smaller. The original image is 520 × 84 pixels.

### Recipe 3: Text Badge Summary Table

If you prefer a plain-text overview, list the achievements you have unlocked, your current tier, and a link to each badge guide on GitVerse. Edit the **Tier** column to match your own progress and delete the rows you have not unlocked yet.

```markdown
| Achievement | My Tier | How It Is Earned | Guide |
| :--- | :--- | :--- | :--- |
| 🦈 **Pull Shark** | Silver (x3) | Pull requests that get merged | [View guide](https://mygitverse.vercel.app/achievements/pull-shark/) |
| ⭐ **Starstruck** | Default (x1) | Stars on a repository you own | [View guide](https://mygitverse.vercel.app/achievements/starstruck/) |
| 👥 **Pair Extraordinaire** | Bronze (x2) | Co-authored commits on merged pull requests | [View guide](https://mygitverse.vercel.app/achievements/pair-extraordinaire/) |
| 🧠 **Galaxy Brain** | Default (x1) | Accepted answers in GitHub Discussions | [View guide](https://mygitverse.vercel.app/achievements/galaxy-brain/) |
| ⚡ **Quickdraw** | Unlocked | Closing an issue or pull request within 5 minutes of opening it | [View guide](https://mygitverse.vercel.app/achievements/quickdraw/) |
| 🚀 **YOLO** | Unlocked | Merging a pull request without a review | [View guide](https://mygitverse.vercel.app/achievements/yolo/) |
```

**Preview:**

| Achievement | My Tier | How It Is Earned | Guide |
| :--- | :--- | :--- | :--- |
| 🦈 **Pull Shark** | Silver (x3) | Pull requests that get merged | [View guide](https://mygitverse.vercel.app/achievements/pull-shark/) |
| ⭐ **Starstruck** | Default (x1) | Stars on a repository you own | [View guide](https://mygitverse.vercel.app/achievements/starstruck/) |
| 👥 **Pair Extraordinaire** | Bronze (x2) | Co-authored commits on merged pull requests | [View guide](https://mygitverse.vercel.app/achievements/pair-extraordinaire/) |
| 🧠 **Galaxy Brain** | Default (x1) | Accepted answers in GitHub Discussions | [View guide](https://mygitverse.vercel.app/achievements/galaxy-brain/) |
| ⚡ **Quickdraw** | Unlocked | Closing an issue or pull request within 5 minutes of opening it | [View guide](https://mygitverse.vercel.app/achievements/quickdraw/) |
| 🚀 **YOLO** | Unlocked | Merging a pull request without a review | [View guide](https://mygitverse.vercel.app/achievements/yolo/) |

#### Tier thresholds

Use this reference to fill in the **My Tier** column:

| Achievement | Default (x1) | Bronze (x2) | Silver (x3) | Gold (x4) |
| :--- | :---: | :---: | :---: | :---: |
| Pull Shark (merged pull requests) | 2 | 16 | 128 | 1024 |
| Starstruck (stars on one repository) | 16 | 128 | 512 | 4096 |
| Pair Extraordinaire (co-authored merged pull requests) | 1 | 10 | 24 | 48 |
| Galaxy Brain (accepted answers) | 2 | 8 | 16 | 32 |

Quickdraw and YOLO have a single tier: they are either unlocked or not. See the full list of achievements at [mygitverse.vercel.app/achievements/](https://mygitverse.vercel.app/achievements/).

---

## 3. Portfolio & Blog Integrations

These HTML snippets work on personal websites, blogs, and static site generators such as Jekyll, Hugo, Astro, or plain HTML pages.

### Standard HTML badge link

```html
<a href="https://mygitverse.vercel.app/result?u=YOUR_USERNAME" target="_blank" rel="noopener noreferrer">
  <img
    src="https://img.shields.io/badge/GitHub-Achievements-blue?logo=github"
    alt="My GitHub Achievements on GitVerse"
  />
</a>
```

### Image button

```html
<a href="https://mygitverse.vercel.app/result?u=YOUR_USERNAME" target="_blank" rel="noopener noreferrer">
  <img
    src="https://raw.githubusercontent.com/TheVicky1/GitVerse/main/docs/check-button.svg"
    alt="Track My Achievements on GitVerse"
    width="400"
  />
</a>
```

### Styled call-to-action button

This version uses inline styles, so it works without any extra CSS file:

```html
<a
  href="https://mygitverse.vercel.app/result?u=YOUR_USERNAME"
  target="_blank"
  rel="noopener noreferrer"
  style="display: inline-block; padding: 10px 18px; background-color: #142430; color: #e2b53a; border: 2px solid #e2b53a; border-radius: 8px; font-family: system-ui, sans-serif; font-weight: 600; text-decoration: none;"
>
  🏆 View My GitHub Achievements on GitVerse
</a>
```

> **Note:** GitHub removes `style` attributes from README files, so this styled button only looks right on your own website. In a GitHub README, use Recipe 1 or Recipe 2 instead.

---

## ✅ Tips for Best Results

- **Replace every `YOUR_USERNAME`:** Search your file for `YOUR_USERNAME` before saving so no placeholder is left behind.
- **Keep it visible:** Put your badge near the top of your README, next to your social links or tech stack.
- **Test your links:** After committing, open your profile and click each badge to make sure it opens your own report.
- **Keep the table up to date:** The summary table in Recipe 3 is static text, so update the tiers when you level up.
