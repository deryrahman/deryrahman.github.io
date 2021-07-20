+++
title = "July Vim Setup"
date = "2021-07-20T12:02:09+07:00"
author = "Dery R Ahaddienata"
authorTwitter = "deryrahman" #do not include @
cover = ""
tags = ["tech", "editor", "vim"]
keywords = ["vim", "setup", "neovim"]
description = "I've been using [vim as my default IDE](https://levelup.gitconnected.com/tweak-your-vim-as-a-powerful-ide-fcea5f7eff9c) for about a year. Until now I'm still improving the vim configuration so that I could do the task even more effective. Now after [nvim launch version 0.5.0](https://neovim.io/roadmap/) which support native LSP client, I immediately tried this feature. Yeps, there're no significant difference when I use [LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim), but since version 0.5.0 has built in feature for LSP client, I would preferably use the built in one."
showFullContent = false
toc = true
+++

I've been using [vim as my default IDE][1] for about a year. Until now I'm still improving the vim configuration so that I could do the task even more effective. Now after [nvim launch version 0.5.0][2] which support native LSP client, I immediately tried this feature. Yeps, there're no significant difference when I use [LanguageClient-neovim][3], but since version 0.5.0 has built in feature for LSP client, I would preferably use the built in one.

If we compare with [my last setup][1], we can see significant config differences. And because I also planned to try contribute on [LunarVim project][4], I would like to documenting all of the setup I used now.

