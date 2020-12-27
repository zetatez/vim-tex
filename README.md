# vim and latex 
- vim
- tex
- zathura
- skim


- install mactex, zathura, skim, vim-plug
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

- configure your .vimrc

```vim
" ------------------------------------------------------
" |   vim-plug start                                   |
" ------------------------------------------------------
" -- configure vim, make it's build in plug-ins loadable
filetype plugin on
call plug#begin('~/.vim/plugged')

" snippets
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

" tex
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
- configure your skim
skim -> preferences -> sync
- custom
- vi
- `--remote-silent +"%line" "%file"`


- ultisnips for latex
```bash
mkdir -p ~/.config/ultisnips

vi ~/.config/ultisnips/tex.snippets
# add
# -----------------------------
# tex

# basic
# -----------------------------------------
snippet law "law"
S_{T}=S_{0}\int_T e^{r(t)}du
endsnippet

snippet parts "parts" bwA
\part{${1:part 1}}$0
\part{${2:part 2}}
\part{${3:part 3}}
endsnippet

snippet chapters "chapter" bwA
\chapter{${1:chapter 1}}$0
\chapter{${2:chapter 2}}
\chapter{${3:chapter 3}}
endsnippet

snippet sections "section" bwA
\section{${1:section 1}}$0
\section{${2:section 2}}
\section{${3:section 3}}
endsnippet

snippet subsections "subsection" bwA
\subsection{${1:subsection 1}}$0
\subsection{${2:subsection 2}}
\subsection{${3:subsection 3}}
endsnippet

snippet subsubsections "subsubsection" bwA
\subsubsection{${1:subsubsection 1}}$0
\subsubsection{${2:subsubsection 2}}
\subsubsection{${3:subsubsection 3}}
endsnippet

snippet paragraphs "paragraph" bwA
\paragraph{${1:paragraph 1}}$0
\paragraph{${2:paragraph 2}}
\paragraph{${3:paragraph 3}}
endsnippet

snippet subparagraphs "subparagraph" bwA
\subparagraph{${1:subparagraph 1}}$0
\subparagraph{${2:subparagraph 2}}
\subparagraph{${3:subparagraph 3}}
endsnippet

snippet documentc "documentclass" bwA
\documentclass[${1:options}]{${2:class}}$0

\begin{document}

\begin{document}
endsnippet

snippet usepack "usepackage" bwA
\usepackage[${1:options}]{${2:package}}$0
endsnippet

snippet 'begin\.([\S]+) ' "begin end" brwA
`!p snip.rv = '\\begin{' + match.group(1) + '}'`
    $1
`!p snip.rv = '\\end{' + match.group(1) + '}'`
$0
endsnippet

snippet 'begin list' "begin end" bwA
\begin{${1:itemize|enumerate|description}}[noitemsep]
	\item ${2:item 1}$0
	\item ${3:item 2}
	\item ${4:item 3}
\end{$1}
endsnippet

# math
# -----------------------------------------
# inline math 
snippet mk "math" wA
$${1}$`!p
if t[2] and t[2][0] not in [',', '.', '?', '-', ' ']:
    snip.rv = ' '
else:
    snip.rv = ''
`$2
endsnippet

# display math
snippet dm "math" wA
\[
$1
\] $0
endsnippet

# fractions
# priority 1
snippet / "fraction" i
\\frac{${VISUAL}}{$1}$0
endsnippet

# //      ->  \frac{}{}
snippet // "fraction" iA
\\frac{$1}{$2}$0
endsnippet

# 3/      ->  \frac{3}{}
# 4\pi^2/     ->  \frac{4\pi^2}{}
# (1 + 2 + 3)/  ->  \frac{1 + 2 + 3}{}
# (1+(2+3)/)  ->  (1 + \frac{2+3}{})
# (1 + (2+3))/  ->  \frac{1 + (2+3)}{}
snippet '([\w\*\^\_\\\%\!]+|\([^(\)]+\)|\[[^\[\]]+\]|\\frac\{.*\}\{.*\}|\{[^(\\frac\{.*\}\{.*\})]*\})/' "fraction" wrA
\\frac{`!p snip.rv = match.group(1)`}{$1}$0
endsnippet

