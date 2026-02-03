


Git global setup
```
git config --global user.name "zhouxinfei"
git config --global user.email "zhouxinfei@able-elec.com"
```
Create a new repository
```
git clone git@git.zhihuishu.com:zhs_project/git_test.git
cd git_test
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

Existing folder
```
cd existing_folder
git init
git remote add origin git@git.zhihuishu.com:zhs_project/git_test.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

Existing Git repository
```
cd existing_repo
git remote add origin git@git.zhihuishu.com:zhs_project/git_test.git
git push -u origin --all
git push -u origin --tags
```