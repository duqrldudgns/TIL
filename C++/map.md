# 맵(map) 기본 함수


### 기본형태

* map<key,value> : key와 value를 pair 형태로 선언합니다.
* map<key,value>::iterator iter : 

### iterator(반복자)

* begin() : beginning iterator를 반환
* end() : end iterator를 반환

### 추가 및 삭제

* insert( make_pair(key,value) ) : 맵에 원소를 pair 형태로 추가
* erase(key) : 맵에서 key(키값)에 해당하는 원소 삭제
* clear() : 맵의 원소들 모두 삭제

### 조회

* find(key) : key(키값)에 해당하는 iterator를 반환
* count(key) : key(키값)에 해당하는 원소들(value들)의 개수를 반환

### 기타

* empty() : 맵이 비어있으면 true 아니면 false를 반환
* size() : 맵 원소들의 수를 반환
