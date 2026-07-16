---
description: Download all references from a file or pasted list — PDFs as-is, blog posts as clean markdown.
---

If `$ARGUMENTS` is a file path, read it. Otherwise treat the input as a pasted list. Each line has a title and a URL. For each, determine the resource type:

- **PDF** — the URL ends in `.pdf`. Download directly to `<download-dir>/<YEAR>-<AUTHOR>-<TITLE>.pdf`.
- **Blog / article** — any other URL. Download the HTML to `/tmp/<slug>.html`, then convert to markdown, then **strip site chrome**: remove headers, footers, sidebars, sponsor blocks, cookie notices, and any navigation. Keep only the content body and the original link of the reference. Save as `<download-dir>/<slug>.md`. Verify the resulting markdown has actual substantive content (not just a book reference, purchase link, abstract, or placeholder). If it does not, delete the file and follow **last resort** / **not found** instructions instead.

Use `curl -Lso` for downloads, and `pandoc -f html -t markdown --wrap=none` for conversion.

Create `<download-dir>/` if it doesn't exist. Skip a reference if its target file already exists.

PDFs are never converted — download raw only. Only HTML pages (blogs/articles) are converted to markdown.

**Last resort** — for any reference where you still cannot find a suitable download with actual content, search the web for `<author> <title> ext:pdf`. If there are several options, number them in the file name (e.g. `<YEAR>-<AUTHOR>-<TITLE>-1.pdf`, `<YEAR>-<AUTHOR>-<TITLE>-2.pdf`). List the files with several options so the user can review and remove what is not adequate — just download; only the user has the capability to decide adequacy.

**If still could not find** — list those references explicitly and mark them as **not found**.

After every reference is processed, list the contents of `<download-dir>/` so the user can verify.
