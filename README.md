[README (1).md](https://github.com/user-attachments/files/34172466/README.1.md)
# Aboutnet Plugin Usage Scanner 3.1

Shows **which installed plugins are actually used**: which pages their elements are on, and what they do on the site
(SEO, cache, email, login, URLs, data), so unused plugins can be removed safely. It runs only when you click
**Scan site**, only in wp-admin, and changes nothing on the site.

## Installation

Plugins → Add New → Upload Plugin → `plugin-usage-scanner.zip`. If version 2.x is already installed, WordPress offers
to **Replace** it. If the old copy is in a different folder and stays active, the new version switches it off
automatically and shows a notice asking you to delete it.

## Usage

1. **Tools → Plugin Usage Scanner → Scan site.** First, your browser opens a few pages of the site as a visitor (the home
   page, the page that uses the most plugins, and the latest page / post / product) and measures what each plugin adds.
   Because this happens in your browser, it also works before the nameservers are switched (with a hosts file entry).
   On large sites the scan runs in steps of about 12 seconds; keep the tab open.
2. For each plugin you see a result, what it adds, and under **Details** every page, template and widget area where it
   was found, with its position in the Elementor layout (e.g. `Container › eael-adv-accordion “FAQ”`).
3. The same information appears as a **Usage** column on the Plugins page, and as a **Plugins used** column with a
   filter on Pages and Posts.
4. The **Custom search** tab is the older preset / pattern search (fixed), for inactive plugins or for data in settings
   and post meta.

| Result | Meaning |
|---|---|
| In use — keep | Its elements are on a published page, a template that is shown, or a widget area |
| Working on the site — keep | It is not (or does not need to be) on pages, but it clearly does something: adds code to the `<head>` or to pages, has moved the login page, sends email via SMTP, converts URLs to Greeklish, page cache is on, it has redirects / SEO fields / translations, or another plugin or the theme needs it |
| Only in unpublished places | Only in drafts, private pages, or templates that are not shown anywhere |
| Background only — check | Only background activity was found (hooks, scheduled tasks, its own data). Decide based on what the plugin does |
| No sign of use — likely removable | Nothing it adds is used, and no activity of it was found |
| Inactive | Not running; if it shows “leftovers” or “data”, its elements or database tables are still on the site |

Each plugin’s **Details** include a **What it does on the site** section that lists every piece of evidence separately
(“Clear sign of use” or “Background”), e.g. “Adds 2 KB of code to the page `<head>`”, “SEO title… set on 37 posts”,
“Sends the site’s email through SMTP (smtp.example.gr)”.

**Needed by** means another active plugin depends on it (e.g. Elementor for its add-ons).
The **Elements no active plugin provides** section lists leftovers of deleted or inactive plugins, and
**Database tables no installed plugin explains** lists database tables most likely left behind by deleted plugins.

## How it works out which plugin owns what

It asks WordPress itself which file registers each shortcode, Elementor widget, dynamic tag, block, widget and post
type, and maps that file to its plugin folder. This covers every plugin, not only those with a preset.

To find out what each plugin does:

- **Front end:** the pages are opened with a one-time token that is valid for 30 minutes. Only on those requests does
  it measure each plugin’s output in the `<head>`, in the body / footer and in the page content, plus the CSS/JS files
  it loads. The page visitors see is not changed.
- **Behaviour:** it tests the login address, the conversion of Greek titles to URLs and the email settings, and checks
  drop-ins (cache), scheduled tasks and plugins the theme requires.
- **Data:** each plugin’s database tables, post meta and settings, matched by name, plus specific checks for Yoast,
  Rank Math, Redirection, LiteSpeed, Loco Translate, TranslatePress, ACF/SCF, Wordfence, UpdraftPlus, Akismet and
  WooCommerce.

If your browser cannot open the pages, it tries again from the server. If that also fails, a notice at the top says so.

## Before deleting a plugin

Backup → **deactivate** (do not delete yet) → clear caches → check the pages it lists → delete.

## Limitations

It cannot see: things that happen only on other pages or only occasionally (e.g. a redirect on a single URL, an email
sent at checkout), shortcodes called from the theme’s PHP files, or menus. Block theme templates count as live when
they are published. *Background only* does not mean “unnecessary”: it means a person has to decide.

## Changelog

**3.1.0**
- New: “What it does on the site” for every plugin, beyond page elements. New results *Working on the site — keep* and
  *Background only — check*. *Not found — likely removable* became *No sign of use — likely removable* and appears only
  when nothing at all is found (e.g. Yoast is no longer marked removable just because its breadcrumbs block is unused).
- New: front-end check from the admin’s browser, which also works before a DNS switch. It replaces the server-side
  request, which is kept as a fallback.
- New: list of database tables that no installed plugin explains.
- Fix: false “Needed by”. The “WC requires at least” / “Elementor tested up to” headers declare compatibility, not a
  dependency. WordPress widgets that Elementor wraps no longer count as the plugin’s own Elementor widgets (e.g.
  WooCommerce no longer “needs” Elementor).
- Fix: the plugin header version matches the code.
- Docs: README and code comments in English.

**3.0.0**
- New *Plugin inventory* tab: checks every plugin automatically, with pages, templates, widget areas and post types.
- Columns on the Plugins and Pages / Posts screens, with “Uses: …” and “Missing elements” filters.
- Fix: the Elementor widget-level check never ran (it started from the list of elements and never went inside them).
- Fix: Elementor’s JSON broke on every page with quotes or HTML (unnecessary `wp_unslash`).
- Fix: snippets with Greek text showed up empty (UTF-8 cut in the middle of a character).
- Fix: usage through saved templates (Template widget, `[elementor-template]`, reusable blocks) and display conditions.
- Fix: pages switched back from Elementor to the WordPress editor no longer count as usage.
- Fix: add-on extensions (e.g. `ha_floating_fx`, `eael_particle_switch`) are detected.
- Fix: the 500-row limit was replaced by a batched scan; when results are cut short, it says so.
- Fix: Custom search keeps the patterns you typed after searching.
- Removed “Edit with Elementor” links on non-Elementor content and the link to `options.php`.

## Testing

WordPress 7.1.3 and 6.2, PHP 8.3 (static compatibility check for 7.4+), MariaDB 10.11, Elementor 4.0, Essential
Addons, Contact Form 7. On a site with 20,000 posts and 300 Elementor pages (~400 KB of layout each), the full scan
including the new checks took 3 s with 12 MB peak memory. Front-end check tested with a hostname that only the browser
can resolve (the hosts file scenario), with and without JavaScript. A scan split into many steps gives exactly the same
result as a single-step scan. No PHP warnings or notices.

Requirements: WordPress 6.0+, PHP 7.4+. Uninstalling the plugin deletes the stored results.
