# Task 2 Crossover

## Machine
- CPU: Intel64 Family 6 Model 158 Stepping 9 (Windows 10)
- RAM: not available through the restricted benchmark environment
- Python: 3.13.1
- Measurements used `task2_crossover.py` with the synthetic `bench.build()` dataset; no extra workload was recorded.

## Results

| n | Brute time (s) | Brute comparisons | LSH time (s) | LSH comparisons |
|---:|---:|---:|---:|---:|
| 250 | 0.300 | 31,125 | 1.917 | 1 |
| 500 | 2.194 | 124,750 | 6.608 | 7 |
| 1000 | 9.497 | 499,500 | 12.957 | 27 |
| 2000 | 40.234 | 1,999,000 | 23.857 | 111 |
| 4000* | 28.347 | 2,246,140 | 25.595 | 128 |

`*` `bench.build()` contains only 2,120 documents, so slicing at 4,000 measures the full dataset rather than n=4,000. This is the generator limit.

## Quadratic check

For 250→500, the brute-force time ratio was 7.31; for 500→1000 it was 4.33; and for 1000→2000 it was 4.24. The comparison counts exactly follow n(n−1)/2, and the latter two timings are close to the expected 4× increase. The 250-point measurement is affected by startup and constant overhead.

## Crossover

Brute force was faster through n=1,000 (0.300, 2.194, and 9.497 seconds versus LSH's 1.917, 6.608, and 12.957). LSH became faster at n=2,000, the observed crossover on this machine. LSH pays linear preprocessing overhead for 100 MinHash passes and 25 bucket-building passes before it performs the small number of expensive similarity comparisons.

## Limit

The supplied generator caps the usable dataset at 2,120 documents, so the requested 4,000 measurement is recorded as the capped full dataset. No memory pressure occurred; the largest measured LSH peak was 3,162,880 bytes. A larger-size crossover curve requires changing the generator, which was intentionally left untouched.
