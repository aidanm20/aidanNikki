---
title: Building My Portfolio Site with Astro & A Deep Dive into SSG, SSR, and CSR
slug: building-with-astro
publishDate: 2026-05-23
description: An introduction to this site, why I chose Astro, and a breakdown of Static Site Generation, Server Side Rendering, and Client Side Rendering.
---
 
## Building My Portfolio Site with Astro
 
Hello, nice to meet you! This is my first post on my developer journal Aidan Nikki. I'm a CS student based in Los Angeles, heading into my second in the fall. My main interest within the field is web development, and I wanted a space to document the projects I build, the challenges I face, and my thought processes.
 
The name *Aidan Nikki* comes from 日記 (*nikki*), the Japanese word for diary. I'm half Japanese and grew up in Japan, so I thought I'd use a Japanese word. Still not sure if I like the name so it may change in the future but it's the best I could come up with for now. 
 
For my first post, I wanted to document the process of building this site. I'll dive into my stack and what advantages and disadvantages it gives me, and also into different rendering methods. 
 
---

## Is this post for you?

Now this post will cover a lot of Astro basics so if you are interested in Astro but don't know anything about it? Or maybe you are also thinking of making a content driven site such as a blog or a landing page. Or maybe you're just interested in me for whatever reason. If so, this article is perfect for you. 
 
## Why Astro?
 
For this blog site, I wanted to build a site that was fast, SEO friendly, and easy to maintain. After some research, I landed on [Astro](https://astro.build/), a relatively modern framework with some compelling advantages for content driven sites (such as a blog!):
 
1. **Performance** — Astro ships minimal JavaScript by default, resulting in faster load times.
2. **SEO** — It generates clean, crawlable HTML rather than relying on the browser to render content.
3. **Flexibility** — It supports component libraries like React when interactivity is needed.
---
 
## SSG, CSR, and SSR — What's the Difference?
 
One of the reasons Astro appealed to me is its use of **Static Site Generation (SSG)**, which is worth contrasting with the other two dominant rendering approaches.
 
### Static Site Generation (SSG)
 
With SSG, pages are pre built at compile time. When you deploy to a host like Vercel or Netlify, the HTML is generated once during the build step. By the time a visitor loads the page, the browser simply downloads already-rendered HTML. This makes SSG exceptionally fast and easy for search engine crawlers to index.
 
SSG is best suited for content that doesn't change frequently, including blogs, documentation, and landing pages.
 
### Client Side Rendering (CSR)
 
CSR is the approach used by standard React applications. The browser receives a minimal HTML shell (typically just a `<div id="root"></div>`) and then executes JavaScript to build out the page dynamically. This is powerful for web apps with heavy interactivity or real time data. Examples include tools like Figma or dashboards, but it comes with slower initial load times and weaker SEO, since crawlers often can't execute JavaScript to see the full content. A smart tactic is to use a SEO friendly landing page that links to the CSR web app!
 
### Server Side Rendering (SSR)
 
SSR sits between the two. Pages are rendered on the server at request time and sent to the browser as HTML. It is similar to SSG in structure, but generated on demand rather than at build time. This makes it a good fit for sites that need both SEO friendliness and dynamic content, such as e-commerce platforms or social media feeds. Next.js is a common framework associated with this approach. It is important to note that SSR performance and SEO heavily depend on how it is executed. 
 
### When to Use Each (TLDR)
 
| | Best For | Examples |
|----------|----------|---------|
| SSG | Static, content driven sites | Blogs, portfolios, documentation |
| CSR | Highly interactive web apps | Dashboards, SaaS tools |
| SSR | Dynamic content requiring SEO | E-commerce, social platforms |
 
---
 
## Interactivity in Astro — Island Architecture
 
One concern developers often have with SSG is the lack of interactivity. Astro addresses this through a concept called **Island Architecture**. You can designate specific components as interactive "islands". Astro will hydrate just those components with JavaScript while leaving the rest of the page as static HTML.
 
This means you get the performance benefits of SSG without sacrificing the ability to build dynamic UI components where needed. The [official Astro documentation on Islands](https://docs.astro.build/en/concepts/islands/) explains this concept really really well! Please read if you want to learn more.
 
---
 
## Now actually getting into how the site works but also getting sidetracked by explaining frontmatter
 
Each post on this site is a Markdown file stored in a `data/` folder. At the top of every file is a **frontmatter (`---`)** block. The frontmatter in markdown files should not be confused with the frontmatter in Astro files. While in Astro it is where you put your JS or TS stuff in before your HTML (kind of like the JS you put before the return in React JSX), in markdown it is a section that contains metadata Astro uses to generate the page like this:
 
```
---
title:  
slug:  
publishDate:  
description:  
---
```
 
I'll also put an example of a basic Astro component so you can see the difference. You can use curly braces to use your frontmatter variables in the html. 
 
```html
---
let greeting = 'Hello, world';
---
 
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>My Page</title>
  </head>
  <body>
    <p>{greeting}</p>  #prints Hello, world
  </body>
</html>
```
 
---
 
## Dynamic Routing with `[slug].astro`
 
Now time to get to the fun stuff! This site uses a dynamic route to render each post from a single template file. In Astro's file based routing system, a filename wrapped in square brackets (like `[slug].astro` in this case) indicates a dynamic route parameter. When a visitor navigates to `/blog/building-with-astro`, Astro matches that path to the `[slug].astro` template and passes `"building-with-astro"` as the `slug` parameter. So replacing the slug keyword with the file name essentially.
 
(The term "slug" comes from print journalism, where it referred to a short identifying label given to an article. Somehow it became kind of a popular keyword)
 
To populate these routes, Astro uses a `getStaticPaths()` function that reads all the Markdown files and tells the framework which slugs to generate pages for:
 
```javascript
export async function getStaticPaths() {
  const posts = await Astro.glob('../data/*.md');
  return posts.map(post => ({
    params: { slug: post.frontmatter.slug },
    props: { post },
  }));
}
 
const { post } = Astro.props;
```
 
This function runs at build time. Astro iterates through every Markdown file, extracts the `slug` from the frontmatter, and generates a corresponding static HTML page for each one. The result is a fully pre rendered blog with clean URLs and no server required at runtime.
 
---
 
That covers the core technical decisions behind this site. It ended up kind of becoming a basic Astro tutorial 😅 I'll probably make a part 2 for other aspects of the site like styling. I kind of started writing without planning the structure out for this one so yeah I'll do that next time.
 
If you have questions or feedback, or want to talk about anything, work on a project, please please please feel free to reach out. 