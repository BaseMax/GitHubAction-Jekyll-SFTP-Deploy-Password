# GitHub Actions: Deploy Jekyll to SFTP with password

### GitHub Actions

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
