+++
author = "sin1111yi"
title = "配置我的Neovim.part2"
date = "2024-02-22"
description = "添砖加瓦。"
tags = [
    "neovim",
]
series = ["Neovim Config"]
+++

从零开始配置我的neovim。第二部分：基于lazy.nvim加载插件，说明我的插件加载方式。
<!--more-->

- [插件管理器 lazy.nvim](#插件管理器-lazynvim)
- [我的 bootstrap.lua](#我的-bootstraplua)
  - [我的插件加载逻辑](#我的插件加载逻辑)


## 插件管理器 lazy.nvim

这个是 lazy.nvim 的git仓库[https://github.com/folke/lazy.nvim](https://github.com/folke/lazy.nvim)， 要注意区分 LazyVim 和 lazy.nvim，前者是一个基于后者的neovimi预配置，并且使用后者加载；后者只是一个单纯的插件管理器。

先看 lazy.nvim 仓库的 readme，后面为了方便，我简写成lazy。可以看到，已经给出了安装方法，只要在最开始的时候执行下面 lua 代码即可，这是当然的，如果想通过插件管理器去加载插件，那么首先要做的就是先安装插件管理器。

```lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)
```

上面的代码中，通过 `vim.fn.stdpath()` 方法，找到了 neovim 的 data 路径，这个路径通常是 `$HOME/.local/share/nvim`，随后使用了 lua 中字符串拼接的语法，将 lazypath 定义为 `$HOME/.local/share/nvim/lazy/lazy.nvim`，目的是为了在后面通过 `vim.loop.fs_stat()` 方法来判断是否存在该路径，如果该路径存在，就认为lazy已经被下载了，并且可以使用，否则就通过 `vim.fn.system` 执行系统命令从 github 拉取 lazy，最后将 lazypath 加入 runtimepath。

```bash
git clone --filter=blob:none https://github.com/folke/lazy.nvim.git --branch=stable $HOME/.local/share/nvim/lazy/lazy.nvim
```

完成这些后，lazy 已经被拉取到了对应的目录下，并且由于已经被加入了 runtimepath，其中的方法也可以被调用，所以就可以加入下面一行来通过 lazy 来加载 plugins 和通过 opts 对 lazy 自身进行配置。

```lua
require("lazy").setup(plugins, opts)
```

关于 lazy 的进一步配置，如 opts 的内容，请移步仓库的 readme，你可以找到所有你想知道的问题的答案。

## 我的 bootstrap.lua

先把代码放在这里，随后慢慢讲。

```lua
local M = {}

local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
    vim.fn.system({
        "git",
        "clone",
        "--filter=blob:none",
        "https://github.com/folke/lazy.nvim.git",
        "--branch=stable", -- latest stable release
        lazypath
    })
end
vim.opt.rtp:prepend(lazypath)

local Util = require("core.util")

---@param name "autocmds" | "options" | "keymaps"
local load = function(name)
    local function _load(mod)
        if require("lazy.core.cache").find(mod)[1] then
            Util.try(function()
                require(mod)
            end, { msg = "Failed loading " .. mod })
        end
    end

    _load("core." .. name)
    if vim.bo.filetype == "lazy" then
        -- HACK: LazyVim may have overwritten options of the Lazy ui, so reset this here
        vim.cmd([[do VimResized]])
    end
    local pattern = "s1sVim" .. name:sub(1, 1):upper() .. name:sub(2)
    vim.api.nvim_exec_autocmds("User", { pattern = pattern, modeline = false })
end

---@class PluginsLoadOpts
local pluginsConf = {
    ---@type table<string, boolean>
    load_modules = {
        ["support"] = true,
        ["colorscheme"] = true,
        ["ui"] = true,
        ["coding"] = true,
        ["coding.support"] = true,

        ["extra"] = false,
        ["custom"] = false,
    },

    ---@type string[]
    disbaled_plugins = {
        -- for example, uncomment this line to let lazy ignore neodev
        -- "folke/neodev.nvim",
    },
}

local opts = {
    defaults = {
        lazy = true,
        version = "*" -- always use the latest git commit
    },

    checker = { enabled = true }, -- automatically check for plugin updates

    performance = {
        disbaled_plugins = {
            "gzip",
            "matchit",
            "matchparen",
            "netrwPlugin",
            "tarPlugin",
            "tohtml",
            "tutor",
            "zipPlugin",
        }
    }
}

local create_usr_cmds = function()
    vim.api.nvim_create_user_command("LazyHealth", function()
        vim.cmd("Lazy! load all")
        vim.cmd("checkhealth")
    end, { desc = "Load all plugins and run :checkhealth" })

    vim.api.nvim_create_user_command("UpdateAll", function()
        vim.cmd("TSUpdate all")
        vim.cmd("MasonUpdate")
    end, { bang = true, nargs = 0, desc = "Update all" })
end

M.setup = function()
    load("options")
    Util.lazy_notify()
    Util.format.setup()
    Util.plugin.setup()

    require("lazy").setup(require("plugins.necessary").setup(pluginsConf), opts)

    load("keymaps")
    load("autocmds")

    create_usr_cmds()

    Util.ui.set_colorscheme("catppuccin")
end

return M
```

这里我将bootstrap定义为了一个模块，如果不明白是什么意思可以回看 part1 来复习。

我们将上面的代码简化来看，首先 Util 模块是直接搬运自 LazyVim 的，可以直接使用，不用太过深究实现方法，对neovim熟悉后，再去看着部分也可以。

后面的 load 方法也是搬运自LazyVim，这里通过 *注解* 限定了传入参数只能是 `"autocmds"`、`"options"`、`"keymaps"` 中的一个，实际上实现的功能就是加载这三个模块，不必太过深究，等后面对 lazy 使用次数增多，并且阅读过 LazyVim 的源码后，这部分是很容易理解的。也就是通过这个方法，加载了在 part1 中提到的自动命令、基本配置、按键映射。

下面，定义了 `pluginsConf` 和 `opts` 两个表，第一个表是我自己用来管理插件的，后面会说到，第二个表对 lazy 的配置表，其中的项参考lazy的readme就可以知道具体含义。

随后，定义了 `create_usr_cmds` 方法用来创建用户命令，在后面的过程中，只需要调用一次，就可以创建其中定义的用户方法。

最后，定义了 `setup` 方法，也就是在 part1 中被 init.lua 调用的方法。在这个方法中，首先加载了 `options` 模块，根据要求，这个模块必须在最初加载，随后调用了 Util 中的几个方法，这里可以先不管，只需要知道这是 Util 组件的初始化，搬运自LazyVim即可。最关键的一行是

```lua
require("lazy").setup(require("plugins.necessary").setup(pluginsConf), opts)
```

在这里，调用了上面说过的

```lua
require("lazy").setup(plugins, opts)
```

在 `plugins` 的位置，取而代之的是 `require("plugins.necessary").setup(pluginsConf)`，这就要去看我实现的 `plugins.necessary` 模块中的 `setup` 方法。

最后，设置颜色主题，完成启动。

### 我的插件加载逻辑

```lua
local M = {}

M.plugins_table = {}

---@param opts PluginsLoadOpts
M.setup = function(opts)
    if opts.load_modules ~= nil and next(opts.load_modules) ~= nil then
        ---@param module string
        ---@return boolean
        local function mod_exist(module)
            local mod_path = vim.fn.stdpath("config") .. "/lua/plugins/"
            module = string.gsub(module, "%.", "/")
            return vim.loop.fs_stat(mod_path .. module) ~= nil
        end

        for k, v in pairs(opts.load_modules) do
            if not mod_exist(k) then
                vim.notify("Module \"" .. k .. "\" is not existed!\n")
            end

            if v then
                table.insert(M.plugins_table,
                    { import = "plugins." .. k })
            end
        end
    end

    if opts.disbaled_plugins ~= nil and next(opts.disbaled_plugins) ~= nil then
        for _, p in ipairs(opts.disbaled_plugins) do
            table.insert(M.plugins_table, { p, cond = false })
        end
    end

    return M.plugins_table
end

return M
```

在这个 setup 方法中，传入参数为一个表，也就是在 bootstrap.lua 定义的 pluginsConf，其由两个成员组成

1. load_modules，类型为字符串和布尔值的键值对
2. disbaled_plugins，类型为字符串表

在该方法中，定义了内部方法 `mod_exist` 来判断 `vim.fn.stdpath("config") .. "/lua/plugins/"` 路径中是否有某一模块，如果有并且传入的布尔值为真，则通过 lazy 加载，否则不加载。如果 disbaled_plugins 中有某一模块名，则会通过 lazy 取消该模块的加载。
