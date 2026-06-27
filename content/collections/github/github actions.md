---
title: "github actions"
---

# how do i use - name: Blog Post Workflow
  uses: gautamkrishnar/blog-post-workflow@1.9.4
To use the **Blog Post Workflow**, you essentially need to create a "landing zone" in your README and then a "worker" (the YAML file) to go out and fetch your RSS data.

Since you've already been working with GitHub Actions for your metrics, this will feel very familiar.

---

## 1. Prepare your README

You need to tell the action exactly where to paste the blog posts. Open your `README.md` and add these "comment tags" wherever you want the list to appear:

Markdown

```
# 📚 Latest Etymonline Columns
```

_Note: The action will automatically replace everything between these two lines._

---

## 2. Create the Workflow File

Create a new file at `.github/workflows/blog-post-workflow.yml`. You can use the same `METRICS_TOKEN` secret or the default `${{ github.token }}` for this.

YAML

```
name: Latest blog post workflow
on:
  schedule:
    # Runs every day at midnight
    - cron: '0 0 * * *'
  workflow_dispatch: # Allows you to run it manually

jobs:
  update-readme-with-blog:
    name: Update this repo's README with latest blog posts
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Pull in RSS Feed
        uses: gautamkrishnar/blog-post-workflow@1.9.4
        with:
          # Replace with your RSS feed link
          feed_list: "https://www.etymonline.com/feed/columns.xml"
          # Max number of posts to show
          max_post_count: 5
```

---

## 3. Configuration Options

You can customize how the list looks by adding these under the `with:` section:

|**Option**|**What it does**|
|---|---|
|`template: "$title - $url"`|Changes how each line is formatted.|
|`max_post_count: 10`|Limits the number of posts shown.|
|`committer_username: "jubilancy-bot"`|Changes the name that appears on the commit.|

---

### 💡 Why use this vs. the Metrics plugin?

You might notice that we already added an RSS plugin to your **Metrics** workflow.

- **Use Metrics** if you want the RSS feed to be part of an **image (.svg)** (perfect for your "Terminal" look).
    
- **Use Blog Post Workflow** if you want the feed to be **actual text/links** in your README that people can click and that search engines can read.
    

Since you are building a digital garden, having the text version (this workflow) is often better for navigation!

