## BOJ_16236_G3_아기 상어
- 구현, 너비 우선 탐색
- https://www.acmicpc.net/problem/16236


## 풀이

주어진 조건에맞춰 시뮬레이션을 돌리는 문제로,
상어는 자기보다 작은 물고기를 먹을수 있고,
자기 크기만큼의 물고기를 먹으면 크기가 1 커지고,
자기와 크기가 같거나 작은 물고기가 있는 위치만을 지나갈 수 있습니다.

<br>

```cpp
inline void babyShark() // 상어가 몇초까지 먹이를 먹을수 있는지 찾는 함수
inline void searchPrey() // 다음먹이를 찾아나서는 함수
inline bool isValid(int x, int y) // 유효한 범위를 돌아다니게끔 해주는 함수

```

<br>

위의 3가지 함수를 중심으로 문제를 해결하였습니다.
babyShark 함수에는 searchPrey 함수와
현재 시간과 위치 그리고 상어의 크기를 갱신해주는 역할을 하고 있습니다.

<br>

그리고 searchPrey 함수에서는 BFS를 통한 탐색으로
유효한 위치만을 돌아다니며 먹이를 찾아다니는 역할을 하고 있습니다.
이때 유효한 위치를 찾는 역할은 
isValid 함수와, searchPrey함수내부의 if문을 활용해 처리하였습니다.

<br>



## 소스코드
```cpp
#include <bits/stdc++.h>
#define fastio cin.tie(0)->ios::sync_with_stdio(0); cout.tie(0); setvbuf(stdout, nullptr, _IOFBF, BUFSIZ);
#define INF INT32_MAX
// INT32_MIN, INT64_MIN, INT32_MAX, INT64_MAX
using namespace std;
using int64 = int64_t;

int N;
int Mp[20][20];
int sz = 2, xp = 2, tim = 0;
int dx[4] = { -1, 0, 1, 0 }, dy[4] = { 0, 1, 0, -1 };
vector<tuple<int64, int64, int64>> prey; //dist, x, y;
queue<tuple<int64, int64, int64>> q;

//유효한 위치인가?
inline bool isValid(int x, int y) {
	return (x >= 0 && x < N&& y >= 0 && y < N);
}

//먹이는 prey벡터에 저장, 다음위치는 q큐에 저장
inline void searchPrey() {
	bool vst[20][20] = { false, };

	while (!q.empty()) {
		int dist = get<0>(q.front());
		int x = get<1>(q.front());
		int y = get<2>(q.front());

		q.pop();

		for (int i = 0; i < 4; ++i) {
			int next_x = x + dx[i];
			int next_y = y + dy[i];

			if (isValid(next_x, next_y) && !vst[next_x][next_y] && Mp[next_x][next_y] <= sz) {
				vst[next_x][next_y] = true;
				if (Mp[next_x][next_y] == 0 || Mp[next_x][next_y] == sz) q.push({ dist + 1, next_x, next_y });
				else prey.push_back({ dist + 1, next_x, next_y });
			}
		}
	}
}

inline void babyShark() {
	searchPrey();

	//더이상 먹을수 있는 먹이가 없을때까지 반복
	while (!prey.empty()) {
		sort(prey.begin(), prey.end());

		int dist = get<0>(prey.front());
		int x = get<1>(prey.front());
		int y = get<2>(prey.front()); 
		tim += dist; // 시간 더하기

		//레벨업
		if (xp == 1) {
			sz++;
			xp = sz;
		}
		else xp--;

		//지나간위치 0
		Mp[x][y] = 0;

		//다음먹이 찾기
		q.push({ 0,  x, y });
		prey.clear();

		searchPrey();
	}

	cout << tim;
}

int main() {
	fastio;

	cin >> N;
	
	//맵 맵핑과, 아기상어 위치 저장
	for (int i = 0; i < N; ++i) {
		for (int j = 0; j < N; ++j) {
			cin >> Mp[i][j];

			if (Mp[i][j] == 9) {
				q.push({ 0, i, j });
				Mp[i][j] = 0;
			}
		}
	}

	babyShark(); // 먹이를 찾아보자

	return EXIT_SUCCESS;
}
```


<br/>


## 결과 

| 메모리 | 시간 |
| ------ | ---- |
| 2160KB | 0ms |


