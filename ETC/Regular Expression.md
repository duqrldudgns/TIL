# 정규표현식(Regular Expression)
 * 특정한 규칙을 가진 문자열의 집합을 표현하는 데 사용하는 형식 언어
 * 주로 Programming Language나 Text Editor 등 에서 문자열의 검색과 치환을 위한 용도
 
## 관련 사이트
 * https://regexr.com/
    * 정규식을 연습하거나 테스트 해볼 수 있는 사이트
    
## 메타 문자

  ### 1. Character classes

메타 문자 | 의미
---- | ----
. |  any character except newline
\w \d \s |  word, digit, whitespace
\W \D \S |  not word, digit, whitespace
[abc] |  any of a, b, or c
[^abc] |  not a, b, or c
[a-z] |  character between a & z
[A-Z] |  character between A & Z
[0-9] |  between 0 & 9
 
 ### 2. Anchors

메타 문자 | 의미
---- | ----
^abc$ |  start / end of the string
\b \B |  word, not-word boundary

 ### 3. Escaped characters

메타 문자 | 의미
---- | ----
\. \* \\ |  escaped special characters
\t \n \r |  tab, linefeed, carriage return

  ### 4. Groups & Lookaround

메타 문자 | 의미
---- | ----
(abc) |  capture group
\1 |  backreference to group #1
(?:abc) |  non-capturing group
(?=abc) |  positive lookahead
(?!abc) |  negative lookahead
 
 ### 5. Quantifiers & Alternation
 
 메타 문자 | 의미
---- | ----
a* a+ a? |  0 or more, 1 or more, 0 or 1
a{5} a{2,} |  exactly five, two or more
a{1,3} |  between one & three
a+? a{2,}? |  match as few as possible
ab\|cd |  match ab or cd

## 문제
 * 모두 만족하는 조합 패턴은? 
  ```
  02-123-4567 (02)123-4567 02) 123.4567​
031-123-4567 (031)123-4567 031) 123-4567​
051-234-5678 (051)234-5678 051) 234.5678​
011-234-5678 011.234.5678​
017-456-7890 017.456.7890​
010-2345-6789 010.2345.6789​
```
 
 * 내가 푼 답
 ```
\(?\d{2,3}.\s?\d{3,4}.?\d{4}
 ```