[![asciicast](https://asciinema.org/a/426314.svg)](https://asciinema.org/a/426314)

## Plugins

### Autocomplete, Definition, etc Related Plugin

1. neovim/nvim-lspconfig
2. hrsh7th/nvim-compe
3. ojroques/nvim-lspfuzzy

### Syntax, Linter, etc Related Plugin

1. nvim-treesitter/nvim-treesitter
2. dense-analysis/ale

### Theme & Layouting Related Plugin

1. junegunn/fzf.vim
2. szw/vim-maximizer
3. preservim/nerdtree
4. Xuyuanp/nerdtree-git-plugin
5. morhetz/gruvbox
6. vim-airline/vim-airline
7. vim-airline/vim-airline-themes
8. airblade/vim-gitgutter
9. yuttie/comfortable-motion.vim

### Debbuger
1. puremourning/vimspector

### Misc
1. vimwiki/vimwiki


## Dot Files

### Plugin Config

{{<code language="vim" title="~/.vimrc" isCollapsed="true">}}
    set nocompatible
    filetype plugin on
    syntax on
    set modeline
    set expandtab
    set tabstop=4
    set shiftwidth=4
    set exrc " .vimrc in local project dir
    set secure
    autocmd BufRead,BufNewFile * set signcolumn=yes
    autocmd FileType tagbar,nerdtree set signcolumn=no
    set foldmethod=syntax
    set nofoldenable
    set number
    set belloff=""
    set lazyredraw
    set ttyfast
    set spell spelllang=en_us

    au CursorHold,CursorHoldI * checktime

    if (has('nvim'))
        set diffopt+=vertical
        "-- VIM-PLUG --
        call plug#begin('~/.vim/plugged')
            Plug 'neovim/nvim-lspconfig'
            Plug 'hrsh7th/nvim-compe'
            Plug 'ojroques/nvim-lspfuzzy'
            Plug 'nvim-treesitter/nvim-treesitter', {'do': ':TSUpdate'}
            Plug 'puremourning/vimspector'
            Plug 'szw/vim-maximizer'
            Plug 'preservim/nerdtree'
            Plug 'Xuyuanp/nerdtree-git-plugin'
            Plug 'sonph/onehalf', {'rtp': 'vim/'}
            Plug 'morhetz/gruvbox'
            Plug 'vim-airline/vim-airline'
            Plug 'vim-airline/vim-airline-themes'
            Plug 'airblade/vim-gitgutter'
            Plug 'dense-analysis/ale'
            Plug 'junegunn/fzf.vim'
            Plug '/usr/local/opt/fzf'
            Plug 'yuttie/comfortable-motion.vim'
            Plug 'vimwiki/vimwiki'
        call plug#end()

        "-- PLUGIN CONFIGS --
        source ~/.vim/config/theme.vim
        source ~/.vim/config/fzf.vim
        source ~/.vim/config/treesitter.vim
        source ~/.vim/config/vimwiki.vim
        luafile ~/.vim/config/compe.lua
        luafile ~/.vim/config/lspfuzzy.lua
        source ~/.vim/config/lsp_config.vim
        source ~/.vim/config/ale.vim
        source ~/.vim/config/vimspector.vim
        source ~/.vim/config/maximizer.vim

        "-- EXTERNAL CONFIGS --
        source ~/.vim/config/autoclose.vim
        source ~/.vim/config/custom_map.vim
        source ~/.vim/config/session.vim
    endif
{{</code>}}

{{<code language="vim" title="~/.vim/config/theme.vim" isCollapsed="true">}}
    if (has("termguicolors") && $TERM_PROGRAM ==# 'iTerm.app')
      set termguicolors
    endif

    set cursorline
    set background=dark

    autocmd vimenter * ++nested colorscheme gruvbox

    hi Error        ctermfg=204 ctermbg=NONE guifg=#ff5f87 guibg=NONE
    hi Warning      ctermfg=178 ctermbg=NONE guifg=#D7AF00 guibg=NONE
    hi Folded       ctermfg=grey guifg=grey ctermbg=NONE guibg=NONE
    hi Normal       ctermbg=NONE guibg=NONE
    hi SignColumn   ctermbg=235 guibg=#262626
    hi LineNr       ctermfg=grey guifg=grey ctermbg=NONE guibg=NONE
    hi CursorLineNr ctermbg=NONE guibg=NONE ctermfg=178 guifg=#d7af00

    hi LspDiagnosticsDefaultError   ctermfg=204 ctermbg=NONE guifg=#ff5f87 guibg=NONE
    hi LspDiagnosticsDefaultWarning ctermfg=178 ctermbg=NONE guifg=#D7AF00 guibg=NONE

    let g:gitgutter_set_sign_backgrounds = 0

    "-- Whitespace highlight --
    hi ExtraWhitespace ctermbg=204 guibg=#ff5f87
    match ExtraWhitespace /\s\+$/
    autocmd BufWinEnter * match ExtraWhitespace /\s\+$/
    autocmd InsertEnter * match ExtraWhitespace /\s\+\%#\@<!$/
    autocmd InsertLeave * match ExtraWhitespace /\s\+$/
    autocmd BufWinLeave * call clearmatches()

    "-- Vimwiki color --
    "-- this using palenight color scheme --
    hi VimwikiHeader1 ctermbg=NONE guibg=NONE ctermfg=180 guifg=#ffcb6b
    hi VimwikiHeader2 ctermbg=NONE guibg=NONE ctermfg=39 guifg=#82b1ff
    hi VimwikiHeader3 ctermbg=NONE guibg=NONE ctermfg=38 guifg=#89ddff
    hi VimwikiHeader4 ctermbg=NONE guibg=NONE ctermfg=38 guifg=#89ddff
    hi VimwikiHeader5 ctermbg=NONE guibg=NONE ctermfg=38 guifg=#89ddff
    hi VimwikiHeader6 ctermbg=NONE guibg=NONE ctermfg=38 guifg=#89ddff

    let g:airline#extensions#tabline#enabled = 1
    let g:airline#extensions#fugutive#enabled = 1
    let g:airline#extensions#coc#enabled = 1

    let NERDTreeShowHidden = 1
{{</code>}}

{{<code language="vim" title="~/.vim/config/fzf.vim" isCollapsed="true">}}
    set rtp+=/usr/local/opt/fzf
    source /usr/local/opt/fzf/plugin/fzf.vim

    let g:fzf_tags_command = 'ctags -R'
    let g:fzf_layout = {'up':'~90%', 'window': { 'width': 0.8, 'height': 0.8,'yoffset':0.5,'xoffset': 0.5, 'highlight': 'Todo', 'border': 'sharp' } }
    let $FZF_DEFAULT_OPTS = '--layout=reverse --info=inline'
{{</code>}}

{{<code language="vim" title="~/.vim/config/treesitter.vim" isCollapsed="true">}}
    lua <<EOF
    require'nvim-treesitter.configs'.setup {
      ensure_installed = "maintained", -- one of "all", "maintained" (parsers with maintainers), or a list of languages
      ignore_install = {}, -- List of parsers to ignore installing
      highlight = {
        enable = true,              -- false will disable the whole extension
        disable = {},  -- list of language that will be disabled
        -- Setting this to true will run `:h syntax` and tree-sitter at the same time.
        -- Set this to `true` if you depend on 'syntax' being enabled (like for indentation).
        -- Using this option may slow down your editor, and you may see some duplicate highlights.
        -- Instead of true it can also be a list of languages
        additional_vim_regex_highlighting = false,
      },
    }
    EOF
{{</code>}}

{{<code language="vim" title="~/.vim/config/vimwiki.vim" isCollapsed="true">}}
    let g:vimwiki_folding = 'custom'
    :hi VimwikiHeader1 guifg=#FF0000
    :hi VimwikiHeader2 guifg=#00FF00
    :hi VimwikiHeader3 guifg=#0000FF
    :hi VimwikiHeader4 guifg=#FF00FF
    :hi VimwikiHeader5 guifg=#00FFFF
    :hi VimwikiHeader6 guifg=#FFFF00
    let g:vimwiki_global_ext = 0
{{</code>}}

{{<code language="vim" title="~/.vim/config/lsp_config.vim" isCollapsed="true">}}
    " LSP config (the mappings used in the default file don't quite work right)
    nnoremap <silent> gd <cmd>lua vim.lsp.buf.definition()<CR>
    nnoremap <silent> gD <cmd>lua vim.lsp.buf.declaration()<CR>
    nnoremap <silent> gr <cmd>lua vim.lsp.buf.references()<CR>
    nnoremap <silent> gi <cmd>lua vim.lsp.buf.implementation()<CR>
    nnoremap <silent> K <cmd>lua vim.lsp.buf.hover()<CR>
    nnoremap <silent> <C-k> <cmd>lua vim.lsp.buf.signature_help()<CR>
    nnoremap <silent> <C-p> <cmd>lua vim.lsp.diagnostic.goto_prev()<CR>
    nnoremap <silent> <C-n> <cmd>lua vim.lsp.diagnostic.goto_next()<CR>

    " LSP config for specific language
    luafile ~/.vim/config/lsp/go.lua
    luafile ~/.vim/config/lsp/js.lua
    luafile ~/.vim/config/lsp/json.lua

    " auto-format
    autocmd BufWritePre *.js lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.jsx lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.ts lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.tsx lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.py lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.go lua vim.lsp.buf.formatting_sync(nil, 100)
    autocmd BufWritePre *.json lua vim.lsp.buf.formatting_sync(nil, 100)
{{</code>}}

{{<code language="vim" title="~/.vim/config/ale.vim" isCollapsed="true">}}
    hi clear ALEErrorSign
    hi clear ALEWarningSign
    let g:ale_echo_msg_format = '[%linter%] %s [%severity%]'
    let g:ale_sign_warning = '○'
    let g:ale_sign_error = '◉'

    hi ALEError ctermfg=204 guifg=#ff5f87 ctermbg=52 guibg=#5f0000 cterm=undercurl gui=undercurl
    hi link ALEErrorSign    Error
    hi link ALEWarningSign  Warning

    let g:ale_linters = {
                \ 'python': ['pylint'],
                \ 'javascript': ['eslint'],
                \ 'typescript': ['eslint'],
                \ 'json': ['fixjson'],
                \ 'vue': ['eslint'],
                \ 'go': ['gobuild', 'golint', 'gofmt'],
                \ 'rust': ['rls'],
                \ 'ruby': ['rubocop']
                \}

    let g:ale_fixers = {
                \ '*': ['remove_trailing_lines', 'trim_whitespace'],
                \ 'python': ['autopep8'],
                \ 'javascript': ['eslint'],
                \ 'typescript': ['eslint'],
                \ 'json': ['fixjson'],
                \ 'vue': ['eslint'],
                \ 'go': ['gofmt', 'goimports'],
                \ 'rust': ['rustfmt'],
                \ 'ruby': ['rubocop']
                \}
    let g:ale_fix_on_save = 1
{{</code>}}

{{<code language="vim" title="~/.vim/config/vimspector.vim" isCollapsed="true">}}
    let g:vimspector_enable_mappings='HUMAN'
    nmap <leader>dd <Plug>VimspectorContinue
    nmap <leader>dc :call vimspector#ClearBreakpoints()<CR>
    nmap <leader>dq :VimspectorReset<CR>
    nmap <leader>dr <Plug>VimspectorRestart
    nmap <leader>db <Plug>VimspectorToggleBreakpoint
    nmap <leader>dn <Plug>VimspectorStepOver
    nmap <leader>di <Plug>VimspectorStepInto
    nmap <leader>do <Plug>VimspectorStepOut
{{</code>}}

{{<code language="vim" title="~/.vim/config/maximizer.vim" isCollapsed="true">}}
    map <C-W>z :MaximizerToggle<CR>

{{</code>}}

{{<code language="lua" title="~/.vim/config/compe.lua" isCollapsed="true">}}
    vim.o.completeopt = "menuone,noselect"

    require'compe'.setup {
      enabled = true;
      autocomplete = true;
      debug = false;
      min_length = 1;
      preselect = 'enable';
      throttle_time = 80;
      source_timeout = 200;
      incomplete_delay = 400;
      max_abbr_width = 100;
      max_kind_width = 100;
      max_menu_width = 100;
      documentation = false;

      source = {
        path = true;
        buffer = true;
        calc = true;
        vsnip = true;
        nvim_lsp = true;
        nvim_lua = true;
        spell = true;
        tags = true;
        snippets_nvim = true;
      };
    }
{{</code>}}

{{<code language="lua" title="~/.vim/config/lspfuzzy.lua" isCollapsed="true">}}
    require('lspfuzzy').setup {
      methods = {'textDocument/implementation','textDocument/references'},         -- either 'all' or a list of LSP methods (see below)
      fzf_preview = {          -- arguments to the FZF '--preview-window' option
        'right:+{2}-/2'          -- preview on the right and centered on entry
      },
      fzf_action = {           -- FZF actions
        ['ctrl-t'] = 'tabedit',  -- go to location in a new tab
        ['ctrl-v'] = 'vsplit',   -- go to location in a vertical split
        ['ctrl-x'] = 'split',    -- go to location in a horizontal split
      },
      fzf_modifier = ':~:.',   -- format FZF entries, see |filename-modifiers|
      fzf_trim = true,         -- trim FZF entries
    }
{{</code>}}

### External Config

{{<code language="vim" title="~/.vim/config/autoclose.vim" isCollapsed="true">}}
    "-- AUTOCLOSE --
    "disable autoclose
    inoremap <C-W>' '
    inoremap <C-W>` `
    inoremap <C-W>" "
    inoremap <C-W>( (
    inoremap <C-W>[ [
    inoremap <C-W>{ {
    "autoclose and position cursor to write text inside
    inoremap ' ''<left>
    inoremap ` ``<left>
    inoremap " ""<left>
    inoremap ( ()<left>
    inoremap [ []<left>
    inoremap { {}<left>
    "autoclose with ; and position cursor to write text inside
    inoremap '; '';<left><left>
    inoremap `; ``;<left><left>
    inoremap "; "";<left><left>
    inoremap (; ();<left><left>
    inoremap [; [];<left><left>
    inoremap {; {};<left><left>
    "autoclose with , and position cursor to write text inside
    inoremap ', '',<left><left>
    inoremap `, ``,<left><left>
    inoremap ", "",<left><left>
    inoremap (, (),<left><left>
    inoremap [, [],<left><left>
    inoremap {, {},<left><left>
    "autoclose and position cursor after
    inoremap '<tab> ''
    inoremap `<tab> ``
    inoremap "<tab> ""
    inoremap (<tab> ()
    inoremap [<tab> []
    inoremap {<tab> {}
    "autoclose with ; and position cursor after
    inoremap ';<tab> '';
    inoremap `;<tab> ``;
    inoremap ";<tab> "";
    inoremap (;<tab> ();
    inoremap [;<tab> [];
    inoremap {;<tab> {};
    "autoclose with , and position cursor after
    inoremap ',<tab> '',
    inoremap `,<tab> ``,
    inoremap ",<tab> "",
    inoremap (,<tab> (),
    inoremap [,<tab> [],
    inoremap {,<tab> {},
    "autoclose 2 lines below and position cursor in the middle
    inoremap '<CR> '<CR>'<ESC>O
    inoremap `<CR> `<CR>`<ESC>O
    inoremap "<CR> "<CR>"<ESC>O
    inoremap (<CR> (<CR>)<ESC>O
    inoremap [<CR> [<CR>]<ESC>O
    inoremap {<CR> {<CR>}<ESC>O
    "autoclose 2 lines below adding ; and position cursor in the middle
    inoremap ';<CR> '<CR>';<ESC>O
    inoremap `;<CR> `<CR>`;<ESC>O
    inoremap ";<CR> "<CR>";<ESC>O
    inoremap (;<CR> (<CR>);<ESC>O
    inoremap [;<CR> [<CR>];<ESC>O
    inoremap {;<CR> {<CR>};<ESC>O
    "autoclose 2 lines below adding , and position cursor in the middle
    inoremap ',<CR> '<CR>',<ESC>O
    inoremap `,<CR> `<CR>`,<ESC>O
    inoremap ",<CR> "<CR>",<ESC>O
    inoremap (,<CR> (<CR>),<ESC>O
    inoremap [,<CR> [<CR>],<ESC>O
    inoremap {,<CR> {<CR>},<ESC>O

{{</code>}}

{{<code language="vim" title="~/.vim/config/custom_map.vim" isCollapsed="true">}}
    map <leader>`  :NERDTreeToggle<CR>
    map <leader>~  :NERDTreeFind<CR>

    nnoremap  <silent>   <tab>  :if &modifiable && !&readonly && &modified <CR> :write<CR> :endif<CR>:bnext<CR>
    nnoremap  <silent> <s-tab>  :if &modifiable && !&readonly && &modified <CR> :write<CR> :endif<CR>:bprevious<CR>

    nnoremap <C-J> <C-W><C-J>
    nnoremap <C-K> <C-W><C-K>
    nnoremap <C-L> <C-W><C-L>
    nnoremap <C-H> <C-W><C-H>

    nnoremap <silent> <C-W><Up>     :exe "resize " . (winheight(0) * 3/2)<CR>
    nnoremap <silent> <C-W><Down>   :exe "resize " . (winheight(0) * 2/3)<CR>
    nnoremap <silent> <C-W><Left>   :exe "vert resize " . (winwidth(0) * 2/3)<CR>
    nnoremap <silent> <C-W><Right>  :exe "vert resize " . (winwidth(0) * 3/2)<CR>
{{</code>}}

{{<code language="vim" title="~/.vim/config/session.vim" isCollapsed="true">}}
    function GetSessionFileName() abort
      if len(argv()) > 0
        return substitute(fnamemodify(argv()[0], ':p:h'), "/", "_", "g")
      endif
      return substitute(fnamemodify(getcwd(), ':p:h'), "/", "_", "g")
    endfunction!

    let g:session_path = join(['~/.vim/sessions/', GetSessionFileName(), '.vim'], '')

    " Save session on quitting Vim
    autocmd VimLeave * NERDTreeClose
    autocmd VimLeave * execute 'mksession!' g:session_path

    " Restore session function
    function RestoreSession() abort
        execute 'source' g:session_path
        autocmd VimEnter * NERDTree
    endfunction!

    map <leader>r  :call RestoreSession()<CR>
{{</code>}}

## Custom Shortcut List

| Shortcut | Action                                           |
|----------|--------------------------------------------------|
| \\`      | Toggle NerdTree                                  |
| \\~      | Toggle NerdTree focus on current file            |
| \r       | Restore Sessions                                 |
| \ww      | Open vimwiki                                     |
| \dd      | Open debug session / continue to next breakpoint |
| \db      | Toggle debug breakpoint                          |
| \dq      | Quit debug session                               |
| \dc      | Clear all debug breakpoint                       |
| \dr      | Restart debug                                    |
| \dn      | Step over (next line)                            |
| \di      | Step into (function)                             |
| \do      | Step out (function)                              |

[1]: https://levelup.gitconnected.com/tweak-your-vim-as-a-powerful-ide-fcea5f7eff9c
[2]: https://neovim.io/roadmap/
[3]: https://github.com/autozimu/LanguageClient-neovim
[4]: https://github.com/ChristianChiarulli/LunarVim

**References**
1. https://levelup.gitconnected.com/tweak-your-vim-as-a-powerful-ide-fcea5f7eff9c
2. https://neovim.io/roadmap/
3. https://github.com/autozimu/LanguageClient-neovim
4. https://github.com/ChristianChiarulli/LunarVim
