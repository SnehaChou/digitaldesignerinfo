# digitaldesignerinfo

A private-first Hugo moodboard archive for simple folders of favorite stores, people, designers, artists, makers, links, photos, notes, and tags.

## Run locally

```sh
hugo server -D
```

Then open the local URL Hugo prints in the terminal.

## Content model

The site is organized as simple folders.

Collections are folders with an `_index.md` file:

```text
content/stores/objects/home/indian/_index.md
```

Entries are one file each inside that collection:

```text
content/stores/objects/home/indian/heyhouse.md
content/stores/objects/home/indian/somedesignhouse.md
```

The website displays that folder as `Stores / Objects / Home / Indian`.

## Add a new entry

Copy an existing entry file, rename it, and edit fields like:

```yaml
title: "Hey House"
description: "Why I saved this source."
photo: "images/samples/furniture-store-shelf-details.jpg"
instagram: "https://www.instagram.com/"
website: "https://example.com/"
location: "Online"
tags: ["objects", "decor", "home"]
```

Photos live in `static/images`.

## Add a new collection

Create a folder and add `_index.md`:

```text
content/stores/objects/ceramics/_index.md
```

Set `collection: true` in `_index.md` so it appears as a collection card.

## Current structure

```text
stores/
  jewelry/
  clothes/
  kids/
  furniture/
    indian/
    international/
  objects/
    art/
      indian/
      international/
    home/
      indian/
      international/
    textile/
      indian/
      international/
    lighting/
      indian/
      international/

people/
  artist/
    indian/
    international/
  interior-designer/
    indian/
    international/
  publication-inspiration/
    international/
```

## Privacy note

This is set up as a local/private archive first. Before publishing to GitHub Pages, replace sample images with images you own, have permission to use, or can properly credit and link back to.
