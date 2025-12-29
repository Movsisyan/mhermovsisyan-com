# mhermovsisyan.com

Personal portfolio website built with [Astro](https://astro.build) and deployed on Firebase Hosting.

## Features

- **Dark Premium Theme** - Glassmorphism design with gradient accents
- **Responsive** - Mobile-first design that works on all devices
- **Blog/Articles** - Markdown-based content with category filtering
- **SEO Optimized** - Meta tags, semantic HTML, fast load times
- **Firebase Ready** - Configured for Firebase Hosting deployment

## Tech Stack

- **Framework**: Astro 5
- **Styling**: Vanilla CSS with CSS Variables
- **Hosting**: Firebase
- **Contact Form**: Formspree

## Project Structure

```
src/
├── components/     # Reusable UI components
├── layouts/        # Page layouts
├── pages/          # Route-based pages
├── content/        # Markdown articles
└── styles/         # Global CSS
```

## Commands

| Command | Action |
|---------|--------|
| `npm install` | Install dependencies |
| `npm run dev` | Start dev server at `localhost:4321` |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build |

## Deployment

```bash
npm run build
firebase login
firebase deploy --only hosting
```

## Adding Articles

Create a new `.md` file in `src/content/articles/`:

```markdown
---
title: "Article Title"
description: "Brief description"
pubDate: 2024-12-28
category: "iOS"  # Data Structures, Algorithms, Common, iOS, AI
tags: ["swift", "swiftui"]
---

Your markdown content here...
```

## License

MIT
