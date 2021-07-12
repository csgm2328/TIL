## 210712 [BOJ 2656 - 중앙값 찾기](https://www.acmicpc.net/problem/2696)
- 중앙값을 찾기 위해 배열을 계속 찾을 떄마다 정렬해주는 게 아니라
- MaxHeap & MinHeap 을 이용해서 양쪽 힙의 개수를 맞추면서 채우고 MaxHeap에서 가장 큰 수가 MinHeap의 가장 작은 수보다 크도록 유지하면
- 자연스럽게 정렬이 되고 둘 중에 먼저넣은 큐에서 맨 위를 뽑으면 중앙값이 된다.
- [코드보기](https://github.com/csgm2328/Algorithm/blob/master/BOJ/_2696_%EC%A4%91%EC%95%99%EA%B0%92_%EA%B5%AC%ED%95%98%EA%B8%B0.java)