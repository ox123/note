### getignore 不生效处理方法

- 在项目当前目录执行

  ```
  touch .gitignore
  ```

- 执行如下命令

  ```
  git rm -r --cached .
  git add .
  git commit -m 'update .gitignore
  ```

  

