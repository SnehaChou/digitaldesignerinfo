---
name: add-design-entry
description: Use when adding a new entry to the digitaldesignerinfo Hugo archive from a website or Instagram link. The skill inspects safe public sources first, uses Instagram only when needed, discovers photos with the same safe-first order when allowed, asks only for missing details, chooses the best stores/people category folders, duplicates the entry across multiple relevant folders when appropriate, writes Markdown entry files, and validates the Hugo build.
---

# Add Design Entry

Use this skill to add one design source, store, artist, designer, or studio to the `digitaldesignerinfo` Hugo archive.

## Inputs

The user should provide at least one:

- website URL
- Instagram URL
- name plus enough detail to identify the source

If only a URL is provided, inspect the page when possible. If network/browser access is unavailable, ask for the missing details instead of guessing.

For batch imports, do not silently choose speed over quality. Ask whether the user wants:

- enriched entries, where websites/Instagram pages are inspected in batches and descriptions/notes are filled from real source material, or
- quick entries, where only the provided spreadsheet/list fields are imported.

## Source Enrichment Order

Use a safe-first source order:

1. Prefer the official website when one is provided or easily discoverable.
2. Use public, non-login web sources only when the official website is missing, blocked, too thin, or clearly not the right source. Favor the source's own pages, reputable publication profiles, portfolio pages, or marketplace/profile pages over generic search snippets.
3. Use Instagram only when it is the only meaningful source, or when the website/public source does not provide the details needed for name casing, bio, location, category, current work, or useful notes.

When using Instagram:

- Use Chrome only if the user has explicitly allowed Chrome/Instagram access for this work and is already logged in. Never ask for, handle, store, or submit the user's Instagram password, OTP, or recovery information.
- Treat Instagram as read-only. Do not follow, unfollow, like, comment, message, save posts, change account settings, open private messages, or submit forms.
- Extract only archive-relevant public/profile-visible details such as display name, username, bio, website link, category label, location if visible, highlight names, visible recent post themes, credited designers/stores, and useful tags.
- Do not download or reuse Instagram images unless the user explicitly asks and rights/credit handling is clear.
- If Instagram is private, blocked, rate-limited, asks for CAPTCHA, asks for extra verification, or exposes only logged-out/limited content, stop and leave a conservative note instead of forcing access.
- Keep notes factual and source-grounded. For Instagram-only entries, say what was visible from Instagram and avoid pretending it came from a full website review.

## Entry Model

Each entry is one Markdown file inside one or more collection folders:

```text
content/stores/objects/home/indian/heyhouse.md
content/people/interior-designer/indian/studio-mumbai.md
```

Each entry file should use this front matter:

```yaml
---
title: "Name"
description: "Short reason this belongs in the archive."
photo: "images/..."
photo_source: "https://..."
photo_credit: "Source or photographer, if known."
instagram: "https://www.instagram.com/..."
website: "https://..."
location: "India"
tags: ["tag one", "tag two"]
---

Optional notes go here.
```

Omit optional fields when unknown. Do not invent URLs, locations, photos, tags, descriptions, or body notes beyond reasonable category tags.

## Photo Discovery Order

Only fetch or attach photos when the user has asked for photos, images, or visual enrichment. Use the same safe-first source order:

1. Prefer the official website:
   - First use an explicit press/media/profile image, logo, Open Graph image, hero image, project thumbnail, product image, or portfolio image that represents the source well.
   - Prefer images with visible attribution, a clear source URL, or obvious ownership by the source.
2. Use public, non-login web sources only if the official website has no usable image or is blocked/thin.
3. Use Instagram only if the website/public source has no usable image and Instagram is the only meaningful visual source.
   - Prefer the profile photo/avatar over post images unless the user explicitly asks for post/project images.
   - Do not open private content, stories, DMs, saved posts, or anything requiring extra verification.

When saving a photo:

