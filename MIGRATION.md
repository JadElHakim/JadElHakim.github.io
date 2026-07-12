# Private Jekyll source migration

This branch prepares `JadElHakim/JadElHakim.github.io` to become a public deployment-only repository.

## Target architecture

```text
Private source repository
  - Jekyll source
  - posts, plugins, config, Gemfile
  - builds `_site`
  - publishes generated output into `site/` in this repository

JadElHakim/JadElHakim.github.io
  - remains public
  - contains generated static output only
  - deploys `site/` to GitHub Pages
  - keeps https://jadelhakim.github.io unchanged
```

## Cutover sequence

1. Create or expose a private source repository to the connector.
2. Copy the current Jekyll source and history into the private repository.
3. Add a build-and-publish workflow in the private repository.
4. Publish the generated `_site` output into `site/` here.
5. Verify the generated site.
6. Merge this migration branch into `main`.
7. Remove the Jekyll source files from the public repository after the private copy is confirmed.

Do not merge this branch before the private source repository is ready and has successfully published a complete generated site into `site/`.
