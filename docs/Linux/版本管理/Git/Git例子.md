# Git例子

## Git安装后的必要设置

```bash
git config --global user.name '老A'
git config --global user.email 'antonchen@hotmail.com'
```

##　在本地创建库

```bash
mkdir antonchen
cd antonchen
git init
touch README.md
git add README.md
git commit -m 'first commit'
```

## 提交到远端

```bash
git remote add origin 'git@gitcafe.com:antonchen/antonchen.git'
git push -u origin master
```

## 也可直接提交本机的库

```
cd existing_git_repo
git remote add origin 'git@gitcafe.com:antonchen/antonchen.git'
git push -u origin master
```