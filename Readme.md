This is a https://gohugo.io/ blog.
There are two branches in the repo:

* master - for the currently published blog content
* hugo - the hugo code

The themes directory contains git submodules referencing hugo themes.

Running locally:

```
  git checkout hugo
  hugo server --theme=after-dark --buildDrafts server
```

Publishing:

```
  git clone https://github.com/maxdeliso/maxdeliso.github.io.git -b hugo
  git submodule init
  git submodule update
  git clone https://github.com/maxdeliso/maxdeliso.github.io.git -b master public
  hugo --theme=after-dark
  cd public
  git add --all
  git commit -m "update message"
  git push origin master
```
