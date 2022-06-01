# GitHub Pages

## Create a Repo

1. Create a repo that will be dedicated to site content - compared to the `docs` folder of a repo.
1. Initialize repo with a `README.md` - this becomes the home page.
1. Change branch name from `main` to `gh-pages`. 
1. Clone repo to local for dev.
1. Commit initial content. 

## Repo Settings > Pages

* Branch = `gh-pages`
* Folder = `root`
* Unmodified URL = https://wcdogg.github.io/<repo>/
* Custom domain = www.mydomain.com

## DNS settings

See [Configuring a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

```
CNAME www wcdogg.github.io

A @ 185.199.108.153
A @ 185.199.109.153
A @ 185.199.110.153
A @ 185.199.111.153
```
