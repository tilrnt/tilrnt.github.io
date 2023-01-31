### Today I Learnt repo

Repo of articles with technical content that are not sufficient as blog posts

Intent is to keep a regular log / diary of new things learnt everyday

The generated HTML pages for https://tilrnt.github.io/


### Workflow

* Write and preview posts locally using `jekyll`

* Make PR with changes 

* Review and merge changes

* Triggers workflow which publishes it to github actions

To run jekyll locally:
```
bundle exec jekyll serve --baseurl=
```

To build the site:
```
bundle exec jekyll build --config _config.yml
```

Static html built into `_site` folder.

To trigger the workflow we can either:

* Push to master directly

* Create a blank commit and push:
  ```
  git commit --allow-empty -m "Test blank commit"

  git push origin master
  ```