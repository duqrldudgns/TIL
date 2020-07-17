# useful functions

## \<algorithm\>

### max(a,b)
- a,b 중 큰 값을 반환
- 모든 타입이 사용 가능

### min(a,b)
- a,b 중 작은 값을 반환
- 모든 타입이 사용 가능

### sort(...)
- 오름차순 정렬
- 모든 타입이 사용 가능
```
sort(arr, arr+n);
sort(v.begin(), v.end());
sort(v.begin(), v.end(), compare);                //사용자 정의 함수 사용
sort(v.begin(), v.end(), greater<자료형>());    //내림차순 (Descending order)

bool compare(Student a,Student b){
    if(a.name == b.name){   //이름이 같으면, 나이가 적은순
        return a.age < b.age;
    }else{                  //이름이 다르면, 이름 사전순
        return a.name < b.name;
    }
}
```

## \<ctime\>

### clock()
```
start = clock();
BinarySearch(URL); // 만든 함수
end = clock();
result = (double)(end - start);
printf("%f ms", result);
```

### srand() 
 * 랜덤함수
 ```
srand((unsigned int)time(NULL));
     while(1){
     printf("난수 : %d\t나머지 : %d\n", num, (int)num % 10);
}
```