[GitHub Actions tutorial](https://www.youtube.com/watch?v=BQrohJ3PT7I)

This video walk-through is helpful if you want to see exactly how to navigate the Actions tab to trigger these workflows manually during your setup.

# can i use workflows for my website? like a plugin?

Yes, you can absolutely do that! In fact, many developers create a dedicated "meta" or "ops" repository to keep their main project code (like your Astro site) clean and focused.

Since you're building a digital garden at `eliana.lol`, having a separate **`jubilancy/workflows`** repo to act as your "data engine" is a very pro move.

### 🏗️ How to Connect Them

You don't just "link" them; you tell the workflow in Repo A to **push** the result into Repo B.

#### 1. The "Push File" Method (Recommended)

You keep all your `.yml` files in your new `workflows` repo. At the end of the script, you add a step that "ships" the generated SVG over to your Astro repo.

**Add this step to your `.yml` in the workflows repo:**

YAML

```
      - name: Push SVG to Astro Repo
        uses: dmnemec/copy_file_to_another_repo_action@main
        env:
          API_TOKEN_GITHUB: ${{ secrets.metrics }} # Use your existing PAT
        with:
          source_file: 'github-metrics.svg'
          destination_repo: 'jubilancy/eliana-lol' # Your Astro repo name
          destination_folder: 'public/metrics'    # Puts it in Astro's public folder
          user_email: 'wellabovewonder@gmail.com'
          user_name: 'jubilancy'
          commit_message: 'chore: update dynamic metrics'
```

---

### 🚀 Why this is better for you:

- **Clean Astro Repo:** Your `eliana.lol` repo won't have 5+ different `.yml` files and experimental branches cluttering it up.
    
- **Centralized Control:** If you decide to make a second website later, you can just add another "Push" step to the same workflow and have your stats show up in both places instantly.
    
- **Easy Assets:** By pushing to the `/public/metrics` folder in Astro, you can reference the image in your code simply as `<img src="/metrics/github-metrics.svg" />`.
    

### ⚠️ One Small Requirement

To let Repo A talk to Repo B, the **Personal Access Token (PAT)** you created (`metrics`) needs to have "Repo" permissions for **both** repositories. Since you likely created a "Fine-grained" or "Classic" token with your account as the owner, it should already have the power to do this!

### 🧪 Your Testing Strategy

Since you're currently running those 4 theme tests:

1. **Create** the new `workflows` repo.
    
2. **Move** those 4 `.yml` files there.
    
3. **Run** them and see if they successfully "teleport" the SVGs over to your Astro project.
    

Do you want me to rewrite one of those theme files specifically to include the "Push" code so you can test it?


# can i make a repository of workflows and have it connect to my astro repo

Yes, you can absolutely do that! In fact, many developers create a dedicated "meta" or "ops" repository to keep their main project code (like your Astro site) clean and focused.

Since you're building a digital garden at `eliana.lol`, having a separate **`jubilancy/workflows`** repo to act as your "data engine" is a very pro move.

### 🏗️ How to Connect Them

You don't just "link" them; you tell the workflow in Repo A to **push** the result into Repo B.

#### 1. The "Push File" Method (Recommended)

You keep all your `.yml` files in your new `workflows` repo. At the end of the script, you add a step that "ships" the generated SVG over to your Astro repo.

**Add this step to your `.yml` in the workflows repo:**

YAML

```
      - name: Push SVG to Astro Repo
        uses: dmnemec/copy_file_to_another_repo_action@main
        env:
          API_TOKEN_GITHUB: ${{ secrets.metrics }} # Use your existing PAT
        with:
          source_file: 'github-metrics.svg'
          destination_repo: 'jubilancy/eliana-lol' # Your Astro repo name
          destination_folder: 'public/metrics'    # Puts it in Astro's public folder
          user_email: 'wellabovewonder@gmail.com'
          user_name: 'jubilancy'
          commit_message: 'chore: update dynamic metrics'
```

---

### 🚀 Why this is better for you:

- **Clean Astro Repo:** Your `eliana.lol` repo won't have 5+ different `.yml` files and experimental branches cluttering it up.
    
- **Centralized Control:** If you decide to make a second website later, you can just add another "Push" step to the same workflow and have your stats show up in both places instantly.
    
- **Easy Assets:** By pushing to the `/public/metrics` folder in Astro, you can reference the image in your code simply as `<img src="/metrics/github-metrics.svg" />`.
    

### ⚠️ One Small Requirement

To let Repo A talk to Repo B, the **Personal Access Token (PAT)** you created (`metrics`) needs to have "Repo" permissions for **both** repositories. Since you likely created a "Fine-grained" or "Classic" token with your account as the owner, it should already have the power to do this!

### 🧪 Your Testing Strategy

Since you're currently running those 4 theme tests:

1. **Create** the new `workflows` repo.
    
2. **Move** those 4 `.yml` files there.
    
3. **Run** them and see if they successfully "teleport" the SVGs over to your Astro project.
    

Do you want me to rewrite one of those theme files specifically to include the "Push" code so you can test it?

# make me a repository tree of what it should look like. also now im curioous things/tools many devs create/use/etc 

This is a very "pro" way to organize your digital ecosystem. By separating your **site logic** (Astro) from your **data engine** (Workflows), you keep your main repository clean and focused on your content.

---

## 🌳 The "Two-Repo" Ecosystem Tree

Here is how your repositories should look to keep everything organized:

### 1. The "Data Engine" (`jubilancy/workflows`)

This repository is just for your automation. It’s like the "back office" of your digital garden.

Plaintext

```
workflows/
├── .github/
│   └── workflows/
│       ├── metrics-theme-terminal.yml    # Generates the SVG
│       ├── blog-post-workflow.yml       # Fetches Etymonline RSS
│       └── dictionary-sync.yml          # (Optional) Syncs your dictionary data
├── scripts/
│   └── custom-scraper.py                # Any custom web-scraping scripts
└── README.md                            # Documentation for your automations
```

### 2. The "Digital Garden" (`jubilancy/eliana-lol`)

This is your Astro site. It stays "dumb" and just displays the files the workflows send to it.

Plaintext

```
eliana-lol/
├── public/
│   └── assets/
│       └── metrics/
│           └── github-metrics.svg       # Teleported here by your workflow repo
├── src/
│   ├── components/
│   │   └── MetricsCard.astro            # A component to display the SVG
│   └── pages/
│       ├── index.astro
│       └── dictionary.astro             # Your dictionary project page
├── astro.config.mjs
└── package.json
```

---

## 🛠️ Things "Pro" Devs Create & Use

Since you're curious about what else devs build to automate their lives, here are the most common "Meta-Projects" in the community:

### 1. Dotfiles Repository (`jubilancy/dotfiles`)

Since you like customizing your **Terminal**, this is a must.

- **What it is:** A repo containing all your hidden config files (`.zshrc`, `.vimrc`, `.gitconfig`).
    
- **Why:** If you get a new MacBook or break your current setup, you run one command and your entire terminal, shortcuts, and themes are restored in seconds.
    

### 2. The "Second Brain" / Digital Garden

- **Tools:** **Obsidian**, **Logseq**, or **Quartz**.
    
- **Dev Move:** They use a GitHub Action to automatically turn their private notes into a public website (like your `eliana.lol`).
    

### 3. Personal API / "Middleman"

- **Tools:** **Cloudflare Workers**, **n8n**, or **Pipedream**.
    
- **What it is:** A single URL that gathers data from your Spotify, Goodreads, and GitHub, and returns it as a single JSON file.
    
- **Why:** So your website only has to make **one** request to see what you're currently listening to, reading, and coding.
    

### 4. Custom Raycast Extensions

- **Tools:** **Raycast** (since you’re on a Mac).
    
- **Dev Move:** Instead of going to a website, they write a small script to search their personal dictionary or check their Astro build status directly from their Mac search bar.
    

### 5. Self-Updating "Now" Page

- **Concept:** A `/now` page on your site that isn't a blog, but a live dashboard of your life.
    
- **Automation:** Use the **Blog Post Workflow** you just learned to pull in your latest bookmarks, letters, or dictionary entries automatically.
    

---

### 💡 Which one sounds most interesting?

Since you're already doing **Digital Gardening** and **Dictionary Building**, I think a **Dotfiles** repo or a **Raycast Extension** for your dictionary would be a really fun next step for your workflow.

Are your theme tests finished running yet? I'm excited to see which one you choose for the Astro site!
