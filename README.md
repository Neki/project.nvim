# 🗃️ project.nvim

**project.nvim** is an all in one neovim plugin written in lua that provides
superior project management.

This is a fork of the [original plugin](https://github.com/ahmedkhalf/project.nvim) with some additional
features, since the upstream repository seems unmaintained.
Notably, this version supports deactivating the automatic working directory update when opening a project
file.

![Telescope Integration](https://user-images.githubusercontent.com/36672196/129409509-62340f10-4dd0-4c1a-9252-8bfedf2a9945.png)

## ⚡ Requirements

- Neovim >= 0.5.0

## ✨ Features

- Automatically detect new project roots when opening a file
  - Detection methods: nvim built-in LSP (no dependency on lspconfig), file patterns
- Telescope integration `:Telescope projects`
  - Access your recently opened projects from telescope.
  - Asynchronous file I/O: will not slow down vim when reading the history
    file on startup.
- Automatically change the current working directory when opening a project

## 📦 Installation

Install the plugin with your preferred package manager:

### [Lazy](https://github.com/folke/lazy.nvim)

```lua
{
    "Neki/project.nvim",
    main="project_nvim",
    opts = {},
    event = "VeryLazy",
}
```

### [vim-plug](https://github.com/junegunn/vim-plug)

```vim
Plug 'Neki/project.nvim'

lua << EOF
  require("project_nvim").setup {
    -- your configuration comes here
    -- or leave it empty to use the default settings
    -- refer to the configuration section below
  }
EOF
```

### [packer](https://github.com/wbthomason/packer.nvim)

```lua
use {
  "Neki/project.nvim",
  config = function()
    require("project_nvim").setup {
      -- your configuration comes here
      -- or leave it empty to use the default settings
      -- refer to the configuration section below
    }
  end
}
```

## ⚙️ Configuration

**project.nvim** comes with the following defaults:

```lua
M.defaults = {
  -- If set to true, don't automatically detect the current project root.
  -- When manual mode is enabled:
  -- 1. The working directory isn't changed when you open a file inside an existing project.
  --    Use :ProjectRoot to manually change the working directory to the project root.
  -- 2. New projects are not automatically added when opening a file. Use :AddProject to
  --    manually add a project.
  manual_mode = false,

  -- When opening a file, automatically change the current working directory to the project root.
  -- If set to false, you can manually change the working directory using the :ProjectRoot command.
  auto_sync_cwd = true,

  -- Methods of detecting the root directory. **"lsp"** uses the native neovim
  -- lsp, while **"pattern"** uses vim-rooter like glob pattern matching. Here
  -- order matters: if one is not detected, the other is used as fallback. You
  -- can also delete or rearangne the detection methods.
  detection_methods = { "lsp", "pattern" },

  -- All the patterns used to detect root dir, when **"pattern"** is in
  -- detection_methods
  patterns = { ".git", "_darcs", ".hg", ".bzr", ".svn", "Makefile", "package.json" },

  -- Table of lsp clients to ignore by name
  -- eg: { "efm", ... }
  ignore_lsp = {},

  -- Don't calculate root dir on specific directories
  -- Ex: { "~/.cargo/*", ... }
  exclude_dirs = {},

  -- Show hidden files in telescope
  show_hidden = false,

  -- When set to false, you will get a message when project.nvim changes your
  -- directory.
  silent_chdir = true,

  -- What scope to change the directory, valid options are
  -- * global (default)
  -- * tab
  -- * win
  scope_chdir = 'global',

  -- Path where project.nvim will store the project history for use in
  -- telescope
  datapath = vim.fn.stdpath("data"),
}
```

Even if you are pleased with the defaults, please note that `setup {}` must be
called for the plugin to start.

### Pattern Matching

**project.nvim**'s pattern engine uses the same expressions as `vim-rooter`:

To specify the root is a certain directory, prefix it with `=`.

```lua
patterns = { "=src" }
```

To specify the root has a certain directory or file (which may be a glob), just
give the name:

```lua
patterns = { ".git", "Makefile", "*.sln", "build/env.sh" }
```

To specify the root has a certain directory as an ancestor (useful for
excluding directories), prefix it with `^`:

```lua
patterns = { "^fixtures" }
```

To specify the root has a certain directory as its direct ancestor / parent
(useful when you put working projects in a common directory), prefix it with
`>`:

```lua
patterns = { ">Latex" }
```

To exclude a pattern, prefix it with `!`.

```lua
patterns = { "!.git/worktrees", "!=extras", "!^fixtures", "!build/env.sh" }
```

List your exclusions before the patterns you do want.

### Telescope Integration

To enable telescope integration:
```lua
require('telescope').load_extension('projects')
```

#### Telescope Projects Picker
To use the projects picker
```lua
require'telescope'.extensions.projects.projects{}
```

#### Telescope mappings

**project.nvim** comes with the following mappings:

| Normal mode | Insert mode | Action                     |
| ----------- | ----------- | -------------------------- |
| f           | \<c-f\>     | find\_project\_files       |
| b           | \<c-b\>     | browse\_project\_files     |
| d           | \<c-d\>     | delete\_project            |
| s           | \<c-s\>     | search\_in\_project\_files |
| r           | \<c-r\>     | recent\_project\_files     |
| w           | \<c-w\>     | change\_working\_directory |

## API

Get a list of recent projects:

```lua
local project_nvim = require("project_nvim")
local recent_projects = project_nvim.get_recent_projects()

print(vim.inspect(recent_projects))
```

## 🤝 Contributing

Pull requests are welcome, but I make no guarantee about timely responses.

## Credits

Thanks to [@ahmedkhalf](https://github.com/ahmedkhalf) for the original plugin on which this fork is based.
