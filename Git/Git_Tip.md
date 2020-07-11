# Git Tip 저장소

## 1. git push (id,pw X)

* git push를 할 때마다 id, password을 물어 보는데 cache를 생성하여 입력 할 필요 없어짐
```
git config --global credential.helper "cache --timeout=360000"
```
