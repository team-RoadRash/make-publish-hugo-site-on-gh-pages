- Create an organization from your account on Github
- Under the organization make a repository named <organization-name>.github.io (all small caps and empty repo without readme file)
- Go to a desired location in your local system and open git bash there:
  
  git clone https://github.com/<organization-name>/<organization-name>.github.io.git
  cd <organization-name>.github.io.git
  git init
  git checkout -b main
  hugo new site ./ -f --force (TOML file type is default, if using YAML, write -f=yaml)
  git submodule add https://github.com/hugo-toha/toha.git themes/toha  (this is an example theme from hugo templates, can change accordingly)

- Check if your local repo <organization-name>.github.io has a config.toml file or not. If not create one with basic config info:
  
  baseURL = 'https://<organization-name>.github.io'
  languageCode = 'en-us'
  theme = 'toha'
  title = 'Example Website'
 
- After editing the config.toml file, go back to git bash to run:
  
  hugo server -t toha -w (creates a localhost website - use the address that comes up to view if the site is seen as desired)
 
- Update config.toml accordingly to change how the site looks, then follow:
  
  mkdir .github && cd .github
  mkdir workflows && cd workflows
  notepad deploy-site.yml (Opens notepad, save the following content into it, close and return to git bash)
  
    name: Deploy to Github Pages

    # run when a commit is pushed to "source" branch
    on:
      push:
        branches:
        - main
    jobs:
      deploy:
        runs-on: ubuntu-18.04
        steps:
        # checkout to the commit that has been pushed
        - uses: actions/checkout@v3
          with:
            submodules: true  # Fetch Hugo themes (true OR recursive)
            fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod
        # install Hugo
        - name: Setup Hugo
          uses: peaceiris/actions-hugo@v2.5.0
          with:
            hugo-version: 'latest'
            extended: true
        # build website
        - name: Build
          run: hugo --minify
        # push the generated content into the `gh-pages` branch.
        - name: Deploy
          uses: peaceiris/actions-gh-pages@v3.8.0
          with:
            github_token: ${{ secrets.GITHUB_TOKEN }}
            publish_branch: gh-pages
            publish_dir: ./public
  
  (Make sure you are in the root directory)
  git remote -v (should show the fetch and origin, if doesn't show then execute the next command else skip)
  git remote add origin https://github.com/<organization-name>/<organization-name>.github.io.git
  git add .
  git commit -m "Adding all files"
  git push origin main (if you refresh your repo online, should be able to see all the files pushed here)
  git checkout -b gh-pages
  git push --set -upstream origin gh-pages
  git checkout main

- In settings of this repository, under Code and automation section select Pages
- Under build and deployment, under Source, select deploy from a branch
- Select the branch gh-pages
- Click save
- You will observe that github actions display the building and deployment of your website. Each time the files are updated, github actions automatically run deploy the website

- References:
  https://www.youtube.com/watch?v=aqAaYZOqiTw (Easiest way to understand)
  https://www.youtube.com/watch?v=LIFvgrRxdt4 
  https://www.youtube.com/watch?v=Z_7RIuf_Z-Q (Complex video but extensive explanation about each aspect of deployment)
  https://toha-guides.netlify.app/posts/quickstart/
  https://toha-guides.netlify.app/posts/getting-started/prepare-site/
  https://toha-guides.netlify.app/posts/getting-started/github-pages/
