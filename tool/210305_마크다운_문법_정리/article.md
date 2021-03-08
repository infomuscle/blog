# \[Markdown\] 마크다운 사용법 정리

![마크다운 로고](./image.png)
<!-- [##_Image|kage@lmSVX/btqZoyQUL1B/mEm79H2Ck0hAeoEiV9eXik/img.png|alignCenter|width="100%" data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##] -->


내가 보고 사용하기 위한 마크다운 문법 정리.

마크다운이란 무엇인가 등에 대한 서론은 생략한다.

---


## 헤더 

`#`으로 표현한다. 개수에 따라 수준이 정해진다. HTML의 `<h1>`~`<h6>`으로 변환된다.

```text
# Header1
## Header2
### Header3
### Header4
#### Header5
##### Header6
###### Header7
```

# Header1
## Header2
### Header3
### Header4
#### Header5
##### Header6
###### Header7

> [MEMO]
> 마크다운은 표준이 없어서, H7 지원 여부는 브라우저나 에디터에 따라 다를 수 있다.

---


## 리스트

리스트에는 순서있는 리스트, 순서없는 리스트가 있다. HTML의 `<ol>`과 `<ul>` 태그에 대응한다.

#### 순서 있는 리스트

`숫자.`를 이용해 표현한다. 들여쓰기에 따라 수준이 정해진다.

```text
1. South Korea
	1. Seoul
	2. Busan
2. France
	1. Paris
	2. Marseille
3. United States
	1. New York
	3. Los Angeles
```

1. South Korea
	1. Seoul
	2. Busan
2. France
	1. Paris
	2. Marseille
3. United States
	1. New York
	3. Los Angeles

> [MEMO]
> 꼭 1,2,3 순서로 타이핑하지 않아도 된다. 1,3,2 등으로 순서가 꼬여도 순서대로 변환된다(United States 참조).

#### 순서없는 리스트

`*`, `+`, `-`로 표현한다. 들여쓰기에 따라 수준이 정해진다. 

```text
* 빅히트
	* 방탄소년단
		* 랩몬스터
		* 뷔
+ YG
	+ 블랙핑크
		+ 제니
		+ 지수
- SM
	- 레드벨벳
		- 슬기
		- 웬디
```

* 빅히트
	* 방탄소년단
		* 랩몬스터
		* 뷔
+ YG
	+ 블랙핑크
		+ 제니
		+ 지수
- SM
	- 레드벨벳
		- 슬기
		- 웬디

---


## 강조

`*`, `_`를 이용해 표현한다. 1개는 이탤릭, 2개는 볼드, 3개는 볼드이탤릭을 표현한다.

#### 이탤릭

```text
This is gettihg *heavy* Can you hear the base boom, I'm *ready*.

Life is sweat as _honey_ yeah this beat cha-ching like _money_.
```

This is gettihg *heavy* Can you hear the base boom, I'm *ready*.

Life is sweat as _honey_ yeah this beat cha-ching like _money_.

#### 볼드

```text
This is gettihg **heavy** Can you hear the base boom, I'm **ready**.

Life is sweat as __honey__ yeah this beat cha-ching like _money__.
```

This is gettihg **heavy** Can you hear the base boom, I'm **ready**.

Life is sweat as __honey__ yeah this beat cha-ching like __money__.

#### 볼드이탤릭

```text
This is gettihg ***heavy*** Can you hear the base boom, I'm ***ready***.

Life is sweat as ___honey___ yeah this beat cha-ching like ___money___.
```

This is gettihg ***heavy*** Can you hear the base boom, I'm ***ready***.

Life is sweat as ___honey___ yeah this beat cha-ching like ___money___.

---


## 코드

`` ` ``를 이용하여 표현한다. 1개로는 인라인, 3개로는 블록을 표현할 수 있다.

> [MEMO]
> 인라인 코드로 Backquote 를 표현하려면 \`\` \` \`\`처럼 사용하면 된다.

#### 인라인

```text
자바의 기본 자료형으로는 `byte`, `short`,`int`,`long`,`double`, `float`, `char`, `boolean`이 있습니다.
```

자바의 기본 자료형으로는 `byte`, `short`,`int`,`long`,`double`, `float`, `char`, `boolean`이 있습니다.

#### 블록

``````text
```python
def sum(a, b):
	return a + b
```

```text
지원되는 언어(추가 예정)
text, java, python, js
```
``````

```python
def sum(a, b):
	return a + b
```

```text
지원되는 언어(추가 예정)
text, java, python, js
```

> [MEMO]
> 상단 ```옆에 언어 이름을 명기하면 해당 언어에 적합하게 하이라이트 된다.

---


## 수평선

`*`이나 `-`로 표현한다. 개수나 띄어쓰기 등을 적절히 활용할 수 있다.

```text
***

******************

* * * * * * * * * * * * * * * * * * 

---

------------------

- - - - - - - - - - - - - - - - - - 
```

***

******************

* * * * * * * * * * * * * * * * * * 

---

------------------

- - - - - - - - - - - - - - - - - -

---


## 링크

`[링크명](URL "설명(생략가능)"`으로 표현한다. `<URL>`이나 URL 원문을 그대로 사용해서 간단히 표현할 수도 있다. HTML의 `<a>` 태그로 변환된다.

```text
[Bortfoloio](https://bortfolio.tistory.com/)

[AWS](https://aws.amazon.com/ko/ "아마존 웹 서비스 바로가기")

<https://leetcode.com/>

https://github.com/infomuscle
```

[Bortfoloio](https://bortfolio.tistory.com/)

[AWS](https://aws.amazon.com/ko/ "아마존 웹 서비스 바로가기")

<https://leetcode.com/>

https://github.com/infomuscle

---


## 이미지

`![대체 텍스트](URL "설명(생략가능))`로 표현한다. HTML의 `<img>`태그로 변환된다. 

```text
![카카오 3월 배경화면](https://t1.kakaocdn.net/friends/prod/brand/202103_type2_thumb_pc.jpg "카카오프렌즈샵")
```

![카카오 3월 배경화면](https://t1.kakaocdn.net/friends/prod/brand/202103_type2_thumb_pc.jpg "카카오프렌즈샵")

> [MEMO]
> URL은 상대적 경로료 표현할 수도 있다. ex) ./sample.png

---


## 표

아래와 같은 포맷으로 표현한다. `:`를 이용해 정렬한다.

```text
|제목|내용|설명
|:---|:---:|---:|
|왼쪽정렬|가운데정렬|오른쪽정렬|
|왼쪽정렬|가운데정렬|오른쪽정렬|
|왼쪽정렬|가운데정렬|오른쪽정렬|
```

|제목|내용|설명
|:---|:---:|---:|
|왼쪽정렬|가운데정렬|오른쪽정렬|
|왼쪽정렬|가운데정렬|오른쪽정렬|
|왼쪽정렬|가운데정렬|오른쪽정렬|
