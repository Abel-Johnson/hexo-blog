# tips

## 由于一台机器要搞两个github账号, 所以需要起别名

1. 修改.ssh/config,  加入别名配置, 
   - 需要修改  `.ssh/config`()  

      ``` yml
      #这里是原先使用的帐号(canny09@qq.com)
      Host github.com
      HostName github.com
      User git
      IdentityFile ~/.ssh/id-rsa__abel-johnson

      #这里是新的帐号(listen_kb@163.com)
      Host aj1219
      HostName github.com
      User git
      IdentityFile ~/.ssh/id-rsa__aj1219


      #更改[remote "origin"]项中的url中的#my.github.com 对应上面配置的host[remote "origin"] url = git@my.github.com:itmyline/blog.git
      ```

2. 然后把部署地址的地址改正  `_config.yml`

    ```yml
    deploy:
    type: git
    # repo: git@github.com:AJ1219/blog.git 改为 aj1219:AJ1219/blog.git
    repo: aj1219:AJ1219/blog.git
    branch: gh-pages
    ```
