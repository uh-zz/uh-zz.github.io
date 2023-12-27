# small-land

### for local

```
hugo server --theme toha --watch --disableFastRender --ignoreCache
```

generate ogp

```
tcardgen \
    --fontDir static/fonts/kinto \
    --output static/ogp \
    --template static/ogp/title_template.png \
    content/posts/category/look-back-on/2023/12/24/index.md
```
