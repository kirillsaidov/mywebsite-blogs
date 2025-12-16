# My Experience Building a Website with the D Language

I recently finished building my personal [website](https://kirillsaidov.com/), and I wanted to share the experience of developing it with the [D programming language](https://dlang.org/) and the [Vibe-d](https://github.com/vibe-d/vibe.d) framework. This was both a practical project and a way to explore web development in a language I've come to appreciate deeply.

Before we continue, I'd like to mention that I have no prior experience in web development. I mostly do AI, familiar with FastAPI, but I have never participated in building a fully fledged backend for any website, nor have I written any code for frontend.

## Why D?

When choosing a language for this project, I had several options. I could have gone with Node.js, Python with Flask or Django, or Go. I know one could also use Rust for the same purpose, but I strongly dislike its syntax. Since this is a personal project with a lot of freedom, I chose D.

The reasons are simple:

**Balance.** D strikes a balance that few languages achieve. It has clean, readable syntax with a script-like feeling reminiscent of Python, but with the performance characteristics of systems languages like C/C++. You can write high-level code that reads almost like pseudocode, then drop down to pointer manipulation and manual memory management when you need that extra bit of control.

**Performance.** While a personal website doesn't exactly need blazing speed, I still appreciate knowing my stack is fast with minimum effort. D compiles to native code and gives me the performance headroom to add features without worrying about hitting walls. I'll start worrying once I hit a bottleneck.

**I want to help popularize D.** The D community is smaller than I think it deserves to be. By building real projects with D and writing about them, I hope to show others that it's a viable choice for web development - not just systems programming. Well, I'm trying out the user experience either way... was it pleasant, easy, and simple? Read on.

**I genuinely like the language.** That's it. There is nothing more to say, really.

## The Architecture

My website uses a straightforward stack:

### Backend: D with Vibe-d

[Vibe-d](https://github.com/vibe-d/vibe.d) is D's framework for simplifying web development, very similar to FastAPI. It's built on fibers for asynchronous I/O, which means it handles concurrent connections efficiently without callback hell. The framework provides everything you need: routing, templating, and database connectivity.

The REST interface feature is particularly elegant. You define an interface in D, implement it, and Vibe-d automatically generates the API endpoints:

```d
@rootPathFromName
interface BlogAPI
{
    // GET /blog_api/posts/metadata - Get all blog metadata
    @path("/posts")
    @method(HTTPMethod.GET)
    BlogMetadata[] getMetadata();
}

class BlogImpl : BlogAPI
{
    override BlogMetadata[] getMetadata()
    {
        // ...
    }
    // ...
}

// Register in router
router.registerRestInterface(new BlogImpl);
```

### Database: MongoDB

MongoDB handles all persistent data - blog posts, metadata, and configuration. Vibe-d has solid MongoDB support through its [`vibe-d:mongo`](https://code.dlang.org/packages/vibe-d%3Amongodb) package, and document storage fits well with blog content that includes variable-length markdown.

### Frontend: Diet Templates

Diet is Vibe-d's templating engine, syntactically similar to [Pug](https://github.com/pugjs/pug) (formerly Jade) for Node.js. Templates compile to D code at build time, so there's no runtime parsing overhead. I used Claude AI to help me generate the frontend, since I have no prior knowledge in working with frontend stuff. It works surprisingly well for these types of tasks.

The syntax is clean and indentation-based:
```html
extends base/layout

block content
    section.hero
        h1 #{post.title}
        .tags
            - foreach(tag; post.tags)
                span.badge #{tag}
        article
            | !{post.content}
```

For styling, I went with Bootstrap 5 and added a dark/light theme toggle.

## Project Structure

I organized the codebase into logical modules:

```bash
source/
├── api/            # REST API implementation
│   ├── ...
│   └── package.d   # API module exports
├── routes/         # Routes serving diet templates
│   ├── ...
│   └── package.d   # Route module exports
├── app.d           # Application entry point
├── config.d        # Configuration loading
├── ...             # Other modules
└── db.d            # Database connection handling

views/
├── base/
│   ├── layout.dt   # Base template
│   ├── styles.dt   # CSS includes
│   └── js.dt       # JavaScript includes
├── blog.dt         # Blog page
├── blog_post.dt    # Individual post page
├── cv.dt           # CV page
└── index.dt        # Homepage
```

This separation keeps code isolated: routing, data operations, API, frontend templates.

## Challenges

Building with D wasn't without friction. The ecosystem is smaller than mainstream languages, and that shows in a few ways.

### Lack of Tutorials

Finding tutorials for Vibe-d web development is difficult. The official documentation exists but tends toward reference material rather than guides. Most of what I learned came from reading source code - both Vibe-d's examples and other open-source D projects. This is not ideal for newcomers or people who generally want to build something quickly.

I was able to find this [tutorial](https://reyvaleza.gitbook.io/vibe.d-tutorial) by Reyvaleza. A very good introduction to building websites with Vibe-d. I strongly recommend it for anyone starting out.

The reason I was able to find it is because I sometimes read the D [forum](https://forum.dlang.org). I doubt I would have found it otherwise, unless I asked a question, but most users probably won't. If they cannot find what they are looking for, they are likely going to move on. To solve this, maybe there should be a list of "highlighted" or "approved" packages grouped into sections on the D package [registry](https://code.dlang.org/). Whenever I want to find a package, the first thing I do is go to the package registry.

If you're used to the endless tutorials and Stack Overflow answers available for Node.js or Django, D will feel sparse. You need to be comfortable reading documentation and experimenting. AI/LLMs may answer many of your questions about D, but the produced code probably won't compile. 

### Finding Tools and Bindings

The D package [registry](https://code.dlang.org/) has many packages available, but not all of them are maintained. I spent several evenings going through packages that looked promising, only to find they didn't compile.

P.S.: There are many dead projects there. Even empty projects are added. The registry should restrict people from adding empty projects. I think this should be monitored somehow. // just a suggestion.

The MongoDB situation is a good example. Searching for `mongodb` turns up multiple packages. Which one is good? Which one works? It's not obvious. After trying a few dead ends, I found that [`vibe-d:mongo`](https://code.dlang.org/packages/vibe-d%3Amongodb) (part of the Vibe-d framework itself) is the right choice. But subpackages of a package are not shown in search results - I had no idea it existed. Discovering this required digging; it's not the kind of thing you find with a quick search.

## The Good: Community

The D community is very welcoming. If you have a question, they will surely help you, and response time is fast. So be brave, ask questions if you are new to the language.

## Verdict

I still like the D programming language :)

## What's Next

The current site handles my portfolio, blog, and CV. I'm planning to add a photo gallery as a separate website - keeping the main site lean while letting the photo stock scale independently. I want to move from [Instagram](https://www.instagram.com/kirill.saidov) to my personal photo gallery where users will be able to download and buy photos in high quality.

If you're curious about D for web development, I encourage you to try it. The ecosystem isn't as polished as mainstream options, but the language itself is a pleasure to work with. And maybe, if more people build with D, the ecosystem will grow.

---

*This site is built with D, Vibe-d, MongoDB, and Diet templates. Source code and more details available on [GitHub](https://github.com/kirillsaidov/mywebsite).*



