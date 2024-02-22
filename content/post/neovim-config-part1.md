+++
author = "sin1111yi"
title = "配置我的Neovim.part1"
date = "2024-02-20"
description = "千里之行，始于足下。"
tags = [
    "neovim",
]
series = ["Neovim Config"]

+++

从零开始配置我的neovim。第一部分，配置options，keymaps。
<!--more-->

## 目录

- [目录](#目录)
- [我的Neovim配置](#我的neovim配置)
- [新建文件夹](#新建文件夹)
- [结构说明](#结构说明)
- [开始 init.lua](#开始-initlua)
- [基本配置 options](#基本配置-options)
- [自动命令 autocmds](#自动命令-autocmds)
- [按键映射 keymaps](#按键映射-keymaps)


## 我的Neovim配置

```bash
cp -r ~/.config/nvim ~/.config/nvim.bak
git clone --branch ver.dev https://github.com/sin1111yi/s1sNvim ~/.config/nvim
```

## 新建文件夹
本文的配置基于Arch Linux，其他linux系统类似，如果是Windows，请注意将文中的路径 **$HOME/.config/nvim**，替换为 **C:/User/username/AppData/Local/nvim**。

Neovim的配置文件可以使用Vimscript或者lua编写，我这里使用lua来编写配置文件，主要是看重了它比较容易上手，而且代码可读性更好一些。

如果不使用我的配置文件，而选择从零开始自己编写，那么

1. 在 **$HOME/.config** 路径下创建 **nvim** 文件夹并进入，在后续的过程中，你的配置文件都会在这个文件夹中编写
   ```bash
   mkdir -p $HOME/.config/nvim && cd $HOME/.config/nvim
   ``` 
2. 在 **$HOME/.config/nvim** 中创建 **lua** 文件夹与 **init.lua**文件，Neovim会从init.lua开始加载配置
   ```bash
   mkdir lua && touch init.lua
   ```
至此，你已经完成了最初的工作。

## 结构说明

如果使用了我的neovim配置，那么你将看到在lua文件夹中，我又创建了 **core** 和 **plugins** 两个文件夹。

我在 core 中存放了一些不依赖于其他插件的功能，并直接搬运了 LazyVim 的一部分 util 组件。此外，我将需要依赖插件的一些功能封装和插件的快捷键整理后写在了 core/plugins 中，以此来方便我修改和查阅快捷键。

截至发布日期我的neovim配置的目录结构如下所示，在 core 中的 lua 文件除 bootstrap.lua 外就是一些基础配置。plugins 目录中都是安装与配置插件的文件，在本文中不会讲到。

```bash
├── init.lua
├── lazy-lock.json
├── LICENSE
├── lua
│   ├── core
│   │   ├── autocmds.lua
│   │   ├── bootstrap.lua
│   │   ├── keymaps.lua
│   │   ├── options.lua
│   │   ├── plugins
│   │   │   ├── exapi.lua
│   │   │   └── keymaps.lua
│   │   └── util
│   │       ├── format.lua
│   │       ├── init.lua
│   │       ├── inject.lua
│   │       ├── lsp.lua
│   │       ├── plugin.lua
│   │       ├── root.lua
│   │       ├── telescope.lua
│   │       ├── toggle.lua
│   │       └── ui.lua
│   └── plugins
│       ├── coding
│       │   ├── comment.lua
│       │   ├── git.lua
│       │   ├── indent-blankline.lua
│       │   ├── navigate.lua
│       │   ├── support
│       │   │   ├── cmp.lua
│       │   │   ├── formatter.lua
│       │   │   └── lspconfig.lua
│       │   └── tree-sitter.lua
│       ├── colorscheme
│       │   ├── catppuccin.lua
│       │   └── tokyonight.lua
│       ├── custom
│       │   └── custom.lua
│       ├── extra
│       │   └── neodev.lua
│       ├── necessary.lua
│       ├── support
│       │   ├── plenary.lua
│       │   ├── telescope.lua
│       │   └── which-key.lua
│       └── ui
│           ├── beauty.lua
│           ├── bufferline.lua
│           ├── dressing.lua
│           ├── lualine.lua
│           ├── navic.lua
│           ├── neotree.lua
│           ├── noice.lua
│           ├── notify.lua
│           └── startup.lua
└── README.md
```

## 开始 init.lua

init.lua 是 neovim 在加载配置时最先加载的脚本，理论上我们可以把所有的配置都放在这里，但是这样就没有美感，不是一个程序员该做的事情。

我的 init.lua 只有一行，调用了 core.bootstrap 模块中的 setup 方法。这个模块涉及到了插件加载等步骤，先按下不表。

```lua
require("core.bootstrap").setup()
```

## 基本配置 options
一般来说，官方文档是最好的文档，尽管neovim的官方文档不是特别清晰，但仍然可以满足绝大部分需要，这里是[链接](https://neovim.io/doc/user/)。

在Neovim中，有很多配置不需要以来插件即可实现，我直接照搬了LazyVim的基本配置，并做了一些小的修改。LazyVim是很好的Neovim预配置，使用了插件的加载方式，而不是直接将配置文件写入我们在[新建文件夹](#新建文件夹)中创建的配置文件夹。这里是它的[官网](https://www.lazyvim.org/)。

这里是[我的基本配置](https://github.com/sin1111yi/s1sNvim/blob/ver.dev/lua/core/options.lua)，你也可以直接克隆我的仓库。

```lua
vim.g.mapleader = " "
vim.g.maplocalleader = " "

vim.g.autoformat = true

vim.g.root_spec = { "lsp", { ".git", "lua" }, "cwd" }

local opt = vim.opt

opt.autowrite = true           -- Enable auto write
opt.clipboard = "unnamedplus"  -- Sync with system clipboard
opt.completeopt = "menu,menuone,noselect"
opt.conceallevel = 3           -- Hide * markup for bold and italic
opt.confirm = true             -- Confirm to save changes before exiting modified buffer
opt.cursorline = true          -- Enable highlighting of the current line
opt.expandtab = true           -- Use spaces instead of tabs
opt.formatoptions = "jcroqlnt" -- tcqj
opt.grepformat = "%f:%l:%c:%m"
opt.grepprg = "rg --vimgrep"
opt.ignorecase = true      -- Ignore case
opt.inccommand = "nosplit" -- preview incremental substitute
opt.laststatus = 3         -- global statusline
opt.list = true            -- Show some invisible characters (tabs...
opt.mouse = "a"            -- Enable mouse mode
opt.number = true          -- Print line number
opt.pumblend = 10          -- Popup blend
opt.pumheight = 10         -- Maximum number of entries in a popup
opt.relativenumber = true  -- Relative line numbers
opt.scrolloff = 4          -- Lines of context
opt.sessionoptions = { "buffers", "curdir", "tabpages", "winsize", "help", "globals", "skiprtp", "folds" }
opt.shiftround = true      -- Round indent
opt.shiftwidth = 4         -- Size of an indent
opt.shortmess:append({ W = true, I = true, c = true, C = true })
opt.showmode = false       -- Dont show mode since we have a statusline
opt.sidescrolloff = 8      -- Columns of context
opt.signcolumn = "yes"     -- Always show the signcolumn, otherwise it would shift the text each time
opt.smartcase = true       -- Don't ignore case with capitals
opt.smartindent = true     -- Insert indents automatically
opt.spelllang = { "en" }
opt.splitbelow = true      -- Put new windows below current
opt.splitkeep = "screen"
opt.splitright = true      -- Put new windows right of current
opt.tabstop = 4            -- Number of spaces tabs count for
opt.termguicolors = true   -- True color support
opt.timeoutlen = 300
opt.undofile = true
opt.undolevels = 10000
opt.updatetime = 200               -- Save swap file and trigger CursorHold
opt.virtualedit = "block"          -- Allow cursor to move where there is no text in visual block mode
opt.wildmode = "longest:full,full" -- Command-line completion mode
opt.winminwidth = 5                -- Minimum window width
opt.wrap = true                    -- Enable line wrap
opt.fillchars = {
    foldopen = "",
    foldclose = "",
    fold = "⸱",
    foldsep = " ",
    diff = "/",
}
opt.listchars = {
    tab = " ~~ ",
    eol = "󱞣",
    trail = "-",
}

if vim.fn.has("nvim-0.10") == 1 then
    opt.smoothscroll = true
end

-- Folding
vim.opt.foldlevel = 99
vim.opt.foldtext = "v:lua.require'core.util'.fold_text()"

if vim.fn.has("nvim-0.9.0") == 1 then
    vim.opt.statuscolumn = [[%!v:lua.require'core.util.ui'.statuscolumn()]]
end

if vim.fn.has("nvim-0.10") == 1 then
    vim.opt.foldmethod = "expr"
    vim.opt.foldexpr = "v:lua.vim.treesitter.foldexpr()"
else
    vim.opt.foldmethod = "indent"
end

vim.g.markdown_recommended_style = 0
```

值得注意的是前两行：
```lua
vim.g.mapleader = " "
vim.g.maplocalleader = " "
```
在这里，我将 neovim 的 leader 键设置为了 space，这意味着后续在使用 leader 键触发的快捷键时，按下空格即可。其他没有用注释说明的配置的含义可以在官方文档搜索找到。

## 自动命令 autocmds

是否想做一些自动化的事情？比如在退出插入模式时自动保存当前的buffer，那么只需要使用 **vim.api.nvim_create_autocmd** 来创建一个 autocmd 即可
```lua
-- Auto save buffer when leaving buffer or text changed
vim.api.nvim_create_autocmd({ "InsertLeave", "TextChanged" }, {
    group = augroup("autosave"),
    pattern = { "*" },
    command = "silent! wall",
    nested = true
})
```
这里先来看一下 **vim.api.nvim_create_autocmd** 的用法，neovim的api查询与用法可以在官方文档的api一章中找到，这里是[链接](https://neovim.io/doc/user/api.html#api)。

首先给出请查看该api的官方说明，这里是[链接](https://neovim.io/doc/user/api.html#nvim_create_autocmd())。简单的说，就是需要给这个api传入两个lua table，第一个table是触发该autocmd的event，所有的autocmd-events可以在[这里](https://neovim.io/doc/user/autocmd.html#autocmd-events)找到；第二个table是该autocmd需要执行的动作以及一些配置，上文中提到的自动保存buffer的autocmd即是执行了 **silent! wall** 来实现保存，触发该动作的event为 **InsertLeave** 和 **TextChanged**。

## 按键映射 keymaps

这里我也几乎照搬了LazyVim的按键映射，对于最基础的按键映射，大部分人的配置区别实际上不大，所以可以先随意使用已有的映射，在后续的使用过程中逐渐修改就可以，但是请不要忘记感谢做出贡献的人。下面是我的 **keymaps.lua**

```lua
local M = {}

local Util = require("core.util")

local vmap = Util.better_nvim_keymap_set
local smap = Util.safe_keymap_set

-- Move to window using the <ctrl> hjkl keys
smap("n", "<C-h>", "<C-w>h", { desc = "Go to left window" })
smap("n", "<C-j>", "<C-w>j", { desc = "Go to lower window" })
smap("n", "<C-k>", "<C-w>k", { desc = "Go to upper window" })
smap("n", "<C-l>", "<C-w>l", { desc = "Go to right window" })

-- Resize window using <ctrl> arrow keys
smap("n", "<C-Up>", "<cmd>resize +2<cr>", { desc = "Increase window height" })
smap("n", "<C-Down>", "<cmd>resize -2<cr>", { desc = "Decrease window height" })
smap("n", "<C-Left>", "<cmd>vertical resize -2<cr>", { desc = "Decrease window width" })
smap("n", "<C-Right>", "<cmd>vertical resize +2<cr>", { desc = "Increase window width" })

-- Move Lines
smap("n", "<A-j>", "<cmd>m .+1<cr>==", { desc = "Move down" })
smap("n", "<A-k>", "<cmd>m .-2<cr>==", { desc = "Move up" })
smap("i", "<A-j>", "<esc><cmd>m .+1<cr>==gi", { desc = "Move down" })
smap("i", "<A-k>", "<esc><cmd>m .-2<cr>==gi", { desc = "Move up" })
smap("v", "<A-j>", ":m '>+1<cr>gv=gv", { desc = "Move down" })
smap("v", "<A-k>", ":m '<-2<cr>gv=gv", { desc = "Move up" })

-- buffers
if Util.has("bufferline.nvim") then
    smap("n", "<S-h>", "<cmd>BufferLineCyclePrev<cr>", { desc = "Prev buffer" })
    smap("n", "<S-l>", "<cmd>BufferLineCycleNext<cr>", { desc = "Next buffer" })
    smap("n", "[b", "<cmd>BufferLineCyclePrev<cr>", { desc = "Prev buffer" })
    smap("n", "]b", "<cmd>BufferLineCycleNext<cr>", { desc = "Next buffer" })
else
    smap("n", "<S-h>", "<cmd>bprevious<cr>", { desc = "Prev buffer" })
    smap("n", "<S-l>", "<cmd>bnext<cr>", { desc = "Next buffer" })
    smap("n", "[b", "<cmd>bprevious<cr>", { desc = "Prev buffer" })
    smap("n", "]b", "<cmd>bnext<cr>", { desc = "Next buffer" })
end

-- Clear search with <esc>
smap({ "i", "n" }, "<esc>", "<cmd>noh<cr><esc>", { desc = "Escape and clear hlsearch" })

-- https://github.com/mhinz/vim-galore#saner-behavior-of-n-and-n
smap("n", "n", "'Nn'[v:searchforward]", { expr = true, desc = "Next search result" })
smap("x", "n", "'Nn'[v:searchforward]", { expr = true, desc = "Next search result" })
smap("o", "n", "'Nn'[v:searchforward]", { expr = true, desc = "Next search result" })
smap("n", "N", "'nN'[v:searchforward]", { expr = true, desc = "Prev search result" })
smap("x", "N", "'nN'[v:searchforward]", { expr = true, desc = "Prev search result" })
smap("o", "N", "'nN'[v:searchforward]", { expr = true, desc = "Prev search result" })

-- windows
smap("n", "<leader>ww", "<C-W>p", { desc = "Other window", remap = true })
smap("n", "<leader>wd", "<C-W>c", { desc = "Delete window", remap = true })
smap("n", "<leader>w-", "<C-W>s", { desc = "Split window below", remap = true })
smap("n", "<leader>w|", "<C-W>v", { desc = "Split window right", remap = true })
smap("n", "<leader>-", "<C-W>s", { desc = "Split window below", remap = true })
smap("n", "<leader>|", "<C-W>v", { desc = "Split window right", remap = true })

-- tabs
smap("n", "<leader><tab>l", "<cmd>tablast<cr>", { desc = "Last Tab" })
smap("n", "<leader><tab>f", "<cmd>tabfirst<cr>", { desc = "First Tab" })
smap("n", "<leader><tab><tab>", "<cmd>tabnew<cr>", { desc = "New Tab" })
smap("n", "<leader><tab>]", "<cmd>tabnext<cr>", { desc = "Next Tab" })
smap("n", "<leader><tab>d", "<cmd>tabclose<cr>", { desc = "Close Tab" })
smap("n", "<leader><tab>[", "<cmd>tabprevious<cr>", { desc = "Previous Tab" })

smap("t", "<esc>", "<c-\\><c-n>", { desc = "Escape terminal insert" })

require("which-key").register({
    q = {
        name = "+Quit",
        c = { "<cmd>q<cr>", "Quit" },
        w = { "<cmd>wq<cr>", "Save & Quit" },
        a = { "<cmd>qa<cr>", "Quit all" },
        q = { "<cmd>wqa<cr>", "Save & Quit all" },
    },

    r = {
        name = "+Built-in",
        n = {
            function()
                if vim.o.relativenumber == true then
                    vim.cmd("set norelativenumber")
                else
                    vim.cmd("set relativenumber")
                end
                Util.info("Toggle relativenumber")
            end, "Toggle relativenumber"
        }
    },

    w = {
        name = "+Window"
    }
}, { prefix = "<leader>" })

require("core.plugins.keymaps")

return M
```

在上面的代码中，可以看到，我将该文件定义为了一个module，即
```lua
local M = {}

-- do something or define some functions, variables for M

return M
```

这样一来，当我们在这个文件外对这个module进行require时，其中的代码就会执行。

```lua
-- code part1
require("keymaps") -- "keymaps" is the name of module 
-- code part2
```

当执行code part1时，keymaps还没有被引入，因此keymaps中所定义的映射在此时均是无效的，当执行code part2时，keymaps已经完成了引入，此时keymaps中的映射就都可以使用了。

此时进行一个举一反三，如果keymaps中定义的不是按键映射，而是其他配置，比如之前提到的ooptions或者autocmds，那么效果也是和刚才描述的一样。实际上我们的配置都是一段一段的lua代码，有些是赋值，如options，有些是调用neovim的api，因此在我们导入它们之前，即执行这些语句前，都是不会生效的。这就要求我们对自己配置加载顺序做出一定的考虑。

而在module中，可以定义module的方法，如
```lua
local M = {}

M.t1 = {}

M.setup = function(opts)
    -- do something
end

return M
```
在上面的代码中，假定模块名为m1，为该模块定义了一个table成员t1，一个function成员setup。这时如果想执行setup中的语句，就需要
```lua
local opts = {}
local m1 = require("m1")
m1.setup(opts)
```
或
```lua
local opts = {}
require("m1").setup(opts)
```
来直接调用，并传入参数，对于参数类型的指定以及可变参数会在后面提到。

回到keymaps.lua文件，我使用了刚才说到的引入模块的方法，引入了 **core.util** 模块并通过 **Util** 来调用它，然后使用了其中的两个方法，这两个方法现在可以先不关心，只需要知道都是对原有的keymap函数的二次封装即可
```lua
local Util = require("core.util")

local vmap = Util.better_nvim_keymap_set
local smap = Util.safe_keymap_set
```

neovim原有的api如下所示，这里是官方文档的[说明](https://neovim.io/doc/user/lua.html#vim.keymap)

```lua
vim.keymap.set(mode, lhs, rhs, opts)
```

随后，我使用 **vmap** 或 **smap** 映射了一些按键，在最后又通过刚才说过的引入模块的方法和调用模块成员函数的方法，引入了名为 **which-key** 的模块，并调用了其中的 **register** 方法
```lua
require("which-key").register({
    --- something
})
```
这里的 **which-key** 是neovim的一个插件，关于插件的内容在后面会讲到。
