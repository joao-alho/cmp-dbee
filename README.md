# cmp-dbee

[![ci](https://github.com/MattiasMTS/cmp-dbee/actions/workflows/ci.yml/badge.svg)](https://github.com/MattiasMTS/cmp-dbee/actions/workflows/ci.yml)

<!--toc:start-->

- [cmp-dbee](#cmp-dbee)
  - [Showcase](#showcase)
  - [Usage](#usage)
    - [Suggestions](#suggestions)
  - [Installation](#installation)
  <!--toc:end-->

Autocompletion plugin for [nvim-dbee](https://github.com/kndndrj/nvim-dbee/) database client.

> Still very much a WIP plugin => expect some breaking changes.

Feel free to open any issues or PRs as you seem fit!

## Showcase

https://github.com/MattiasMTS/cmp-dbee/assets/86059470/1a0141dd-cb4d-47ce-b510-148d654d9503

## Usage

cmp-dbee is using a little bit of regex but mostly Treesitter to generate
suggestions.

### Suggestions

TL;DR

- schemas
- leaf of schemas (tables, views, functions, etc)
- aliases
- CTEs
- columns: name and dtype (by referencing aliases)

By default, schema + model suggestion is generated whenever a user hits "space".
At this point, the syntax for the SQL query isn't complete and the plugin
therefore uses a little bit of regex to "find/match" the selected schema.

Ones the schemas has been found, the plugin suggests all the "leafs"
for the schema.

CTEs and aliases are suggested based on the cursor position. I.e. if you've
two _complete_ (complete = ended with ';') queries in the buffer. Then, only
the aliases and ctes which the current cursor are nearest will be suggested.

Column suggestion is provided by being explicit. I.e. you'd have to refer the
alias of a table if you want column completion. This is to reduce ambiguity
when there are other suggestions that compete. Column completion provide you
with the current name and the datatype of the column. As of now, only table
leafs provides columns. This might change in the future.

## Installation

- Using **lazy**:

```lua
  {
    "hrsh7th/nvim-cmp",
    dependencies = {
      {
        "MattiasMTS/cmp-dbee",
        dependencies = {
          {"kndndrj/nvim-dbee"}
        },
        ft = "sql", -- optional but good to have
        opts = {}, -- needed
      },
    },
    opts = {
      sources = {
        { "cmp-dbee" },
      },
    },
  }
```

- Using **packer**:

```lua
   use({"hrsh7th/nvim-cmp", requires = {"MattiasMTS/cmp-dbee"}})
   use{
     "kndndrj/nvim-dbee",
      run = function()
        require("dbee").install()
      end,
   }
```


# Bug report

When working on an [Athena DBee adapter](https://github.com/joao-alho/nvim-dbee) (very early stages) I ran into this error a lot. Was easily fixed by applying this fix.
I don't know Lua and was very confused why the previous check for structure being null was not working.
So maybe this is isn't needed at all.

Tested on WSL2, Go version `1.22.5 linux/amd64`, neovim version `v0.11.0-dev`, LuaJIT `2.1.0-beta3` 


```lua

Error detected while processing TextChangedI Autocommands for "*":
Error executing lua callback: ....local/share/nvim/lazy/cmp-dbee/lua/cmp-dbee/handler.lua:121: bad argument #1 to 'ipairs' (table
expected, got nil)
stack traceback:
        [C]: in function 'ipairs'
        ....local/share/nvim/lazy/cmp-dbee/lua/cmp-dbee/handler.lua:121: in function 'get_documentation'
        ....local/share/nvim/lazy/cmp-dbee/lua/cmp-dbee/handler.lua:198: in function 'convert_to_completion_item'
        ....local/share/nvim/lazy/cmp-dbee/lua/cmp-dbee/handler.lua:214: in function 'get_completion'
        .../.local/share/nvim/lazy/cmp-dbee/lua/cmp-dbee/source.lua:29: in function 'complete'
        ...jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/source.lua:326: in function 'complete'
        ...e/jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/core.lua:299: in function 'complete'
        ...e/jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/core.lua:169: in function 'callback'
        ...e/jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/core.lua:229: in function 'autoindent'
        ...e/jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/core.lua:161: in function 'on_change'
        ...e/jalho/.local/share/nvim/lazy/nvim-cmp/lua/cmp/init.lua:346: in function 'callback'
        ...local/share/nvim/lazy/nvim-cmp/lua/cmp/utils/autocmd.lua:49: in function 'emit'
        ...local/share/nvim/lazy/nvim-cmp/lua/cmp/utils/autocmd.lua:23: in function <...local/share/nvim/lazy/nvim-cmp/lua/cmp/uti
ls/autocmd.lua:22>
```

