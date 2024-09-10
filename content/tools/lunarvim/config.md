---
tags:
  - config
---
```lua
-- vim options
vim.opt.shiftwidth = 2
vim.opt.tabstop = 2
vim.opt.relativenumber = true

vim.api.nvim_set_keymap("n", "<Leader>ai", "<cmd>lua vim.lsp.buf.code_action()<CR>", { noremap = true, silent = true })

lvim.log.level = "info"
lvim.format_on_save = {
  enabled = true,
  timeout = 1000,
}

lvim.leader = "space"
-- add your own keymapping
lvim.keys.normal_mode["<C-s>"] = ":w<cr>"

lvim.builtin.alpha.active = true
lvim.builtin.alpha.mode = "dashboard"
lvim.builtin.terminal.active = true
lvim.builtin.nvimtree.setup.view.side = "left"
lvim.builtin.nvimtree.setup.renderer.icons.show.git = false

lvim.builtin.treesitter.auto_install = true
lvim.builtin.treesitter.autotag.enable = true

local formatters = require "lvim.lsp.null-ls.formatters"
formatters.setup {
  {
    command = "prettier",
    extra_args = { "--print-width", "80", "--tab-width", "2" },
    filetypes = { "css", "json", "typescript", "typescriptreact", "javascript", "javascriptreact", "yaml" },
  },
}


local code_actions = require "lvim.lsp.null-ls.code_actions"
code_actions.setup {
  {
    name = "proselint",
  },
}



-- lvim.keys.normal_mode["<leader>xx"] = {
--   "<cmd>Trouble diagnostics toggle<cr>",
--   silent = true,
--   noremap = true,
-- }
-- lvim.builtin.which_key.mappings["<leader>xx"] = {
--   name = "Diagnostics",
--   t = { "<cmd>TroubleToggle<cr>", "trouble" },
--   w = { "<cmd>TroubleToggle workspace_diagnostics<cr>", "workspace" },
--   d = { "<cmd>TroubleToggle document_diagnostics<cr>", "document" },
--   q = { "<cmd>TroubleToggle quickfix<cr>", "quickfix" },
--   l = { "<cmd>TroubleToggle loclist<cr>", "loclist" },
--   r = { "<cmd>TroubleToggle lsp_references<cr>", "references" },
-- }

lvim.keys.normal_mode["<leader>xw"] = {
  "[[:let @+ = substitute(expand('%'), getcwd() . '/', '', '')<CR>]]",
  silent = true,
  noremap = true,
}

lvim.builtin.telescope.on_config_done = function(telescope)
  pcall(telescope.load_extension, "frecency")
  pcall(telescope.load_extension, "neoclip")
  -- any other extensions loading
end


lvim.plugins = {
  -- {
  --   'wfxr/minimap.vim',
  --   build = "cargo install --locked code-minimap",
  --   config = function()
  --     vim.cmd("let g:minimap_width = 10")
  --     vim.cmd("let g:minimap_auto_start = 1")
  --     vim.cmd("let g:minimap_auto_start_win_enter = 1")
  --   end,
  -- },
  {
    "folke/persistence.nvim",
    event = "BufReadPre", -- this will only start session saving when an actual file was opened
    -- module = "persistence",
    config = function()
      require("persistence").setup {
        dir = vim.fn.expand(vim.fn.stdpath "config" .. "/session/"),
        options = { "buffers", "curdir", "tabpages", "winsize" },
      }
    end,
  },
  {
    "tpope/vim-surround",
    -- make sure to change the value of `timeoutlen` if it's not triggering correctly, see https://github.com/tpope/vim-surround/issues/117
    -- setup = function()
    --  vim.o.timeoutlen = 500
    -- end
  },
  {
    "f-person/git-blame.nvim",
    event = "BufRead",
    config = function()
      vim.cmd "highlight default link gitblame SpecialComment"
      vim.g.gitblame_enabled = 0
    end,
  },
  {
    "folke/todo-comments.nvim",
    event = "BufRead",
    config = function()
      require("todo-comments").setup()
    end,
  },
  {
    "folke/trouble.nvim",
    opts = {}, -- for default options, refer to the configuration section for custom setup.
    cmd = "Trouble",
    keys = {
      {
        "<leader>xx",
        "<cmd>Trouble diagnostics toggle<cr>",
        desc = "Diagnostics (Trouble)",
      },
      {
        "<leader>xX",
        "<cmd>Trouble diagnostics toggle filter.buf=0<cr>",
        desc = "Buffer Diagnostics (Trouble)",
      },
      {
        "<leader>cs",
        "<cmd>Trouble symbols toggle focus=false<cr>",
        desc = "Symbols (Trouble)",
      },
      {
        "<leader>cl",
        "<cmd>Trouble lsp toggle focus=false win.position=right<cr>",
        desc = "LSP Definitions / references / ... (Trouble)",
      },
      {
        "<leader>xL",
        "<cmd>Trouble loclist toggle<cr>",
        desc = "Location List (Trouble)",
      },
      {
        "<leader>xQ",
        "<cmd>Trouble qflist toggle<cr>",
        desc = "Quickfix List (Trouble)",
      },
    },
  },
  -- { 'ibhagwan/fzf-lua' },
  -- {
  --   "folke/trouble.nvim",
  --   cmd = "TroubleToggle",
  -- },
  {
    "norcalli/nvim-colorizer.lua",
    config = function()
      require("colorizer").setup(
        { "css", "scss", "html", "javascript", "javascriptreact", "typescript", "typescriptreact", "yaml" }, {
          RGB = true,      -- #RGB hex codes
          RRGGBB = true,   -- #RRGGBB hex codes
          RRGGBBAA = true, -- #RRGGBBAA hex codes
          rgb_fn = true,   -- CSS rgb() and rgba() functions
          hsl_fn = true,   -- CSS hsl() and hsla() functions
          css = true,      -- Enable all CSS features: rgb_fn, hsl_fn, names, RGB, RRGGBB
          css_fn = true,   -- Enable all CSS *functions*: rgb_fn, hsl_fn
        })
    end,
  },
}


-- local capabilities = vim.tbl_deep_extend(
--   -- "error": raise an error
--   -- "keep": use value from the leftmost map
--   -- "force": use value from the rightmost map
--   "force",
--   {}, -- Empty capabilities
--   vim.lsp.protocol.make_client_capabilities(), -- Minimal capabilities
--   require("cmp_nvim_lsp").default_capabilities() -- Default capabilities
-- )

local lspconfig = require 'lspconfig'
local capabilities = require("cmp_nvim_lsp").default_capabilities()
lspconfig.tsserver.setup({
  capabilities = capabilities
})


```