# you can use it by first select­ing some text, then pressing Tab, typing / and pressing Tab again
snippet '^.*\)/' "() fraction" wr
`!p
stripped = match.string[:-1]
depth = 0
i = len(stripped) - 1
while True:
  if stripped[i] == ')': depth += 1
  if stripped[i] == '(': depth -= 1
  if depth == 0: break;
  i -= 1
snip.rv = stripped[0:i] + "\\frac{" + stripped[i+1:-1] + "}"
`{$1}$0
endsnippet

# sub and supscript
snippet '([A-Za-z])(\d)' "auto subscript" wr
`!p snip.rv = match.group(1)`_`!p snip.rv = match.group(2)`
endsnippet

# auto subcript
# type x1_12324 tap -> x1_{1234} .
snippet '([A-Za-z0-9])_([\S]+) ' "auto subscript 1" wrA
`!p snip.rv = match.group(1)`_{`!p snip.rv = match.group(2).strip()`} 
endsnippet

# auto subscript
# type x1^12324 tap -> x1^{1234} .
snippet '([A-Za-z0-9])\^([\S]+) ' "auto supscript 1" wrA
`!p snip.rv = match.group(1)`^{`!p snip.rv = match.group(2).strip()`} 
endsnippet

# sympy and mathematica
# sympy Tab expands to sympy | sympy, and sympy 1 + 1 sympy Tab expands to 2
snippet sympy "sympy block " w
sympy $1 sympy$0
endsnippet

snippet 'sym(.*)sym' "evaluate sympy" wr
`!p
from sympy import *
x, y, z, t = symbols('x y z t')
k, m, n = symbols('k m n', integer=True)
f, g, h = symbols('f g h', cls=Function)
init_printing()
snip.rv = eval('latex(' + match.group(1).replace('\\', '') \
    .replace('^', '**') \
    .replace('{', '(') \
    .replace('}', ')') + ')')
`
endsnippet

snippet '(\#|\$|\%|\^|\&|\_|\{|\}|\~|\\)\\' "reserved characters" rwA
\\`!p snip.rv = match.group(1)`
endsnippet

# alphabet: Alpha|Beta|Gamma|Delta|Epsilon|Zeta|Eta|Theta|Iota|Kappa|Lambda|Mu|Nu|Xi|Omicron|Pi|Rho|Sigma|Tau|Upsilon|Phi|Chi|Psi|Omega
# alphabet: alpha|beta|gamma|delta|epsilon|zet|eta|theta|iota|kappa|lambda|mu|nu|xi|omicron|pi|rho|sigma|tau|upsilon|phi|chi|psi|omega
# alphabet: varepsilon|vartheta|varkappa|varpi|varrho|varsigma|varphi
# math    : imath|hbar|ell|mho|Re|Im|aleph|wp
# newline : clearpage|pagebreak
# function: max|min|sup|inf|limsup|liminf|ker|exp|lg|deg|Pr|det|hom|arg|dim|infty|infty
# triangle: sin|cos|tan|sec|csc|cot|arcsin|arccos|arctan|sinh|cosh|tanh
# sets    : forall/exists/empty/emptyset/varnothing/in/ni/notin/subset/subseteq/supset/supseteq/cap/bigcap/cup/bigcup/biguplus/sqsubset/susubseteq/sqsupset/sqsupseteq/sqcap/sqcup/bigsqcup/sum/prod/coprod
# logic   : lnot|land|lor|lneg|wedge|bigwedge|vee|bigvee|smallsetminus|iff|iff
# special noations: oplus|bigoplus|otimes|bigotimes|bigodot|boxplus|boxtimes|times|div|ciro|cdot|bullet|dots|cdots|vdots|ddots|nabala|triangle|Box|Diamond|diamondsuit|heartsuit
# reserved characters: copyright|vfill|hfill
snippet '(Alpha|Beta|Gamma|Delta|Epsilon|Zeta|Eta|Theta|Iota|Kappa|Lambda|Mu|Nu|Xi|Omicron|Pi|Rho|Sigma|Tau|Upsilon|Phi|Chi|Psi|Omega|alpha|beta|gamma|delta|epsilon|zet|eta|theta|iota|kappa|lambda|mu|nu|xi|omicron|pi|rho|sigma|tau|upsilon|phi|chi|psi|omega|varepsilon|vartheta|varkappa|varpi|varrho|varsigma|varphi|imath|hbar|ell|mho|Re|Im|aleph|wp|clearpage|pagebreak|max|min|sup|inf|limsup|liminf|ker|exp|lg|deg|Pr|det|hom|arg|dim|infty|sin|cos|tan|sec|csc|cot|arcsin|arccos|arctan|sinh|cosh|tanh|forall|exists|empty|emptyset|varnothing|inn|ni|notin|subset|subseteq|supset|supseteq|cap|bigcap|cup|bigcup|biguplus|sqsubset|susubseteq|sqsupset|sqsupseteq|sqcap|sqcup|bigsqcup|sum|prod|coprod|lnot|land|lor|lneg|wedge|bigwedge|vee|bigvee|smallsetminus|iff|setminus|oplus|bigoplus|otimes|bigotimes|bigodot|boxplus|boxtimes|times|div|ciro|cdot|bullet|dots|cdots|vdots|ddots|nabala|triangle|Box|Diamond|diamondsuit|heartsuit|copyright|vfill|hfill) ' "functions" rwA
`!p snip.rv = '\\' + match.group(1) + ' '`$0
endsnippet

