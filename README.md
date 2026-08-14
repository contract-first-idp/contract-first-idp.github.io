# Contract-First IDP GitHub Pages

[view site](https://contract-first-idp.github.io/)

## Run locally

```
podman run --rm -it   -p 4000:4000   -p 35729:35729   -v "$PWD":/site:Z   -w /site   docker.io/library/ruby:3.1   bash -lc '
    gem install bundler &&
    bundle install &&
    bundle exec jekyll serve \
      --host 0.0.0.0 \
      --livereload \
      --livereload-port 35729
  '
```
