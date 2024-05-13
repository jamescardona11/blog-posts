---
title: "Create a blogFolio with Astro.js for non frontend - Part 1"
date: 2024-5-1
slug: create-a-blogfolio-with-astro-for-non-frontend-part-1
status: draft
summary:  
link: 
tags: [astro, web, frontend]
serie: 
  order: 1
  title: Creating a blogfolio with Astro.js
---

Do you want summary of this post in a couple of lines? Here you go!
We create a template using as inspiration other templates from other content creators, that is the summary use a template if you do know much about frontend, if you want to learn how I did it, and why I choose Astro.js, keep reading.

PD: My template with all references to other creators here: [BlogFolio-astro](https://github.com/jamescardona11/blogfolio-astro)

## Introduction

Why you needs a web?, I mean a blog, or a webpage to show your work, or your thoughts, or your projects; well maybe is a good point to start your journey to improve as developer show others your amazing work, maybe is something that easily help you to get more changes to get a job. Of course if you are here you maybe already know the reasons. The most important is you have a lot options to do this work and you don't need to start from scratch, you can choose a pay-template, free-template or choose pages like ghost, WordPress or Bubble to get this more quickly. But if you are like me, you want to learn how to do it, learn something new and understood maybe new things that help you to solve future problems. In my case I'm not the best frontend developer, mainly my work is focused on Mobile development, but part of my goal is became a better developer.


READ THIS: Here in this post is a quick overview how to build different things and what you need to have in mind if you want to build something similar, in next posts maybe I'm going to cover in more detail specific things.

## Choosing a tech stack

I believe search for free templates on Github is the most easy way to start, first you can decide if you want something more complex or simple and how many time you have in order to modify the template to your needs. Another important is how you want to provide your content, I mean you want to use a CMS? if that is the case you can search for a concept called headless CMS, that is a CMS that provide you the content and you can use it in any way you want, in this case you can use a static site generator, like Gatsby, Next.js, SvelteKit, or Astro.js. 

What are my decisions? I choose Astro.js, why? the short and long answer is because Astro.js is very easy and simple to use for my needs, in the past I used Next.js from a template at the end I spend time to understand many things and Next.js evolve so quickly to follow everything for something simple like a blogfolio. Astro.js is a new project that is very simple to use, and is very easy to understand, and the most important is very fast, and I don't need to spend time to understand many things, and I can focus on my content.

What CMS I choose? I choose **Notion** as CMS. I use Notion on my daily basis, and I know how to use it, but also you can use markdown files to provide your content.

Something else? Yes, I used typescript for 98% of things, and some scripts were write on javascript, and finally I use **Vercel** to deploy my site.

## Create your project

Features: 
- blog (series and tags)
- projects
- about
- resume
- uses

Start with this ```npm create astro@latest``` later we can create a simple structure for this project, I will use the following structure:

TODO IMAGE With STRUCTURE


### Basic Setup
- [tailwindcss](https://docs.astro.build/en/recipes/tailwind-rendered-markdown/#setting-up-tailwindcsstypography) for styling
- [react](https://docs.astro.build/en/guides/integrations-guide/react/) for dynamic components
- [mdx](https://docs.astro.build/en/guides/integrations-guide/mdx/) for markdown
- [vercel](https://docs.astro.build/en/guides/integrations-guide/vercel/) for deployment

```js
import { defineConfig } from 'astro/config';
import mdx from '@astrojs/mdx';
import sitemap from '@astrojs/sitemap';
import tailwind from '@astrojs/tailwind';
import react from '@astrojs/react';
import { siteMetadata } from './src/site-metadata';

import vercel from "@astrojs/vercel/serverless";

// https://astro.build/config
export default defineConfig({
  site: siteMetadata.siteUrl,
  output: 'hybrid',
  integrations: [mdx(), sitemap(), tailwind({
    applyBaseStyles: false
  }), react({
    include: ['**/react/*']
  })],
  adapter: vercel()
});
```


Run again to see the changes and verify that everything is working correctly.

### Content Structure

You two options follow my structure to use Notion or use markdown files.

**How to setup the collections in Astro.js?**

Astro have a concept called [collections](https://docs.astro.build/en/guides/content-collections/), that is a way to group a set of files, in this case we will use it to group our blog posts, projects, and other content.

In our case I'm going to create a couple of them to handle, posts, projects and videos.
This is the basic configuration for our collections:
```js
const posts = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    summary: z.string().optional(),
    date: z.coerce.date(),
    tags: z.array(z.string().optional()).optional(),
    related: z.array(reference('posts')).default([]),
    link: z.string().optional(),
    cover: z.string().optional(),
    serie: z
      .object({
        order: z.number(),
        title: z.string()
      })
      .optional(),
  })
})
```

With this Astro will read the files in the folder and you can use them in your pages.


### Notion Setup



## Add new content


## Conclusion


