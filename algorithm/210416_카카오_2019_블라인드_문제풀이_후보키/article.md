# [Algorithm] 2019 카카오 블라인드 테스트 문제풀이 - 후보키

![2019 카카오 블라인드 테스트](./image.jpeg)
<!-- [##_Image|kage@rWI8k/btq2E9hoQRB/MAd8Y7hau0BbShOxD46KE0/img.jpg|alignCenter|width="100%"|_##] -->

## 문제

```text
프렌즈대학교 컴퓨터공학과 조교인 제이지는 네오 학과장님의 지시로, 학생들의 인적사항을 정리하는 업무를 담당하게 되었다.

그의 학부 시절 프로그래밍 경험을 되살려, 모든 인적사항을 데이터베이스에 넣기로 하였고, 이를 위해 정리를 하던 중에 후보키(Candidate Key)에 대한 고민이 필요하게 되었다.

후보키에 대한 내용이 잘 기억나지 않던 제이지는, 정확한 내용을 파악하기 위해 데이터베이스 관련 서적을 확인하여 아래와 같은 내용을 확인하였다.

관계 데이터베이스에서 릴레이션(Relation)의 튜플(Tuple)을 유일하게 식별할 수 있는 속성(Attribute) 또는 속성의 집합 중, 다음 두 성질을 만족하는 것을 후보 키(Candidate Key)라고 한다.
유일성(uniqueness) : 릴레이션에 있는 모든 튜플에 대해 유일하게 식별되어야 한다.
최소성(minimality) : 유일성을 가진 키를 구성하는 속성(Attribute) 중 하나라도 제외하는 경우 유일성이 깨지는 것을 의미한다. 즉, 릴레이션의 모든 튜플을 유일하게 식별하는 데 꼭 필요한 속성들로만 구성되어야 한다.
제이지를 위해, 아래와 같은 학생들의 인적사항이 주어졌을 때, 후보 키의 최대 개수를 구하라.
```

[프로그래머스 문제 바로가기](https://programmers.co.kr/learn/courses/30/lessons/42890)

## 풀이

```python
from itertools import combinations


def solution(relation):
    # 컬럼 조합 리스트 구하기
    col_size = len(relation[0])
    col_idxs = [x for x in range(col_size)]
    col_combs = []
    for i in range(col_size):
        col_combs += list(combinations(col_idxs, i + 1))

    # 각 조합에 대해 pk 여부 체크 후 리스트에 추가
    pks = []
    for col_comb in col_combs:
        # 각 행에서 "컬럼1+...+컬럼n" 포맷의 키를 담은 리스트 생성
        keys = ["".join([row[col] for col in col_comb]) for row in relation]

        # 리스트 keys와 집합 keys의 값이 같지 않으면 중복 존재
        if len(keys) != len(set(keys)):
            continue

        # 현 조합의 최소 여부 체크(==최소가 아닌 것들만 필터링한 개수가 0)
        if len(list(filter(lambda pk: len(set(pk) - set(col_comb)) == 0, pks))) == 0:
            pks.append(col_comb)

    return len(pks)
```
2차원의 배열 형태로 테이블이 주어지면, 각 데이터를 비교해 후보키가 될 수 있는 컬럼 조합을 찾는 문제다.

문제 해결 로직의 단계는 아래와 같다. 

1. 컬럼의 조합 구하기
2. 각 조합에 대해 PK 여부 체크하기
    1. 중복 여부 체크
    2. 최소 여부 체크

# STEP 1

1. 컬럼의 크기 `col_size`를 구한다.
2. `0` ~  `col_size-1`까지의 정수를 원소로 갖는 리스트 `col_idxs` 만든다.
3. `nC1` + `nC2` + ... + `nCk`의 모든 경우의 수 조합을 모은 리스트 `col_combs`를 만든다. `n`과 `k`는 `col_size`다.  

# STEP 2
1. 후보키를 담을 리스트 `pks`를 만든다.
2. 조합 `col_combs`내의 모든 원소에 대해 반복문 실행.
    1. 릴레이션의 각 행에 대해, 선택된 컬럼의 값들을 일렬로 붙인 문자들을 모은 리스트 `keys`를 만든다.
    2. `set(keys)`는 `keys`에서 중복을 제거한 집합이다. 집합의 크기와 리스트의 크기가 다르면 중복이 있는 것이므로 이 조합은 후보키가 아니다.  
    3. 최소성도 만족해야 후보키가 된다. `pks`에 기존에 담겨있는 각 후보키 `pk`에서 현재 조합 `col_comb`을 뺀 차집합의 크기가 0이면, `col_comb`는 `pk`의 모든 원소를 포함하므로 최소성을 만족하지 않는다. 차집합의 크기가 0인 pk들만 필터링한 리스트의 개수가 0이면 `col_comb`의 최소성을 만족시키지 않는 pk의 개수가 0이란 것이다. 즉 최소성을 만족하므로 `pks`에 `col_comb`를 추가한다.

> 포스팅을 쓰면서 코드량을 최소화 시켜봤더니 마지막 현 조합의 최소 여부 체크 부분이 가독성을 잃고 좀 직관적이지 않게 된 것 같다. 그냥 최근 자바 스트림 사용을 익히고 있어서 저런 식으로 풀어보고 싶었다.