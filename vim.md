## ctags

Jump to tag under cursor - `Ctrl + ]`\
Jump back up tag stack - `Ctrl + t`

Generate tags file for current directory and all subdirectories:
```
ctags -R
```
Add to ~/.vimrc to search for tags file in parent directories:
```
set tags=tags;/
```

##  Pathogen plugin manager

```
mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```

Add to ~/.vimrc:
```
execute pathogen#infect()
syntax on
filetype plugin indent on
```

Now, to add a new plugin from a git repository, clone the repo inside ~/.vim/bundle/

### vim-scala plugin
```
git clone https://github.com/derekwyatt/vim-scala.git
```

## CtrlP