snippet lim "lim" iw
\lim_{n=1}^{n=\infty} $0
endsnippet

snippet lims "limsup" iwA
\limsup $0
endsnippet

snippet limi "liminf" iwA
\liminf $0
endsnippet

snippet int "integral" iw
\int_{${1:-\infty}}^{${2:\infty}} ${3:${VISUAL}} $0
endsnippet

snippet 'log ([\w]+) ([\w]+) ' "log_a b" rwA
`!p snip.rv = '\\log_' + '{' + match.group(1) + '} ' + match.group(2)`$0
endsnippet

# text: title|textsubscript|textsupscript
# font text: \textrm\textsf\texttt\textmd\textbf\textup\textit\textsl\textsc\emph
snippet '(title|textsubscript|textsupscript|textrm|textsf|texttt|textmd|textbf|textup|textit|textsl|textsc|emph) ([^\.]+)\.' "math" rwA
`!p snip.rv = '\\' + match.group(1) + '{' + match.group(2) + '} '`$0
endsnippet

# tone: bar|acute|check|grave|breve|hat|tilde|vec|dot|ddot|partial
snippet '(bar|acute|check|grave|breve|hat|tilde|vec|dot|ddot|partial) ([\S]+) ' "å" rwA
`!p snip.rv = '\\' + match.group(1) + ' ' + match.group(2) + ' '`
endsnippet

# font size: \tiny\scriptsize\footnotesize\small\normalsize\large\Large\LARGE\huge\Huge
snippet '(tiny|scriptsize|footnotesize|small|normalsize|large|Large|LARGE|huge|Huge)\.([^\.]+)\. ' "color" rwA
`!p snip.rv = '\{\\' + match.group(1) + ' ' + match.group(2) + '\} '`$0
endsnippet

# font math: mathnormal/mathbb/mathbf/mathrm/mathit/mathtt/mathcal/mathrak/boldsymbol
snippet '(mathnormal|mathbb|mathbf|mathrm|mathit|mathtt|mathcal|mathrak|boldsymbol) ([\S]+) ' "math" rwA
`!p snip.rv = '\\' + match.group(1) + '{' + match.group(2) + '} '`$0
endsnippet

# mod: pmod/bmod
snippet 'pmod ([\w]+) ' "mod(m)" rwA
`!p snip.rv = '\\pmod{' + match.group(1) + '}'`$0
endsnippet

