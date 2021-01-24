# vim and tex
- [1.Installation](#1installation)
- [2.Configure your .vimrc](#2configure-your-vimrc)
- [3.Ultisnips !!!](#3ultisnips-)
- [4.Configure your skim](#4configure-your-skim)

### 1.Installation
```bash
# install mactex
# note: need to enter root password
brew install --cask mactex-no-gui &

# install zathura
brew tap zegervdv/zathura &
brew install xdotool &
brew install zathura &
brew install zathura-pdf-poppler &

# install skim
brew cask install skim &

wait
 
mkdir -p $(brew --prefix zathura)/lib/zathura
ln -s $(brew --prefix zathura-pdf-poppler)/libpdf-poppler.dylib $(brew --prefix zathura)/lib/zathura/libpdf-poppler.dylib

mkdir -p ~/.config/zathura
echo "set selection-clipboard clipboard" > ~/.config/zathura/zathurarc

# install vim-plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 2.Configure your .vimrc
```vim
" ------------------------------------------------------
" |   vim-plug start                                   |
" ------------------------------------------------------
" -- configure vim, make it's build in plug-ins loadable
filetype plugin on
call plug#begin('~/.vim/plugged')

""""""
"""""" snippets
Plug 'SirVer/ultisnips'                                                 " ultimate snippet
" Trigger configuration. You need to change this to something other than <\> if you use one of the following:
" - https://github.com/Valloric/YouCompleteMe
" - https://github.com/nvim-lua/completion-nvim
let g:UltiSnipsExpandTrigger = '<tab>'
let g:UltiSnipsJumpForwardTrigger = '<c-j>'
let g:UltiSnipsJumpBackwardTrigger = '<c-k>'
let g:UltiSnipsEditSplit='vertical'                                     " If you want :UltiSnipsEdit to split your window
let g:UltiSnipsSnippetDirectories = ['~/.config/ultisnips']

Plug 'honza/vim-snippets'                                               " snippets are separated from the engine

""""""
"""""" tex
Plug 'lervag/vimtex'                                                    " latex
let g:tex_flavor='latex'
let g:vimtex_view_method='skim'
let g:vimtex_quickfix_mode=0
let g:vimtex_compiler_latexmk = {
      \ 'executable' : 'latexmk',
      \ 'options' : [
      \   '-xelatex',
      \   '-file-line-error',
      \   '-synctex=1',
      \   '-interaction=nonstopmode',
      \ ],
      \}

Plug 'KeitaNakamura/tex-conceal.vim'
set conceallevel=1
let g:tex_conceal='abdmg'

call plug#end()
" -----------
" :w
" :source %
" :PlugInstall
" :qa
" ------------------------------------------------------
" |   vim-plug end                                     |
" ------------------------------------------------------

" ---
" --- for tex, open pdf with zathura in current folder
" ---
noremap \lp :! zathura *pdf &<CR><CR>
```

### 3.Ultisnips !!!
```bash
mkdir -p ~/.config/ultisnips

curl -fsSL https://raw.githubusercontent.com/zetatez/vim-tex/main/tex.snippets > ~/.config/ultisnips/tex.snippets
```

### 4.Configure your skim
skim -> preferences -> sync
- custom
- vi
- `--remote-silent +"%line" "%file"`



