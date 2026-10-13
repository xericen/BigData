# PageRank convergence measurements

## Beta sweep

Measured on the 1,200-node graph with `tol=1e-10`.

| beta | iterations | wall time (s) |
|---:|---:|---:|
| 0.50 | 14 | 0.075 |
| 0.70 | 17 | 0.161 |
| 0.85 | 20 | 0.150 |
| 0.95 | 23 | 0.200 |
| 0.99 | 24 | 0.241 |

The iteration curve rises as beta approaches 1. Teleportation then contributes
less mixing, so link-following retains more of the previous state and errors
decay more slowly.

## Graph size

| beta | nodes | iterations | wall time (s) |
|---:|---:|---:|---:|
| 0.85 | 1,200 | 20 | 0.150 |
| 0.85 | 6,000 | 22 | 1.153 |
| 0.95 | 1,200 | 23 | 0.200 |
| 0.95 | 6,000 | 24 | 1.033 |

Increasing the graph by 5x changed convergence by only one or two iterations,
but the beta=0.85 wall time rose by about 7.7x. Iteration count depends mainly
on the transition dynamics and tolerance; each iteration's work grows with the
number of nodes and edges. Timing also contains normal system noise.

## Tolerance

At beta 0.85 on 1,200 nodes, `tol=1e-6` took 12 iterations and `tol=1e-10`
took 20. Four additional decimal digits therefore cost eight iterations here,
or about two extra iterations per digit.

## Top-10 sensitivity

The same ten pages remained in the top ten for every beta. Their order first
changed at beta 0.70: `p00000` and `p00004` swapped relative to beta 0.50, while
the leading six pages were stable. Thus the selected beta can affect published
positions even when the high-ranking set appears robust.

## Machine

- Windows 10 (10.0.19045), Python 3.13.1
- Intel Core i7-7700HQ at 2.80 GHz, 17,024,520,192 bytes (about 15.9 GiB) RAM
- Visual Studio Code, Chrome, Windows Defender, and Codex were running
