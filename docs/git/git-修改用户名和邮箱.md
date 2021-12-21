## 修改全局配置

- 查看全局配置项

  ```bash
  git config --list
  
  # 输出
  core.symlinks=false
  core.autocrlf=true
  core.fscache=true
  color.diff=auto
  color.status=auto
  color.branch=auto
  color.interactive=true
  help.format=html
  rebase.autosquash=true
  http.sslcainfo=C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
  http.sslbackend=openssl
  diff.astextplain.textconv=astextplain
  filter.lfs.clean=git-lfs clean -- %f
  filter.lfs.smudge=git-lfs smudge -- %f
  filter.lfs.process=git-lfs filter-process
  filter.lfs.required=true
  credential.helper=manager
  user.name=QiaoHao
  user.email=H_Qiao1126@163.com
  alias.cp=cherry-pick
  http.sslverify=false
  filter.lfs.clean=git-lfs clean -- %f
  filter.lfs.smudge=git-lfs smudge -- %f
  filter.lfs.process=git-lfs filter-process
  filter.lfs.required=true
  ```

- 查看某一项配置

  ```bash
  git config user.name
  git config user.email
  ```

- 修改全局配置

  ```bash
  git config --global user.name xxx
  git config --global user.email xxx
  ```

- 直接修改 git 配置文件的方式来修改，文件位置：`~/.gitconfig`

  ```
  [user]
  	name = QiaoHao
  	email = H_Qiao1126@163.com
  [alias]
  	cp = cherry-pick
  [http]
  	sslVerify = false
  [filter "lfs"]
  	clean = git-lfs clean -- %f
  	smudge = git-lfs smudge -- %f
  	process = git-lfs filter-process
  	required = true
  ```

## 修改当前项目配置

> 必须在当前项目目录下执行命令

- 查看当前项目配置项

```bash
git config --local --list
```

- 修改配置

```bash
git config user.name xxx
git config user.email xxx
```

- 修改当前项目 `.git` 目录中的 `config` 文件，如果没有进行过修改的话，默认打开是没有对应的用户名和邮箱的