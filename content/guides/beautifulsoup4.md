---
tags:
  - guides
  - terminal
---
# BeautifulSoup4 (bs4) Reference Guide

## Overview

**BeautifulSoup4** (`bs4`) is a Python library for parsing HTML and XML documents. It creates a navigable parse tree from page source code, making it easy to extract data, follow links, and scrape structured content from websites. It is almost always paired with the **`requests`** library, which fetches the raw HTML, while BeautifulSoup handles the parsing.

**Dependencies installed in this session:**

| Package | Version | Role |
|---------|---------|------|
| `beautifulsoup4` | 4.14.3 | HTML/XML parser |
| `requests` | 2.32.5 | HTTP client to fetch pages |
| `soupsieve` | 2.8.3 | CSS selector engine for bs4 |
| `lxml` / `html.parser` | built-in | Parser backends |

---

## Installation (macOS with Homebrew Python)

On modern macOS with Homebrew-managed Python, you **cannot** install packages system-wide with `pip` (PEP 668 restriction). The correct approach is a **virtual environment**.

```zsh
# Navigate to your project folder
cd ~/Desktop

# Create a virtual environment
python3 -m venv url-parser-env

# Activate it
source url-parser-env/bin/activate

# Install both libraries
pip install beautifulsoup4 requests

# Optionally upgrade pip
pip install --upgrade pip
```

> **Why not `pip install` directly?**  
> macOS reports `error: externally-managed-environment` to prevent breaking Homebrew's Python install. Always use a venv or `pipx` for CLI tools.

**Deactivate when done:**

```zsh
deactivate
```

---

## Core Concepts

### Parsing a page

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://example.com")
soup = BeautifulSoup(response.text, "html.parser")
```

The second argument to `BeautifulSoup()` is the **parser**:

| Parser | Install | Notes |
|--------|---------|-------|
| `html.parser` | built-in | Good default, pure Python |
| `lxml` | `pip install lxml` | Faster, more lenient |
| `html5lib` | `pip install html5lib` | Most lenient, slowest |

---

## Navigating the Tree

### Finding elements

```python
# Find the first matching tag
soup.find("h1")

# Find all matching tags
soup.find_all("a")

# Find by CSS class
soup.find("div", class_="article")

# Find by id
soup.find(id="main-content")

# CSS selector syntax
soup.select("ul.nav > li a")
soup.select_one(".post-title")
```

### Extracting data

```python
tag = soup.find("a")

tag.text          # Inner text (strips HTML)
tag.get_text()    # Same, with optional separator
tag["href"]       # Access an attribute
tag.attrs         # Dict of all attributes
tag.string        # Direct string content (None if nested)
```

### Traversal

```python
tag.parent          # Parent element
tag.children        # Direct children (generator)
tag.descendants     # All nested descendants
tag.next_sibling    # Next sibling element
tag.previous_sibling
tag.find_parent("div")
```

---

## Practical Example — URL Scraper

The file `parse_urls.py` in this session fetched URLs from a data source, iterated over them using `requests`, and parsed each response with BeautifulSoup. The script successfully processed **3,599 URLs**.

```python
import requests
from bs4 import BeautifulSoup

urls = [...]  # your list of URLs

for url in urls:
    try:
        response = requests.get(url, timeout=10)
        soup = BeautifulSoup(response.text, "html.parser")
        title = soup.find("title")
        print(title.text if title else "No title")
    except Exception as e:
        print(f"Error: {e}")
```

> **Tip:** Always set a `timeout` on `requests.get()` to avoid hanging on slow servers.

---

## Common Patterns

### Extract all links from a page

```python
links = [a["href"] for a in soup.find_all("a", href=True)]
```

### Scrape a table into a list of dicts

```python
rows = soup.select("table tbody tr")
data = []
for row in rows:
    cells = [td.get_text(strip=True) for td in row.find_all("td")]
    data.append(cells)
```

### Remove all script and style tags

```python
for tag in soup(["script", "style"]):
    tag.decompose()
clean_text = soup.get_text(separator=" ", strip=True)
```

---

## Key Notes

- `find()` returns `None` if no match — always check before accessing attributes.
- `find_all()` returns an empty list if nothing matches — safe to iterate over.
- BeautifulSoup modifies the parse tree in-place; `decompose()` permanently removes a tag.
- For large-scale scraping, consider `lxml` as your parser for significantly better speed.
- Respect `robots.txt` and rate-limit your requests when scraping public websites.
