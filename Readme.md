This is a https://gohugo.io/ blog.
There are two branches in the repo:

* master - for the currently published blog content
* hugo - the hugo code

The themes directory contains git submodules referencing hugo themes.

Running locally:

```
  git clone git@github.com:maxdeliso/maxdeliso.github.io.git
  git checkout hugo
  git submodule init
  git submodule update
  hugo server --buildDrafts server
```

Publishing:

```
  git clone git@github.com:maxdeliso/maxdeliso.github.io.git -b hugo
  git submodule init
  git submodule update
  rm -rf public
  git clone git@github.com:maxdeliso/maxdeliso.github.io.git -b master public
  hugo
  cd public
  git add --all
  git commit -m "[update message]"
  git push origin master
```
