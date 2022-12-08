# GitHub Actions: Deploy Jekyll to SFTP with password

This is a guide and tempalte for deploying a Jekyll site to a SFTP server with GitHub Actions. Usually, you can easily deploy your jekyll application in GitHub Pages, but sometimes you need to deploy your site to your own server by FTP/SFTP. This guide will help you to do that.

### GitHub Actions

GitHub Actions is a service that allows you to run your own scripts on GitHub's infrastructure. You can use it to automate tasks like building and testing your code, or deploying your site to a server.

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

© Copyright Max Base, 2022
