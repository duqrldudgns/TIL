# init
 ## 초기 설정(init)
```
echo "# abcde" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/duqrldudgns/abcde.git
git push -u origin master                
```

 ## 초기 설정(clone) 

* user name 등록
```
git config --global user.name <사용자명>
```

* email 등록
```
git config --global user.email <이메일주소>
```

* git 환경 설정
```
git config --global --list
```

* repository를 가져옴
```
git clone https://github.com/snoopspy/sum_test.git
```
  
  
  
## 유용 

* git push를 할 때마다 id, password을 물어 보는데, cache를 두어 여러번 입력 할 필요 없어짐
```
git config --global credential.helper "cache --timeout=360000"
```

출처 : https://gitlab.com/gilgil/sns/-/wikis/git-exam/git-exam [gilgil]
