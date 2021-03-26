# [Algorithm] 카카오 2019 블라인드 테스트 문제풀이 - 후보키

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
    col_combs = []
    for i in range(col_size):
        col_combs.extend(list(combinations([x for x in range(col_size)], i + 1)))

    # 각 조합에 대해 pk 여부 체크 후 리스트에 추가
    pks = []
    for col_comb in col_combs:
        # 각 행에서 "컬럼1+...+컬럼n" 포맷의 키를 담은 리스트 생성
        keys = ["".join([row[col] for col in col_comb]) for row in relation]

        # 리스트 keys와 집합 keys의 값이 같지 않으면 중복 존재
        if len(keys) != len(set(keys)):
            continue

        # 현 조합의 최소 여부 체크(col_comb가 작은 순)
        is_key_minimum = True
        for pk in pks:
            if len(set(pk) - set(col_comb)) == 0:
                is_key_minimum = False
                break

        # 최소면 pk 리스트에 추가
        if is_key_minimum:
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