# [Python] 파이썬 문자열 함수 정리

![Python Logo](./image.png)
<!-- [##_Image|kage@4OxfM/btq0fkq18Ov/FXiejhak0PnV8bcAXsMLK1/img.png|alignCenter|width="100%"|_##] -->

- 내가 보고 사용하기 위한 파이썬 문자열 함수 정리
- 지속 업데이트 예정


#### replace()

문자열 내의 특정 문자열을 다른 문자열로 바꾼 값을 리턴한다.

`replace("찾을 문자열", "바꿀 문자열")`

```python
s = "Shoes on, get up in the morn', cup of milk, let's rock and roll"
replaced = s.replace("milk", "coffee")
print(replaced)

# 결과
# Shoes on, get up in the morn', cup of coffee, let's rock and roll
```

> [MEMO]  
> 찾을 문자열이 여러개라면?
> => 모든 문자열을 바꾼다.


#### split()

인자를 기준으로 하여 문자열을 자른 리스트를 리턴한다.

`split("기준 문자열")`
```python
s = "Life goes on"
splits = s.split()
print(splits)

# 결과
# ['Life', 'goes', 'on']
```

> [MEMO]  
> 인자를 넣지 않으면 공백을 기준으로 나눈다.


#### join()

인자로 들어오는 문자열 사이사이에 문자열을 삽입한 값을 리턴한다.

`"사이에 들어갈 문자열".join("사이를 내어줄 문자열")`

```python
s = ".".join("BTS")
print(s)

# 결과
# B.T.S
```

> [MEMO]  
> 헷갈릴 수 있는데, 한글로 번역하면 약간 매끄럽지 못한데 영어 그대로 자연스럽게 읽으면 쉽다.  
> He Join Us == 그는 우리와 함께 한다(그는 우리 사이에 어울린다)

#### format()

문자열에 인자를 포맷팅한 값을 리턴한다.

##### 1. 순서로 값 대입하기

`"{0} {1} {2}".format("값1", "값2", "값3")`

```python
s = "Jump up to the {0} {1}".format("top", "LeBron")
print(s)

# 결과
# Jump up to the top LeBron
```

##### 2. 키워드 인자로 값 대입하기

`"{kwarg1} {kwarg2} {kwarg3}".format(kwarg1="값1", kwarg2="값2", kwarg3="값3")`

```python
s = "Jump up to the {position} {player}".format(position="top", player="LeBron")
print(s))

# 결과
# Jump up to the top LeBron
```

> [MEMO]  
> 함수 파라미터명으로 인자를 대입하는 것을 키워드 인자(Keyword Argument)라고 부른다.


#### count()

문자열에서 인자의 개수를 세서 리턴한다.


`count("찾을 문자열")`

```python
s = "BTS"
c = s.count("B")
print(c)

# 결과
# 1
```


#### find()

문자열에서 인자가 처음으로 나온 위치를 찾아서 리턴한다.
***찾지 못했을 경우 -1을 리턴한다***

`find("찾을 문자열")`

```python
s = "Disco overload, I'm into that, I'm good to go"
idx = s.find("good")
print(idx)

# 결과
# 35
```


#### index()

문자열에서 인자가 처음으로 나온 위치를 찾아서 리턴한다.
***찾지 못했을 경우 에러를 발생시킨다***

`index("찾을 문자열")`

```python
s = "Disco overload, I'm into that, I'm good to go"
idx = s.index("goooood")
print(idx)

# 결과
# ValueError: substring not found
```

> [TODO]  
> find()와 index()가 무슨 차이가 있는지 확실히 파악 필요.
> 1) find()는 문자열에서만 사용 가능 / index()는 문자열, 리스트, 튜플에서 사용 가능. 
> 2) 시간복잡도의 차이는 없을까?


#### upper()

문자열의 모든 문자를 대문자로 바꾼 값을 리턴한다.

`upper()`

```python
s = "dynamite"
up = s.upper()
print(up)

# 결과
# DYNAMITE
```

#### lower()

문자열의 모든 문자를 소문자로 바꾼 값을 리턴한다.

`lower()`

```python
s = "DYNAMITE"
low = s.lower()
print(low)

# 결과
# dynamite
```



<!-- 

#### strip()
#### rstrip()
#### lstrip()

-->