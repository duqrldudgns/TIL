# Makefile
 * make 빌드 자동화 도구가 대상 / 목표를 생성하는 데 사용하는 지시문 세트를 포함하는 파일
 
  
 ## 구조  
 * 대상(Target), 의존 관계(Dependency), 명령(Command)
 
```
    <target> : <dependency>
    (tab)<Command>
```

> target : 빌드 대상 이름. 명령에 의해 생성되는 결과 파일, 오브젝트 파일이나 실행 파일
>
> Dependency : 대상을 만들 때 의존되는 파일들. 여기에 나열된 대상들을 먼저 만들고 빌드 대상을 생성한다.
>
> Command : 빌드 대상을 생성하는 명령. 의존 관계에 있는 파일들이 변경됐거나 대상 파일이 없을 때 명령이 실행된다. 쉘에서 쓸 수 있는 명령을 사용할 수 있다.

 ***
 
 ## 이해

sum.h  sum.c  main.c 세개의 파일이 있다고 가정.

* sum_test라는 실행파일을 만들기 위해 입력하는 명령어의 나열
```
g++ -c -o sum.o sum.cpp
g++ -c -o main.o main.cpp
g++ -o sum_test sum.o main.o
```

  * 컴파일 O, 실행파일 생성 O
```
g++ -o sum.o sum.cpp
```

  * 컴파일 O, 실행파일 생성 X
```
g++ -c -o sum.o sum.cpp
```

  * main.cpp 컴파일해서 main.o를 만들어라
```
g++ -c -o main.o main.cpp
```

  * .o를링크를걸어서 실행파일 sum_test를 만들어라
```
g++ -o sum_test sum.o main.o
```

 ## 작성
 
 실행파일 생성 할 때마다 위 명령어 입력하기 싫으면 Makefile 만드는 것이 Best!
 ```
 #Makefile
 
 all: sum_test

sum_test: sum.o main.o              #실행순서 3순위
	g++ -o sum_test sum.o main.o

sum.o: sum.cpp sum.h                #실행순서 1순위
	g++ -c -o sum.o sum.cpp

main.o: main.cpp sum.h              #실행순서 2순위
	g++ -c -o main.o main.cpp

clean:
	rm -f sum_test *.o
  ```
  
## 실행

```
$ make
```

## 지우기
```
$ make clean
```
