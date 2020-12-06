# Settings for Git

- [Chinese git toturial](https://www.liaoxuefeng.com/wiki/896043488029600)
- [Git Cheat Sheet](https://github.com/arslanbilal/git-cheat-sheet)

```
//.gitconfig
[user]
	name = willsnow
	email = willsnowdev@gmail.com
#[http]
#	proxy = socks5://127.0.0.1:1086
#[https]
#	proxy = socks5://127.0.0.1:1086
[color]
	ui = true
[alias]
	st = status
	co = checkout
	ci = commit
	br = branch
	unstage = reset HEAD
	lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

```