snippet '([\S]+) bmod ([\S]+)' "a mod b" rwA
`!p snip.rv = match.group(1) + ' \\bmod ' + match.group(1)`$0
endsnippet

# sqrt:sqrt/sqrt[n]
snippet 'sqrt ([\S]+) ([\S]+) ' "sqrt" rwA
`!p
if match.group(1) == '2':
    snip.rv = '\\sqrt' + '{' + match.group(2) + '}'
else:
    snip.rv = '\\sqrt' + '[' + match.group(1) + ']' + '{' + match.group(2) + '}'
`$0
endsnippet

# color
snippet 'color\.(\w+)\.([^\.]+)\.' "color" rwA
`!p snip.rv = '{\\color' + '{' + match.group(1) + '} ' + match.group(2) + '} '`$0
endsnippet

# arrow
snippet '(left|right|liftright|longleft|longright|longleftright|hockleft|hockright|up|down|updown|ne|se|sw|nw|Left|Right|Liftright|Leftright|Longleft|Longright|Longleftright|Up|Down|Updown) arrow ' "arrow" rwA
`!p snip.rv = '\\' + match.group(1) + 'arrow' + ' '`$0
endsnippet

snippet '(to|gets|mapsto|longmapsto)\.' "to gets mapsto longmapsto" rwA
`!p snip.rv = '\\' + match.group(1) + ' '`$0
endsnippet

snippet '(left|right|up|down)\.(left|right|up|down)' "harpoon" rwA
`!p snip.rv = '\\' + match.group(1) + 'harpoon' + match.group(2) `$0
endsnippet

# []something.-> \left[ something \right]
# ()something.-> \left( something \right)
# {}something.-> \left{ something \right}
# <>something.-> \left< something \right>
# [.something.-> \left[ something \right.
snippet "(\[|\(|\{|\<|\.|\||\\)(\]|\)|\}|\>|\.|\||\\)([^\.]+)\." "parenthesis" rwA
`!p
snip.rv = '\\left' + match.group(1) + ' ' + match.group(3) + ' \\right ' + match.group(2)
`$0
endsnippet

snippet "(ceil|floor|angle)([^\.]+)\." "parenthesis" rwA
`!p
snip.rv = '\\left \\l' + match.group(1) + ' ' + match.group(2) + ' \\right \\r' + match.group(1)
`$0
endsnippet

snippet () "left( right)" iA
\left( ${1:${VISUAL}} \right) $0
endsnippet

snippet {} "left{ right}" i
\left\\{ ${1:${VISUAL}} \right\\} $0
endsnippet

snippet || "left| right|" i
\left| ${1:${VISUAL}} \right| $0
endsnippet

snippet <> "leftangle rightangle" iA
\left<${1:${VISUAL}} \right>$0
endsnippet

snippet bbox "box"
`!p snip.rv = '┌' + '─' * (len(t[1]) + 2) + '┐'`
│ $1 │
`!p snip.rv = '└' + '─' * (len(t[1]) + 2) + '┘'`
$0
endsnippet

snippet pmat "pmat" iA
\begin{pmatrix} $1 \end{pmatrix} $0
endsnippet

snippet bmat "bmat" iA
\begin{bmatrix} $1 \end{bmatrix} $0
endsnippet

snippet conj "conjugate" iA
\overline{$1}$0
endsnippet

snippet sum "sum" w
\sum_{n=${1:1}}^{${2:\infty}} ${3:a_n z^n}
endsnippet

snippet taylor "taylor" w
\sum_{${1:k}=${2:0}}^{${3:\infty}} ${4:c_$1} (x-a)^$1 $0
endsnippet

snippet prod "product" w
\prod_{${1:n=${2:1}}}^{${3:\infty}} ${4:${VISUAL}} $0
endsnippet

snippet __ "subscript" iA
_{$1}$0
endsnippet

snippet ^^ "subscript" iA
^{$1}$0
endsnippet

snippet ooo "\infty" iA
\infty
endsnippet

snippet xx "cross" iA
\times 
endsnippet

snippet ** "cdot" iA
\cdot 
endsnippet

