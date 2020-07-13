# String

### str[index] 
* 배열처럼접근가능

***
### str.front()
* 맨 앞 인자를 반환

***
### str.back()
* 맨 뒤 인자를 반환

***
### str.size() 
* string의 길이 반환

***
### str.capacity()
* 객체에 할당된 메모리 크기(bytes)를 반환

***
### str.resize(...)
* str.resize(n)
* str.resize(n, 'c')
* string을 n만큼의 크기로 만듦
  * 만약 그 크기가 원래 사이즈 보다 작다면, 남은 string을 버림. 
  * 만약 그 크기가 원래 사이즈 보다 크다면, 빈 공간으로 남은 공간을 채움. 만약 c를 사용한다면 남은 공간을 c로 채울 수 있음.

***
### str.shrink_to_fit()
* string 길이에 비해 낭비되고 있는 capacity를 줄이는 함수 (capacity : 메모리에 할당된 용량의 크기)

***
### str.reserve(n=0) 
* 문자열을 넣기 전에 미리 "곧 n만큼의 크기의 string이 들어올거니까 그에 맞는 capacity를 할당해 달라"는 함수 

***
### str.clear()
* string에 들어있는 문자열을 지우는 함수
  * size와 length는 0이 되고, capacity는 그대로 남게 됨 
  * 메모리 해제가 아닌 문자열 값들을 삭제하는 것

***
### str.empty() 
* string이 비었는지 확인, 비었으면 ture반환

***
### str.c_str()
* string 문자열을 C스타일의 문자열로 변경해주는 함수
```
const char* arr = str1.c_str();    // "abcde"를 "abcde\0"로 반환해 줍니다.
```

***
### str.substr(index, len(생략가능))
* string을 index에서부터 len만큼 잘라서 반환하는 함수

***
### str1.replace(index, len, str2)
* 문자열의 index위치에서 len 길이까지의 범위를 매개변수로 들어온 str2 전체로 대체 하는 함수
```
str1.replace(5, 2, str2); //"abcdefghij"의 5번째 인자에서부터 2개를 str2로 대체하게 됩니다. 그러면 "abcdeYOUNGHUNhij" 이런식의 문자가 됩니다.
```

***
### str.compare(....)
* str1.compare(str2)
* str1.compare(index, len, str2)
* str1.compare(index, len, str2, index2, len2)

* 매개변수로 들어온 str을 비교하는 함수
  * 같으면 0 리턴
  * str1 < str2 일 때 (작을 때, 사전 순 빠를 때) -1 리턴
  * str1 > str2 일 때 (클 때, 사전 순 느릴 때) 1 리턴

***
### str.copy(arr, len, index=0)
* index의 위치부터 len 까지 arr에 복사
  * 복사된 길이, arr의 길이를 반환
  * 함수 호출 후 arr[arrlen] = '\n'; 해줘야 에러 발생하지 않음

***
### str.find(....)
* str.find(str, index = 0)
* str.find(arr, index = 0)

* index부터 들어온 문자열을 찾는 함수. 
  * 만약에 일치하는게 있다면, 일치하는 부분의 첫번째 순서(index)를 반환

***
### str.push_back(char)
* string의 맨뒤에 문자 c를 더하는 함수

***
### str.pop_back()
* string의 맨뒤에 있는 문자 하나를 없애는 함수

***
### str1+str2 가능

***
### swap(str1, str2)
* str1과 str2를 바꾸는 것

***
### str.begin()
* 문자열의 첫 번째 문자를 가리키는 iterator 포인터를 반환

***
### str.end()
* 문자열의 마지막의 바로 다음을 가리키는 iterator 포인터를 반환
  * 문자열의 끝 + 1 인 것에 유의

```
for (string::iterator iter = str1.begin(); iter != str1.end(); ++iter)
{
    cout << *iter << endl;
}
```

***
출처 https://blockdmask.tistory.com/338
