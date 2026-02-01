# puffer-fish

### for local

Synchronize submodules

```
git submodule update --init --recursive
```

```
npm install
```

#### Start Server

```
hugo server --theme toha --watch --disableFastRender --ignoreCache
```

#### generate ogp

```
tcardgen \
    -c tcardgen.config.yaml \
    --fontDir static/fonts/kinto \
    --output static/ogp \
    --template static/ogp/title_template.png \
    content/posts/category/look-back-on/2024/06/08/index.md
```
