# 실험 관찰

## Task 1: MinHash와 LSH

Jaccard 유사도와 MinHash 서명이 정상적으로 계산되었다. `Jaccard(S1, S4)`는 실제로 0.6667이었지만, 두 개의 해시만 사용한 서명에서는 S1과 S4가 두 위치 모두 일치하여 추정 유사도가 1.0이 되었다. 해시 함수가 적으면 추정값의 오차가 커질 수 있으며, 더 많은 해시를 사용하면 정확도가 높아지는 대신 계산 및 저장 비용이 증가한다. 빈 집합의 Jaccard 유사도도 0.0으로 정상 처리되었다.

실행 결과:

```text
ok jaccard(S1, S4) 0.6667
ok jaccard on empty sets 0.0
ok signature of S1 [1, 0]
ok signature of S2 [3, 2]
ok signature of S3 [0, 0]
ok signature of S4 [1, 0]
ok S1 and S4 are candidates True

all ok
Note that S1 and S4 agree in both signature positions, which estimates
their similarity as 1.0 when it is actually 2/3. Two hashes is not many.
```

## Task 2: Brute Force와 LSH의 crossover

| 문서 수 | Brute Force 시간 / 비교 횟수 | LSH 시간 / 비교 횟수 |
|---:|---:|---:|
| 250 | 0.39초 / 31,125회 | 2.04초 / 1회 |
| 500 | 1.25초 / 124,750회 | 3.52초 / 7회 |
| 1,000 | 5.31초 / 499,500회 | 7.03초 / 27회 |
| 2,000 | 25.03초 / 1,999,000회 | 14.25초 / 111회 |

Brute Force의 비교 횟수는 문서 수의 제곱에 비례한다. 문서 수를 두 배로 늘릴 때 비교 횟수가 약 네 배가 되었고, 실행 시간도 0.39→1.25→5.31→25.03초로 증가하여 quadratic한 경향을 보였다. 작은 입력에서는 LSH의 해싱과 버킷 생성 초기 비용 때문에 Brute Force가 더 빨랐다. 두 방법의 crossover는 1,000과 2,000 사이에서 나타났으며, 2,000개에서는 LSH가 14.25초로 더 빨랐다. Brute Force가 25.03초 걸린 2,000개 지점부터 실험이 상당히 부담스러워졌다.

실행 결과:

```text
n= 250 brute 0.39s 31,125 cmp | lsh 2.04s 1 cmp
n= 500 brute 1.25s 124,750 cmp | lsh 3.52s 7 cmp
n= 1000 brute 5.31s 499,500 cmp | lsh 7.03s 27 cmp
n= 2000 brute 25.03s 1,999,000 cmp | lsh 14.25s 111 cmp

-> out/crossover.json (9 measurement(s))
Keep raising --sizes until something becomes unpleasant. Record where.
```

## Task 3: 유사 문서 탐색 성능

사용한 설정은 해시 함수 100개, 밴드 25개, 밴드당 행 4개이다. 후보 생성 확률은 `P(candidate) = 1 - (1 - s^r)^b`이고 S-curve의 중심은 `(1/25)^(1/4) ≈ 0.447`이다. 이는 목표 임계값 0.6보다 낮으므로 높은 recall을 우선한 설정이다. 유사도 0.6에서 후보가 될 확률은 약 0.969이다. 중심을 임계값보다 높이면 비교 횟수는 줄어들 수 있지만 유사 쌍을 놓칠 위험이 커진다.

2,120개 문서에서 실제 유사 쌍은 121개였다. Brute Force는 2,246,140번 비교하여 recall과 precision이 모두 100.0%, 실행 시간은 16.64초였다. LSH는 128번만 비교하여 recall과 precision이 모두 100.0%, 실행 시간은 2.93초였다. 즉 비교의 99.99%를 피하면서 모든 유사 쌍을 찾았다. 다만 해싱과 버킷 생성 비용은 비교 횟수에 포함되지 않았으므로, 데이터 규모가 훨씬 커지면 이 선형적인 CPU 및 메모리 비용도 중요해질 것이다.

실행 결과:

```text
2,120 documents, threshold 0.6
121 truly similar pairs

baseline comparisons 2,246,140 recall 100.0% precision 100.0% 16.64s
yours comparisons 128 recall 100.0% precision 100.0% 2.93s

ok recall 100.0%
comparisons avoided 99.99% (128 instead of 2,246,140)

-> strong
```
