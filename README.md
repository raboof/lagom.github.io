# Lagom website

This project is responsible for generating the Lagom website.

The Lagom website is a static website deployed to GitHub pages that is generated from this project.  This project uses twirl for templating, along with sbt-web to process web assets and webjar for dependencies.

The documentation is generated from the lagom main project as unstyled HTML snippets, along with javadocs/scaladocs, and an index file that describes the navigation structure of the documentation.  Each time the website is generated, the documentation pages are recreated from these HTML snippets, allowing the layout and styling to be kept up to date, and allowing links between versions of the documentation.

## Project layout

    - src
      - blog - each markdown file in here gets converted to a blog post
      - docs
        - 1.0.x - the documentation for each version of Lagom
          - index.json - the index for this version of the docs
          - api - api docs
      - main
        - markdown - each file in here gets served as a web page
        - twirl - twirl templates for the documentation
        - assets - compiled assets, eg, stylus stylesheets
        - public - static assets, eg images
        - scala - the Scala source code that actually does the documentation generation

## Developing the website

A static version of the website can be generated by running `sbt webStage`.  This will output the entire website to the `target/web/stage` directory.  You should start a simple HTTP server from here to serve the docs, eg using `python -m SimpleHTTPServer`.  The `sbt ~web-stage` command can be used to tell sbt to watch for changes and rebuild the site whenever anything changes.

For convenience, `sbt run` does the above for you, starting a static Akka HTTP server in the stage directory, and then watching the filesystem for changes and rebuilding the site whenever it changes.

### Troubleshooting

This projects requires Node 12.x (currently the latest LTS) installed. Using newer versions is known to be the cause of issues (see #229). Use `nvm` (https://github.com/nvm-sh/nvm) to make sure what node version you are using:

```
nvm install --lts
```

## Deploying the documentation

A new version of the markdown docs can be generated by running `sbt markdownGenerateAllDocumentation` in the `docs` directory of the main Lagom project, this outputs the documentation to `docs/target/markdown-generated-docs`.  This can be then copied into the corresponding `src/docs/<version>` directory in this project.

A new version of the API docs can be generated by running `sbt unidoc` in the main Lagom project.  This will output the javadocs in `target/javaunidoc`, which can then be copied into the corresponding `src/docs/<version>/api/java` directory in this project.

## Writing blog posts

Lagom uses a format similar to Jekyll for writing blog posts. Blog posts are written in markdown, prefixed with a front matter in YAML format. To create a new blog post, copy the `src/blog/_template.md` file to a new file in the same directory. The name of the file should match the URL that you want it served at, so for example, if you want it to be served at `blog/my-blog-post.html`, it should be in `src/blog/my-blog-post.md`.

Edit the front matter to suit your needs. The `title` and `date` fields are compulsory. The `author_github`, if provided, will be used by the website generator to lookup the author details from GitHub, including the name, URL and Avatar of the author. These can all be overridden, and must be provided manually if no GitHub username is provided, using the `author_name`, `author_url` and `author_avatar` fields.

The `tags` field can be used to specify a space separated list of tags. Multi-word tags can be configured by separating the words with a `+` symbol. The `summary` field can be used to provide a summary, this is what's displayed on the blog index page as well as on the tag index pages, and it will be markdown rendered. Usually it will simply be the first paragraph of the blog post.

The remainder of the file is the blog post. You can preview your blog post by following the [Developing the website](#developing-the-website) instructions above.
