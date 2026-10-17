# Henry Tsay's website

Personal website and blog, built with Jekyll and the Academic Pages theme.

## Local development

Use Ruby 3.3 (the GitHub Pages dependencies do not install correctly with Ruby 4).
On macOS with Homebrew:

```sh
brew install ruby@3.3
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
```

Run the export command in each new terminal before working on the site. Then run:

```sh
bundle install
bundle exec jekyll serve
```

Open http://localhost:4000. Restart the server after changing `_config.yml`.

## Editing

- `_pages/about.md`: homepage
- `_posts/`: blog posts
- `_data/navigation.yml`: navigation links
- `_config.yml`: site settings and profile details
- `images/`: images

## Optional sections

The Publications, Talks, Teaching, Portfolio, and CV pages are ready to use but
are omitted from navigation. They remain accessible at their direct URLs.

Add entries to `_publications/`, `_talks/`, `_teaching/`, or `_portfolio/`;
the corresponding listing page updates automatically. Edit `_pages/cv.md`
directly for your CV. The collection folders contain no sample entries.

When a section is ready, add a link under `main` in `_data/navigation.yml`:

```yaml
  - title: "Publications"
    url: /publications/
```

Other section URLs are `/talks/`, `/teaching/`, `/portfolio/`, and `/cv/`.

The site is hosted at https://henrytsay.github.io.

Theme attribution and licensing are preserved in `LICENSE` and the site footer.
