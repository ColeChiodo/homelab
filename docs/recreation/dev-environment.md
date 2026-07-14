# Dev Environment

## Neovim

```bash
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.appimage
chmod u+x nvim-linux-x86_64.appimage
```

Expose globally:

```bash
sudo mkdir -p /opt/nvim
sudo mv nvim-linux-x86_64.appimage /opt/nvim/nvim
```

## Oh My Bash

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```

## Stow + Dotfiles

```bash
sudo apt install stow
git clone https://github.com/ColeChiodo/dotfiles.git
cd dotfiles
stow --adopt tmux nvim oh-my-bash bashrc
git restore .
```

Add to `.bashrc_local`:

```bash
export PATH="$PATH:/opt/nvim/"
```

Then `source .bashrc`.

## Neovim needs GCC

```bash
sudo apt install gcc
```

## Opencode

```bash
curl -fsSL https://opencode.ai/install | bash
```
