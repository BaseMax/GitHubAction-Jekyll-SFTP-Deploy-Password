# GitHub Actions: Deploy Jekyll to SFTP with password

This is a guide and tempalte for deploying a Jekyll site to a SFTP server with GitHub Actions. Usually, you can easily deploy your jekyll application in GitHub Pages, but sometimes you need to deploy your site to your own server by FTP/SFTP. This guide will help you to do that.

## GitHub Actions

GitHub Actions is a service that allows you to run your own scripts on GitHub's infrastructure. You can use it to automate tasks like building and testing your code, or deploying your site to a server.

GitHub Actions are great to use in projects. Happy coding ^_^

### Example

Create `.github/workflows/deploy.yml` file.

You can see an example workflow in the following:

```yml
on: [push]

jobs:
  mirror_with_sftp:
    name: deploy
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: '2.7.4' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically

      - name: Bundle install
        env:
          RAILS_ENV: test
        run: |
          bundle install --jobs 4 --retry 3

      - name: 🔨 Build Project
        run: |
          bundle exec jekyll build

      - name: List output files
        run: |
          find ./_site/ -print

      - name: FTP Deployer
        uses: sand4rt/ftp-deployer@v1.4
        with:
          sftp: true
          host: ${{ secrets.SERVER_HOST }}
          port: 22
          username: ${{ secrets.SERVER_USERNAME }}
          password: ${{ secrets.SERVER_PASSWORD }}
          remote_folder: ${{ secrets.SERVER_PATH }}
          local_folder: './_site/'
          cleanup: false
          include: '[ "*", "**/*" ]'
          exclude: '[".htaccess", ".github/**", ".git/**", "*.env"]'
          pasive: true
```

Note that you need to add your secrets to the repository settings. You need to define the following secrets:

- SERVER_HOST
- SERVER_USERNAME
- SERVER_PASSWORD
- SERVER_PATH

### Show case

I am using this workflow in my personal website, because I want to deploy my site to my own server by FTP/SFTP. You can see the source code of my website in the following: https://github.com/BaseMax/Max

### Read more

- https://pages.github.com/versions/
- https://www.jessesquires.com/blog/2021/08/23/caching-bundler-on-github-actions/
- https://docs.knapsackpro.com/2021/how-to-load-ruby-gems-from-cache-on-github-actions
- https://github.com/marketplace/actions/jekyll-actions
- https://github.com/actions/setup-ruby
- https://jekyllrb.com/docs/continuous-integration/github-actions/
- https://github.com/marketplace/actions/jekyll-actions

© Copyright Max Base, 2022