snippet normm "norm" iA
\|$1\|$0
endsnippet

snippet invv "inverse" iA
^{-1}
endsnippet

snippet comm "complement" iA
^{c}
endsnippet

snippet tt "text" iA
\text{$1}$0
endsnippet

snippet vecc "v vector" iA
\begin{pmatrix} ${1:x}_${2:1}\\\\ \vdots\\\\ $1_${2:n} \end{pmatrix}
endsnippet

snippet r0+ "R0+" iA
\\R_0^+
endsnippet

snippet r0- "R0-" iA
\\R_0^-
endsnippet



# table
# -----------------------------------------
snippet table "table environment" b
\begin{table}[${1:htpb}]
	\centering
	\caption{${2:caption}}
	\label{tab:${3:label}}
	\begin{tabular}{${5:c}}
	$0${5/((?<=.)c|l|r)|./(?1: & )/g}
	\end{tabular}
\end{table}
endsnippet

# figure
# -----------------------------------------
snippet fig "figure environment" b
\begin{figure}[${1:htpb}]
	\centering
	${2:\includegraphics[width=0.8\textwidth]{$3}}
	\caption{${4:$3}}
	\label{fig:${5:${3/\W+/-/g}}}
\end{figure}
endsnippet

# plot
# -----------------------------------------
snippet plot "plot" w
\begin{figure}[$1]
	\centering
	\begin{tikzpicture}
		\begin{axis}[
			xmin= ${2:-10}, xmax= ${3:10},
			ymin= ${4:-10}, ymax = ${5:10},
			axis lines = middle,
		]
			\addplot[domain=$2:$3, samples=${6:100}]{$7};
		\end{axis}
	\end{tikzpicture}
	\caption{$8}
	\label{${9:$8}}
\end{figure}
endsnippet

