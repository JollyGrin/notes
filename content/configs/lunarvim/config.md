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

local formatters = require "lvim.lsp.null-ls.formatters"
formatters.setup {
  {
    command = "prettier",
    extra_args = { "--print-width", "80", "--tab-width", "2" },
    filetypes = { "css", "json", "typescript", "typescriptreact", "javascript", "javascriptreact", "yaml" },
  },
}

lvim.keys.normal_mode["<leader>xx"] = {
  "<cmd>TroubleToggle<cr>",
  silent = true,
  noremap = true,
}

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
  {
    'wfxr/minimap.vim',
    build = "cargo install --locked code-minimap",
    config = function()
      vim.cmd("let g:minimap_width = 10")
      vim.cmd("let g:minimap_auto_start = 1")
      vim.cmd("let g:minimap_auto_start_win_enter = 1")
    end,
  },
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
    cmd = "TroubleToggle",
  },
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

```