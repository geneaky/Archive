``` vim.rc
imap jk <Esc>
imap kj <Esc>

set NERDTree
set multiple-cursors
set commentary
set vim-paragraph-motion
set ignorecase smartcase

map <C-N> <Plug>NextWholeOccurrence
xmap <C-X> <Plug>SkipOccurrence
xmap <C-P> <Plug>RemoveOccurrence

inoremap <C-H> <Esc>bi
inoremap <C-L> <Esc>ea

inoremap <C-S-J> :action EditorCloneCaretBelow<CR>

nnoremap <C-m> :action ToggleBookmark<CR>
nnoremap <C-]> :action GotoNextBookmark<CR>
nnoremap <C-[> :action GotoPreviousBookmark<CR>

map <C-S-c> :action StringManipulation.ToSnakeCaseOrCamelCase
```