snippet node "tikz node" w
\node[$5] (${1/[^0-9a-zA-Z]//g}${2}) ${3:at (${4:0,0}) }{$${1}$};
$0
endsnippet


# 'template article'
# -----------------------------------------
snippet 'template article' "template article" bA
\documentclass[twoside,a4paper,10pt]{article}
\usepackage[UTF8]{ctex}

% *** page setup ***
%
\usepackage{geometry}
\geometry{reset,
          ignoreall,
          textheight = 253mm,
          textwidth  = 175mm,
          bottom     = 21mm,
          inner      = 17.5mm,
          footskip   = 8mm,
          headsep    = 5mm,
          headheight = 10.14995pt
          }


% *** fancy ***
%
\usepackage{fancyhdr}
\renewcommand{\headrule}{}
\renewcommand{\footrule}{}
\pagestyle{fancy}
\fancyhead{}\fancyfoot{}
\fancyhead[LE]{ }
\fancyhead[RO]{ }
\fancyfoot[LE,RO]{\sffamily\bfseries\upshape \thepage}


% *** CITATION PACKAGES Undefined***
\usepackage{cite}


% *** GRAPHICS RELATED PACKAGES ***
%
\usepackage{graphicx}

% or
% \usepackage[pdftex]{graphicx}
% declare the path(s) where your graphic files are
\graphicspath{./pics/}
% and their extensions so you won't have to specify these with
% every instance of \includegraphics
% \DeclareGraphicsExtensions{.pdf,.jpeg,.png}

% or
% or other class option (dvipsone, dvipdf, if not using dvips). graphicx
% will default to the driver specified in the system graphics.cfg if no
% driver is specified.
% \usepackage[dvips]{graphicx}
% declare the path(s) where your graphic files are
% \graphicspath{{../eps/}}
% and their extensions so you won't have to specify these with
% every instance of \includegraphics
% \DeclareGraphicsExtensions{.eps}

\usepackage{tikz}


% *** MATH PACKAGES ***
%
\usepackage{amsmath}
% A popular package from the American Mathematical Society that provides
% many useful and powerful commands for dealing with mathematics.
%
% Note that the amsmath package sets \interdisplaylinepenalty to 10000
% thus preventing page breaks from occurring within multiline equations. Use:
\interdisplaylinepenalty=2500
% after loading amsmath to restore such page breaks as IEEEtran.cls normally
% does. amsmath.sty is already installed on most LaTeX systems. The latest
% version and documentation can be obtained at:
% http://www.ctan.org/pkg/amsmath


% *** SPECIALIZED LIST PACKAGES ***
\usepackage{acronym}
% acronym.sty was written by Tobias Oetiker. This package provides tools for
% managing documents with large numbers of acronyms. (You don't *have* to
% use this package - unless you have a lot of acronyms, you may feel that
% such package management of them is bit of an overkill.)
% instead of using the acronym environment's optional argument.
% The latest version and documentation can be obtained at:
% http://www.ctan.org/pkg/acronym

\usepackage{algorithmic}
% algorithmic.sty was written by Peter Williams and Rogerio Brito.
% This package provides an algorithmic environment fo describing algorithms.
% You can use the algorithmic environment in-text or within a figure
% environment to provide for a floating algorithm. Do NOT use the algorithm
% floating environment provided by algorithm.sty (by the same authors) or
% algorithm2e.sty (by Christophe Fiorio) as the IEEE does not use dedicated
% algorithm float types and packages that provide these will not provide
% correct IEEE style captions. The latest version and documentation of
% algorithmic.sty can be obtained at:
% http://www.ctan.org/pkg/algorithms
% Also of interest may be the (relatively newer and more customizable)
% algorithmicx.sty package by Szasz Janos:
% http://www.ctan.org/pkg/algorithmicx

\usepackage{fancyvrb}                     % verbatim env 抄录


% *** ALIGNMENT PACKAGES ***
%
\usepackage{array}
% Frank Mittelbach's and David Carlisle's array.sty patches and improves
% the standard LaTeX2e array and tabular environments to provide better
% appearance and additional user controls. As the default LaTeX2e table
% generation code is lacking to the point of almost being broken with
% respect to the quality of the end results, all users are strongly
% advised to use an enhanced (at the very least that provided by array.sty)
% set of table tools. array.sty is already installed on most systems. The
% latest version and documentation can be obtained at:% http://www.ctan.org/pkg/array

\usepackage{mdwmath}
\usepackage{mdwtab}
% Also highly recommended is Mark Wooding's extremely powerful MDW tools,
% especially mdwmath.sty and mdwtab.sty which are used to format equations
% and tables, respectively. The MDWtools set is already installed on most
% LaTeX systems. The lastest version and documentation is available at:
% http://www.ctan.org/pkg/mdwtools

\usepackage{eqparbox}
% Also of notable interest is Scott Pakin's eqparbox package for creating
% (automatically sized) equal width boxes - aka "natural width parboxes".
% Available at:
% http://www.ctan.org/pkg/eqparbox


% *** SUBFIGURE PACKAGES ***
\usepackage[caption=false,font=footnotesize,labelfont=sf,textfont=sf]{subfig}
% subfig.sty, written by Steven Douglas Cochran, is the modern replacement
% for subfigure.sty.

% Note that the Computer Society format requires a sans serif font rather
% than the serif font used in traditional IEEE formatting and thus the need
% to invoke different subfig.sty package options depending on whether
% compsoc mode has been enabled.
%
% The latest version and documentation of subfig.sty can be obtained at:
% http://www.ctan.org/pkg/subfig


% *** FLOAT PACKAGES ***
\usepackage{float}                        % [H] not float
\usepackage{dblfloatfix}
% The latest version can be found at:
% http://www.ctan.org/pkg/dblfloatfix


% *** PDF, URL AND HYPERLINK PACKAGES ***
%
\usepackage{url}
% url.sty was written by Donald Arseneau. It provides better support for
% handling and breaking URLs. url.sty is already installed on most LaTeX
% systems. The latest version and documentation can be obtained at:
% http://www.ctan.org/pkg/url
% Basically, \url{my_url_here}.

% \usepackage[pdftex]{thumbpdf}
% or
% \usepackage[dvips]{thumbpdf}
% thumbpdf.sty and its companion Perl utility were written by Heiko Oberdiek.
% It allows the user a way to produce PDF documents that contain fancy
% thumbnail images of each of the pages (which tools like acrobat reader can
% utilize). This is possible even when using dvi->ps->pdf workflow if the
% correct thumbpdf driver options are used. thumbpdf.sty incorporates the
% file containing the PDF thumbnail information (filename.tpm is used with
% dvips, filename.tpt is used with pdftex, where filename is the base name of
% your tex document) into the final ps or pdf output document. An external
% utility, the thumbpdf *Perl script* is needed to make these .tpm or .tpt
% thumbnail files from a .ps or .pdf version of the document (which obviously
% does not yet contain pdf thumbnails). Thus, one does a:
% 
% thumbpdf filename.pdf 
%
% to make a filename.tpt, and:
%
% thumbpdf --mode dvips filename.ps
%
% to make a filename.tpm which will then be loaded into the document by
% thumbpdf.sty the NEXT time the document is compiled (by pdflatex or
% latex->dvips->ps2pdf). Users must be careful to regenerate the .tpt and/or
% .tpm files if the main document changes and then to recompile the
% document to incorporate the revised thumbnails to ensure that thumbnails
% match the actual pages. It is easy to forget to do this!
% 
% Unix systems come with a Perl interpreter. However, MS Windows users
% will usually have to install a Perl interpreter so that the thumbpdf
% script can be run. The Ghostscript PS/PDF interpreter is also required.
% See the thumbpdf docs for details. The latest version and documentation
% can be obtained at.
% http://www.ctan.org/pkg/thumbpdf

\usepackage{hyperref}
\hypersetup{colorlinks,
            linkcolor=blue,
            urlcolor=blue,
            citecolor=blue,
            plainpages=false
            }


% *** customize enumitem ***
%
\usepackage{enumitem}                     % Customized lists
\setlist[itemize]{noitemsep}              % Make itemize lists more compact


% *** bibliography ***
%
% \usepackage[numbers,sort&compress]{natbib}


\begin{document}
\title{Happy Hacking}
\author{F. Lorenzo}
% \date{\today}

\begin{abstract}
HAPPY HACKING
\end{abstract}

\maketitle
\tableofcontents


\section{Introduction}
$0

HAPPY HACKING


\subsection{Subsection Heading Here}

Subsection text here.


\subsubsection{Subsubsection Heading Here}

Subsubsection text here.



\section{Conclusion}
The conclusion goes here.



%% Appendices
\appendix
\section*{Appendix I}

Appendix I goes here.

\section*{Appendix II}

Appendix II goes here.


%% Acknowledgment
\section*{Acknowledgment}

The authors would like to thank Floren.


%% Bibliography
% \bibliographystyle{naturemag} 
% \bibliography{references}{}


% To get bibliography, as usual, run pdflatex -> bibtex -> pdflatex -> pdflatex.

\end{document}
endsnippet


# 'template book'
# -----------------------------------------
snippet 'template book' "template book" bA
\documentclass[twoside,a4paper,10pt]{book}
\begin{document}
\title{Our Fun Document}
\author{Jane Doe \and John Doe}
\date{\today}
\maketitle

book goes here

\end{document}
endsnippet

# template letter
# -----------------------------------------
snippet 'template letter' "template letter" bA
\documentclass[twoside,a4paper,10pt]{letter}
\begin{document}

letter goes here

\end{document}
endsnippet

# template slides
# -----------------------------------------
snippet 'template slides' "template slides" bA
\documentclass[twoside,a4paper,10pt]{slides}
\begin{document}
\title{Our Fun Document}
\author{Jane Doe \and John Doe}
\date{\today}
\maketitle

slides goes here

\end{document}
endsnippet

# template beamer
# -----------------------------------------
snippet 'template beamer' "template beamer" bA
\documentclass[twoside,a4paper,10pt]{beamer}
\begin{document}
\title{Our Fun Document}
\author{Jane Doe \and John Doe}
\date{\today}
\maketitle

beamer goes here

\end{document}
endsnippet

# -----------------------------

```
