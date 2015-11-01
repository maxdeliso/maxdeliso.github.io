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
  git clone https://github.com/maxdeliso/maxdeliso.github.io.git
  git checkout hugo
  git submodule init
  git submodule update
  hugo --theme=after-dark
  cd public
  git init
  git remote add origin https://github.com/maxdeliso/maxdeliso.github.io.git
  git checkout -b master
  git add --all
  git commit -m "update message"
  git push origin master
```
