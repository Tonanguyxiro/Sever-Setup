# 配置 Github 登录

### Git & Github

#### Fine-Grained Access Key

```shell
# 查看地址
git remote -v

# 修改地址
git remote set-url origin https://oauth2:<Your-Fine-Grained-Access-Key>@github.com/<Your-Username>/<Your-Repository>.git
```

#### Set User Name & Email

```shell
git config --global user.name "Tong Yuan"
git config --global user.email "yuantong.zj@qq.com"
```