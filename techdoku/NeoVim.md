# Neovim
## Kürzel

| Tastenkombo | Beschreibung |
| --- | --- |
| `gcc` | ein-/auskommentieren der aktuellen Zeile (normal mode)
| `gc` | Bereich ein-/auskommentieren (visual mode)
| `gcip` | Kombination mit movement ("comment in paragraph")

## Setup
### Plugins

**must-have**
- [x] lazy.nvim (Plugin-Management)
- [x] fuzzy-finder -> fzf-lua (alt.: telescope, mini.pick)
- [x] bufferline (Leiste oben f. offene Buffers)
- [x] theme (TokyoNight?)
- [x] treesitter (besseres Code-Highlighting, Indenting)
- [x] lazygit (snacks)
- [x] yazi (Dateioperationen) -> alt.: oil?

**nice to have**
- [x] highlight-colors (toggle!)
- [x] transparent
- [x] which-key
- [x] autocompletion -> blink.cmp
- [x] Linien am linken Rand -> ok, snacks.indent
- [x] autopairs.nvim + tabout.nvim

**Plugin-Liste aus erstem Setup**

- autopairs
- bufferline
- highlight-colors
- lualine
- ~~neo-tree~~
- snacks
- tabout
- ~~telescope~~
- theme
- transparent
- treesitter
- which-key
- yazi