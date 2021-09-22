# mytraffic tech & product blog

## Contributing

### Writing your content

To write a new post you need to do the following:

- create your content as a markdown file in the `_posts` directory
- edit the post's metadata (add a main image for your post, you can find great ones on [unsplash.com](https://unsplash.com))
- add your post's images to the `assets/images` directory
- add your author profile to the `_config.yml` file if it is not there yet

If you need to customize the layout, you can modify the html templates.
The site build process uses the well maintained [jekyll](https://jekyllrb.com/docs/).
You can refer to the official documentation should you need any customization.


### Render the site locally

You can run the site locally by doing `docker compose up`.
It will automatically rebuild the site if you modify some posts or layout.
However it does not rebuild when you change the `_config.yml` file.
You can restart the docker in this case.


### Deploying

When you push to the `main` branch, the site is automatically deployed by [github pages](https://pages.github.com/), which knows how to deal with jekyll static sites.
The articles are then manually integrated on the www.mytraffic.io site (inside an iframe :/). For this you need to send the page url to Alexia so that she can create a new blog entry in prismic.


### Useful resources

- [jekyll](https://jekyllrb.com/docs/)
- The repository of the [original theme](https://github.com/wowthemesnet/mediumish-theme-jekyll)
- You can find great illustrations for your posts on [Unsplash](https://unsplash.com/)
- If needed you can build an avatar on [avatarmaker](https://avatarmaker.com/)
