# Tmux current pane hostname/user

Tmux plugin that enables displaying hostname and user of the current pane in your status bar.

Replaces the `#H` format and adds a `#U` format option.

Currently working for gcloud and mosh connections

### Usage

- `#H` will be the hostname of your current path. If there is an ssh session opened, the ssh hostname will show instead of the local one.
- `#{hostname_short}` will be the short hostname of your current path (up to the first dot). If there is an ssh session opened, the ssh hostname will show instead of the local one.
- `#U` will show the `whoami` result or the user that logged in an ssh session.
- `#{pane_ssh_port}` if an open ssh session will show the connection port, otherwise it will be empty.
- `#{pane_ssh_connected}` will be set to 1 if the currently selected pane has an active ssh connection. (Useful for `#{?#{pane_ssh_connected},ssh,no-ssh}` which will evaluate to `ssh` if there is an active ssh in the currently selected pane and `no-ssh` otherwise.)

Here are examples in `.tmux.conf`:

```bash
set -g status-right '#[fg=cyan,bold] #U@#H #[default]#[fg=blue]#(tmux display-message -p "#{pane_current_path}" | sed "s#$HOME#~#g") #[fg=red]%H:%M %d-%b-%y#[default]'
```

```bash
set -ga status-left "#[bg=#{@thm_bg},fg=#{@thm_green}] #{?#{pane_ssh_connected},#[fg=#{@thm_red}]  #{hostname_short} ,  #{pane_current_command}}" #changes the current process for the remote hostname if connected (and the color)

set -ga status-left "#[bg=#{@thm_bg},fg=#{@thm_mauve}] #{?#{pane_ssh_connected}, , #{=/-32/...:#{s|$USER|~|:#{b:pane_current_path}}} |}" #shows the local path only if not connected

set -ga status-right "#[bg=#{@thm_bg},fg=#{@thm_blue}] #{?#{pane_ssh_connected},, #{pane_current_path}} " #shows the current full path only if not connected

set -ga status-right "#[bg=#{@thm_bg},fg=#{@thm_blue}] #{?#{pane_ssh_connected},#[fg=#{@thm_red}]  #U ,#[fg=#{@thm_blue}]  #U }" #shows the current or remote user, different colors for awareness
```

### Installation with [Tmux Plugin Manager](https://github.com/tmux-plugins/tpm) (recommended)

Add plugin to the list of TPM plugins in `.tmux.conf`:

    set -g @tpm_plugins "                 \
      tmux-plugins/tpm                    \
      soyuka/tmux-current-pane-hostname     \
    "

Hit `prefix + I` to fetch the plugin and source it.

`#U@#H` interpolation should now take the current pane ssh status into consideration.

### Manual Installation

Clone the repo:

    $ git clone https://github.com/jacostag/tmux-current-pane-hostname ~/clone/path

Add this line to the bottom of `.tmux.conf`:

    run-shell ~/clone/path/current_pane_hostname.tmux

Reload TMUX environment:

    # type this in terminal
    $ tmux source-file ~/.tmux.conf

`#U@#H` interpolation should now work.

### Limitations

I wanted to get the current path of the opened ssh session but that's not possible. I haven't found a way to get the output of a remote command that will be executed on an opened ssh session. A dirty way would be to use `send-keys pwd Enter` but this will show on the pane and we don't want this.
So, I'm just getting the correct ssh command corresponding to the pane job pid and parsing it, for example:
```
ssh test@host.com
# #H => host.com
# #U => test
```

### License

[MIT](LICENSE.md)