- Copy it into `static/images/sources/<section-or-category>/<slug>.<ext>` when possible, then set `photo: "images/sources/<section-or-category>/<slug>.<ext>"`.
- Add `photo_source` with the page URL or image source URL.
- Add `photo_credit` when the photographer, publication, brand, or source is visible. If no credit is visible, use a conservative value such as `Instagram profile photo` or `Official website`.
- Do not hotlink remote images in `photo`; local files are preferred so the Hugo site stays stable.
- Do not use images from Instagram posts, publication articles, or third-party sites when rights/credit are unclear unless the user explicitly says this private archive can use them as reference placeholders.
- If no safe visual is available, omit `photo` and mention that no photo was added.

## Workflow

1. Discover the current collection folders from the live repo:

   ```sh
   rg -l "collection: true" content/stores content/people
   ```

   Treat each matching `_index.md` parent directory as an allowed destination. Do not rely on a static category reference.
2. Inspect sources using the Source Enrichment Order above. For more than 10 rows, ask whether to enrich all rows, enrich a sample/batch, or import provided fields only.
3. Extract or ask for:
   - name
   - short description or why the user likes it
   - website URL
   - Instagram URL
   - location or region: Indian, International, local, online, unknown
   - best category or categories
   - photo path, if the user has one ready, or a discovered photo using the Photo Discovery Order when the user asked for images
   - tags
4. Normalize the display name before writing:
   - Convert rough imported names such as `june walk design` to title case: `June Walk Design`.
   - Preserve known acronyms and initialisms such as `LA`, `NYC`, `UK`, `PCD`, and `KLD`.
   - Preserve brand-preferred casing when the website or Instagram clearly uses a different style.
5. Ask concise follow-up questions only for details that materially affect placement or entry quality.
6. Choose one or more collection folders. Duplication is allowed when a source naturally belongs in multiple categories.
7. Create a slug from the normalized title:
   - lowercase
   - replace spaces and punctuation with hyphens
   - keep ASCII letters, digits, and hyphens
   - avoid duplicate filenames by adding a short suffix if needed
8. Write one Markdown entry file per chosen collection. Leave the body empty unless you have real notes from the user or real source material. Never add placeholder body text like “add notes later.”
9. Run a Hugo build with `--noBuildLock` and a temporary destination to validate without leaving generated files in the repo:

```sh
hugo --minify --noBuildLock --destination /tmp/digitaldesignerinfo-build
```

## Placement Rules

- Use `stores/...` when the source primarily sells products or objects.
- Use `people/...` when the source is a person, studio, artist, designer, or design practice.
- If a design house is both a store and a studio, add duplicate entries in both relevant `stores/...` and `people/...` folders.
- Choose `indian` when the source is based in India, strongly Indian in practice, or the user explicitly wants it under Indian.
- Choose `international` when the source is outside India or region is unclear but not obviously Indian.
- If uncertain between two valid categories, ask. If the user does not answer and one placement is clearly safer, proceed and mention the assumption.

Use folder names as category hints:

- `jewelry`: jewelry, wearable metalwork, accessories.
- `clothes`: clothing, fashion labels, wearable textile references.
- `kids`: kids decor, nursery sources, children’s furniture, toys, bedding.
- `furniture`: chairs, tables, sofas, cabinets, beds, shelves, built pieces.
- `objects/art`: paintings, sculpture, prints, wall art, decorative art objects.
- `objects/home`: decor, trays, mirrors, planters, ceramics, tabletop, small home goods.
- `objects/lighting`: lamps, pendants, sconces, lanterns, lighting fixtures.
- `objects/textile`: rugs, cushions, fabric, upholstery, throws, tapestries, textile art.
- `artist`: artist, textile artist, visual artist, printmaker, sculptor, craft artist.
- `interior-designer`: interior designer, architecture/interiors studio, styling studio, design practice.
- `publication-inspiration`: magazine, publication, design directory, inspiration feed, non-designer reference account.

## Photo Handling

- Prefer user-provided local image paths copied into `static/images/...`.
- If the user asks to discover photos, follow the Photo Discovery Order above.
- If there is no photo yet, omit `photo` or use an existing placeholder only if the user wants placeholders.
- Do not download or reuse images from websites or Instagram unless the user explicitly asks and rights/credit handling is clear.

## Final Response

Keep the response short:

- list the file(s) created
- mention the chosen category or categories
- mention the build result
- note any fields left blank
