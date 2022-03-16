# About this repo

## Layout
Please see the Academic documentation (e.g. https://gohugo.io/getting-started/directory-structure/ and https://sourcethemes.com/academic/docs/managing-content/) for an explanation of the repo structure. The generated site is in `public/` which is kept out of the Git repo.

## How to contribute
Create a new branch for your modifications. Please do not work directly on the master branch - it should always generate the latest (non-broken) public website. Make edits on your own branch, then create a pull request & have labmates review your changes.

We may need to use `git lfs` if we have large files e.g. notebooks.

## Setup
from https://github.com/gcushen/hugo-academic#create-your-site-on-your-computer-with-git
- `brew install hugo`
- `git clone --bare https://github.com/sourcethemes/academic-kickstart.git`
- created private repo, `git push --mirror https://github.com/uw-cryo/lab-website.git`
- `git clone https://github.com/uw-cryo/lab-website`
- `git submodule update --init --recursive`
- create new branch with `git checkout -b example-site`
- `cp -av themes/academic/exampleSite/* .`
    - copied the demo content in
- `git add -A`
- `git commit -m "Initialize site with the demo content"`
- `git push --set-upstream origin example-site`
- how to debug locally
    - run `hugo` to generate `public` folder
    - `python -m http.server 1234` from the `lab-website/public` folder
    - in browser, go to `localhost:1234`


### Sections we'd like to add
- Intro
- Posts
- People (just add folders in `authors`)
- Projects ()
- Publications
- Data
- Code
- Fieldwork
- Get involved/DEI/outreach/donate
- Contact (just leaving this for now)
- Funding? (maybe should go in intro)

## Other next steps
- [ ] https://sourcethemes.com/academic/docs/deployment/#github-pages (if we want to use github pages)
- [ ] Try importing your publications with https://github.com/sourcethemes/academic-admin
- [ ] Investigate options for more complicated sections like `Data` (if we want interactive visualizations)


## How to add a new group member bio
1. Starting from the tacolab-website git directory, cd to the authors/ dir  
`cd tacolab-website/content/authors/`

2. Create a folder with your username (can copy from existing user)  
`cp existing_user/ new_user/`
3. Edit the _index.md file (change all the bits to fit you)  
`cd new_user/`  
`nano _index.md`
4. Add a prof pic in your authors/ dir and rename it  
`mv prof_pic.jpg avatar.jpg`
5. Run and refresh the site locally (follow steps 6–8 on [**Website walkthrough**](https://docs.google.com/document/d/1ad_FNJqooKXiRZEbVTm43clrr8kXTE9GVIXrAAnkIi8/edit#)) to see your changes  
6. Then
`git add new_user/`  
`git commit -m “descriptive message”`  
and `git push`
your profile changes up to the main branch (after checking out that it’s what you want)



---
OLD STUFF from HUGO
# Academic Template for [Hugo](https://github.com/gohugoio/hugo)
The Hugo **Academic Resumé Template** empowers you to create your job-winning online resumé and showcase your academic publications.

[Check out the latest demo](https://academic-demo.netlify.app/) of what you'll get in less than 10 minutes, or [view the showcase](https://wowchemy.com/user-stories/).

[**Wowchemy**](https://wowchemy.com) makes it easy to create a beautiful website for free. Edit your site in Markdown, Jupyter, or RStudio (via Blogdown), generate it with Hugo, and deploy with GitHub or Netlify. Customize anything on your site with widgets, themes, and language packs.

- 👉 [**Get Started**](https://wowchemy.com/templates/)
- 📚 [View the **documentation**](https://wowchemy.com/docs/)
- 💬 [Chat with the **Wowchemy community**](https://discord.gg/z8wNYzb) or [**Hugo community**](https://discourse.gohugo.io)
- 🐦 Twitter: [@wowchemy](https://twitter.com/wowchemy) [@GeorgeCushen](https://twitter.com/GeorgeCushen) [#MadeWithWowchemy](https://twitter.com/search?q=(%23MadeWithWowchemy%20OR%20%23MadeWithAcademic)&src=typed_query)
- 💡 [Request a **feature** or report a **bug** for _Wowchemy_](https://github.com/wowchemy/wowchemy-hugo-modules/issues)
- ⬆️ **Updating Wowchemy?** View the [Update Guide](https://wowchemy.com/docs/guide/update/) and [Release Notes](https://wowchemy.com/updates/)

## Crowd-funded open-source software

To help us develop this template and software sustainably under the MIT license, we ask all individuals and businesses that use it to help support its ongoing maintenance and development via sponsorship.

### [❤️ Click here to unlock rewards with sponsorship](https://wowchemy.com/plans/)

## Ecosystem

* **[Hugo Academic CLI](https://github.com/wowchemy/hugo-academic-cli):** Automatically import publications from BibTeX

[![Screenshot](https://raw.githubusercontent.com/wowchemy/wowchemy-hugo-modules/master/academic.png)](https://wowchemy.com)

## Demo image credits

- [Open book](https://unsplash.com/photos/J4kK8b9Fgj8)
- [Course](https://unsplash.com/photos/JKUTrJ4vK00)

[![Analytics](https://ga-beacon.appspot.com/UA-78646709-2/starter-academic/readme?pixel)](https://github.com/igrigorik/ga-beacon)
