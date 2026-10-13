# Week 06 관찰 결과

## Task 1 · A-Priori와 연관 규칙
- support 2에서 `cola → diaper`의 confidence는 `2/2=1.0`, 반대 방향은 `2/5=0.4`였다. 따라서 confidence는 비대칭이며, 이 경우 cola를 산 고객이 diaper도 산다는 방향이 더 유용한 주장이다.
- support 3에서 최고 confidence는 `0.8`이고 `diaper → beer`를 포함한 네 규칙이 공동 1위였지만 lift는 `1.12`에 불과했다. beer 자체가 7개 basket 중 5개에 등장하므로 강한 관계라고 단정하기 어렵다.
- 2,000개 item을 brute force로 세면 `2,000×1,999/2=1,999,000`개 counter가 필요하다. 이 데이터에서 support 50의 A-Priori는 pass 1 뒤 실제로 820,259개 pair counter만 만들었고, `python task1_apriori.py --verify`의 5개 검사를 모두 통과했다.

## Task 2 · Support 임계값과 폭발
- 가능한 최저 support 1까지 실행했으며 실패나 RAM 고갈은 없었지만, 893,456개 답을 반환하면서 10.60초와 peak 327.1MB가 필요해 가장 부담스러웠다. 모든 관측 pair가 답이 되어 더 낮출 유효 임계값이 없었고, 먼저 압박한 자원은 메모리였다.
- support를 400→200→100→50으로 반씩 낮추자 peak counter는 10,585→58,626→273,701→820,259개로 각각 약 5.54배, 4.67배, 3.00배 증가했다. 이후에는 관측된 pair 수인 893,456개 부근에서 포화되었다.
- 같은 구간의 frequent pair 답은 249→776→2,244→6,397개로 counter보다 훨씬 적었다. support 50에서는 답 하나를 찾기 위해 약 128개의 후보 counter가 필요했으므로, “임계값을 조금만 낮추기”도 정답 수보다 후보 메모리를 먼저 폭발시킨다.

## Task 3 · PCY로 pass 2 줄이기
- 100,003개 해시 버킷을 선택해 약 300만 번의 pair 출현을 분산했다. 실행 결과 기준과 동일한 6,397개 frequent pair를 찾았고, peak pair counter는 820,259개에서 149,706개로 81.7% 줄어 `strong` 판정을 받았다.
- 버킷 배열도 공짜가 아니며 pass 1에서 100,003개의 정수(대략 3.6MB)로 존재한다. pass 2 전에 이를 약 0.1MB bitmap으로 바꾸므로 이 데이터에서는 이득이지만, baseline의 고유 pair counter가 대략 10만 개보다 적은 작은 데이터에서는 고정 버킷 비용 때문에 PCY가 이기지 못하며 crossover는 pair 분포와 support에 따라 달라진다.
- 버킷을 10,007개로 줄이자 충돌로 거의 모든 버킷이 frequent해져 peak counter가 baseline과 같은 820,259개가 되었다. 30,001개에서는 781,736개, 100,003개에서는 149,706개였으므로 너무 작은 배열은 bitmap의 필터 효과를 없앤다.
