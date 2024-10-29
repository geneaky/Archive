- 타입 없이 변수 선언
- Array가 객체 역할을 함
	- Map 방식으로도 사용가능
- 반복문
	- array를 반복하기 위한 표현식
``` lua
b = {1,2,"3"}
for i,line in ipairs(b) do
print(line)
end
```
ipairs 함수를 제공해주어 array를 쉽게 반복문 돌릴 수 있음

- false, nil 둘 다 조건 판별시 부정으로 동작
- 