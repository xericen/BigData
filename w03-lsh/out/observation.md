# Week 03 Observation

## Task 1
MinHash is row-first: each row is visited once and updates every document containing it, avoiding a separate scan for each column. If signature length is not divisible by the band count, the implementation rejects the input with `ValueError` rather than silently dropping rows. The textbook S1/S4 estimate is 1.0 versus true Jaccard 2/3; more hash functions would narrow this variance but increase signature and hashing cost.

## Task 2
The measured crossover was around n=2,000. Brute-force time ratios for 500→1,000 and 1,000→2,000 were 4.33× and 4.24×, consistent with quadratic comparison growth. The supplied generator stops at 2,120 documents, so the recorded n=4,000 point is explicitly the capped dataset rather than a true 4,000-document run.

## Task 3
The final setting is 100 hashes, 25 bands, and 4 rows per band with a fixed seed. The S-curve step is `(1/25)^(1/4) ≈ 0.447`, and at s=0.6, `P(candidate) = 1-(1-0.6^4)^25 ≈ 0.969`; placing the step below the threshold favors recall, while moving it above the threshold would reduce comparisons but lose planted pairs. The benchmark achieved 100.0% recall and 99.99% comparisons avoided; at much larger scale, the currently free hashing/bucketing work would become a significant linear CPU and memory cost.
