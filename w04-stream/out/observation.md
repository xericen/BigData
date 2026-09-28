# Task 1

```text
> python task1_sketches.py --verify
  ok    no false negatives
  ok    measured false-positive rate matches theory    measured 0.790%, predicted 0.860%
  ok    distinct estimate within a factor of 2         estimated 18,202, true 19,953 (0.91x)
  ok    reservoir is uniform across items              spread 8.9% around 1000

  all ok
```

- Bloom filter는 `add`에서 지정된 모든 비트를 1로 설정하고 조회 시 같은 비트만 확인하며 비트를 다시 0으로 바꾸지 않으므로 거짓 음성이 없다. 이론값과 측정값의 작은 차이는 유한한 실험 횟수와 해시의 확률적 편차 때문이다.
- 64개의 레지스터와 조화 평균을 사용했으며, 산술 평균·중앙값·기하 평균은 각각 795,623개·23,233개·31,463개로 특히 산술 평균이 이상값의 영향을 크게 받았다. Reservoir sampling은 `rng.randrange(i + 1)`에서 현재 원소를 `k/(i+1)` 확률로 기존 표본과 교체하므로 최종 길이를 몰라도 `k`개만 균일하게 유지한다.

# Task 2

```text
> python task2_limits.py --sizes 100000,200000,400000,1600000
  n=   100,000  distinct    36,702   exact    0.53s      3.9 MB   |  fm    2.16s   0.00 MB  0.95x
  n=   200,000  distinct    73,410   exact    1.11s      5.8 MB   |  fm    4.34s   0.00 MB  1.05x
  n=   400,000  distinct   146,970   exact    2.13s     11.6 MB   |  fm    7.88s   0.00 MB  1.01x
  n= 1,600,000  distinct   587,625   exact    8.48s     46.6 MB   |  fm   31.83s   0.00 MB  0.76x

> python task2_limits.py --sizes 6400000
  n= 6,400,000  distinct 2,349,909   exact   39.21s    188.3 MB   |  fm  138.98s   0.00 MB  0.99x
```

- 정확한 계산은 640만 건에서 39.21초와 188.29MB가 필요했으므로 이 지점을 부담스러운 크기로 정했으며, 사용 가능한 메모리보다 실행 시간이 먼저 실질적인 한계가 되었다.
- 입력이 64배 증가할 때 exact의 최대 메모리는 47.96배 증가해 O(n)에 가까웠지만, FM은 4,389바이트에서 4,393바이트로 거의 일정해 O(1)이었다. FM 정확도는 0.756배에서 1.051배 사이였으며 입력 증가에 따라 단조롭게 좋아지지는 않았다.
- 2배 이내의 추정은 서버 용량 계획이나 일일 방문자 추세 파악에는 충분할 수 있지만, 사용자 수가 금액이나 권한에 직접 영향을 주는 과금·라이선스·정산 용도에는 적합하지 않다.

# Task 3

```text
> python bench.py --yours
  80,000 bits, 8,000 items inserted, 200,000 queried

  baseline   false positives  19,023  ( 9.511%)   false negatives     0   bits 80,000
  yours      false positives   1,663  ( 0.831%)   false negatives     0   bits 80,000

   ok    no false negatives
   false-positive rate cut by 91.3%  (0.831% from 9.511%)

   -> strong
```

- 해시 함수 수는 `(1-e^{-kn/m})^k`를 최소화하는 `k = round((m/n) ln 2) = round(10 ln 2) = 7`로 정했다. 원소당 10비트의 이론적 최저 거짓 양성률 `(0.6185)^10 ≈ 0.819%`와 측정값의 차이는 0.012%p였다.
- `n`을 모르는 경우에는 확장형 Bloom filter를 사용해 기존 필터가 용량에 가까워질 때 오차율이 더 낮은 새 계층을 추가할 수 있다. `n`을 너무 작게 예상하면 비트가 포화되어 거짓 양성이 늘고, 너무 크게 예상하면 메모리가 낭비된다.
