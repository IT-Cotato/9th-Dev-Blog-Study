<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsqnzB%2FbtsG2XPBqoh%2Fkkqmw9w4GsaYxzXbsX0ook%2Fimg.jpg" originWidth:458 originHeight:588 style:alignCenter link = "https://product.kyobobook.co.kr/detail/S000001810273" isLinkNewWndow : true>

## 그래프

노드 Node(정점 Vertex), 간선 Edge

그래프 탐색 : 하나의 노드를 시작으로 다수의 노드를 방문하는것.

두 노드가 간선으로 연결되어 있다면, 두 노드는 인접하다고 표현한다.

- 인접 행렬 Adjacency Matrix : 2차원 배열로 연결 관계 표현
- 인접 리스트 Adjacency List : 연결 리스트로 연결 관계 표현

#### 예제 5-6. 인접 행렬

자바스크립트에서 무한대 숫자를 의미하는 `Infinity`를 이용해 연결되어있지 않다고 표시한다.

```
const INF = Infinity;

const graph = [
    [0,7,5],
    [7,0,INF],
    [5,INF,0]
]

console.log(graph);
```

0,7,5 노드가 연결된 노드들을 표현한다.

#### 예제 5-7. 인접 리스트

\[노드, 거리\] 형식

```
const graph = Array.from(Array(3), () => []);

//[노드, 거리] 형식
graph[0].push([1, 7]);
graph[0].push([2, 5]);

graph[1].push([0, 7]);

graph[2].push([0, 7]);

//배열
const graph = [
  [
    [1, 7],
    [2, 5],
  ],
  [[0, 7]],
  [[0, 5]],
];

console.log(graph);
```

0 노드와 연결된 노드 1, 거리는 7

0 노드와 연결된 노드 2, 거리는 5

1노드, 2노드와 연결된 노드 0, 거리는 7/5

<br>
인접 행렬 방식은 모든 관계를 저장해 노드의 개수가 많을수록 불필요한 메모리 낭비가 발생하지만,

인접 리스트 방식은 연결된 정보만 저장하기 때문에 미모리가 효율적이다.

그러나 인접 리스트 방식은 연결된 데이터를 하나씩 확인해야 하기 때문에 조금 속도가 느리다.

<br>

주로 코딩테스트 문제에서는 주어진 txt 파일에 첫줄에는 문제의 조건,

이후에는 그래프 데이터를 인접 행렬 또는 인접 리스트 방식으로 표현해 데이터를 준다.

### \+ JS 객체 사용

사실 **자바스크립트의 배열은 객체**이기 때문에, 노드의 값이 숫자가 아닌 경우에 유용하게 사용된다.

```
const graph = {
  'A': ['B', 'C'],
  'B': ['C', 'D'],
  'C': ['D'],
  'D': ['C'],
  'E': ['F'],
  'F': ['C']
};
```

```
const graph = [
  { node: 'A', edges: ['B', 'C'] },
  { node: 'B', edges: ['C', 'D'] },
  { node: 'C', edges: ['D'] },
  { node: 'D', edges: ['C'] },
  { node: 'E', edges: ['F'] },
  { node: 'F', edges: ['C'] }
];
```

## DFS 깊이 우선 탐색

Depth_First Search

그래프에서 깊은 부분을 우선적으로 탐색하는 알고리즘

특정한 경로를 최대한 깊숙히 들어가서 노드를 방문한 수, 다시 돌아가 다른 경로로 탐색하는 알고리즘

**스택, 재귀함수**를 사용한다.

데이터의 개수가 N 개인 경우 O(N) 의 시간이 소요된다.

<br>

### 동작 과정

1.  탐색 시작 노드를 스택에 삽입하고, **방문 처리**를 한다.
2.  스택의 최상단 노드
    - 방문하지 않은 인접 노드가 **있으면**, 그 인접 노드를 스택에 넣고 방문 처리를 한다.
    - 방문하지 않은 인접 노드가 **없으면**, 스택에서 최상단 노드를 꺼낸다.
3.  2번 과정을 더이상 수행할 수 없을 때까지 반복한다.

<br>

#### 예제 5-8. 인접 리스트 방식

```
const dfs = (graph, v, visited) => {
  //탐색 시작 노드 visited
  visited[v] = true;
  console.log(v);

  //탐색 노드의 인점 노드 확인
  for (const cur of graph[v]) {
    if (!visited[cur]) {
    //재귀적으로 지속적 탐색
      dfs(graph, cur, visited);
    }
  }
};

let graph = [
  [],
  [2, 3, 8],
  [1, 7],
  [1, 4, 5],
  [3, 5],
  [3, 4],
  [7],
  [2, 6, 8],
  [1, 7],
];

//방문처리할 빈 배열
let visited = [...Array(9).fill(false)];

dfs(graph, 1, visited);
```

## BFS 너비 우선 탐색

Breadth-First Search

가까운 노드부터 탐색하는 알고리즘

**큐** 이용하지만, n의 크기가 작다면 그냥 배열로 구현해도 무방하다.

O(N)의 시간 복잡도

\*DFS 보다 BFS가 조금 더 빠르게 동작한다 - 재귀함수 사용시 예외

<br>

### 동작 과정

1.  탐색 시작 노드를 큐에 삽입하고 방문 처리를 한다
2.  큐에서 들어간지 제일 오래된 **노드를 꺼내** 해당 노드의 *인접 노드 중에서 방문하지 않은 노드*를 **모두 큐에 넣고 방문처리** 한다.
3.  2번의 과정을 더이상 수행할 수 없을 때까지 반복한다.

<br>

#### 예제 5-9. 인접리스트 구현한 Queue 사용

자바스크립트에는 큐 내장 메서드가 없기에, 사용하려면 앞선 [03-1 스택, 큐, 재귀함수](https://datdaradanadat.tistory.com/114#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%97%B0%EA%B2%B0%EB%A6%AC%EC%8A%A4%ED%8A%B8%20%ED%81%90-1) 글에서 설명한 코드를 가져와야 한다.

```
//JS 큐 구현
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

class Queue {
  constructor() {
    this.front = null;
    this.rear = null;
    this.size = 0;
  }

  isEmpty() {
    return this.size === 0;
  }

  enQueue(data) {
    const newNode = new Node(data);
    if (this.isEmpty()) {
      this.front = newNode;
    } else {
      this.rear.next = newNode;
    }
    this.rear = newNode;
    this.size++;
  }

  deQueue() {
    if (this.isEmpty()) {
      return null;
    }
    const data = this.front.data;
    this.front = this.front.next;
    this.size--;
    return data;
  }
}

//BFS 구현
const bfs = (graph, start, visited) => {
  //탐색 시작 노드 Queue, visited
  const q = new Queue();
  q.enQueue(start);
  visited[start] = true;	//방문처리

  //큐가 빌때까지 반복
  while (!q.isEmpty()) {
  	//큐에서 하나의 원소를 뽑아 출력
    const v = q.deQueue();
    console.log(v);

    //탐색 노드의 방문하지 않은 인접 노드 큐에 삽입
    for (const cur of graph[v]) {
      if (!visited[cur]) {
        q.enQueue(cur);
        visited[cur] = true;
      }
    }
  }
};

let graph = [
  [],
  [2, 3, 8],
  [1, 7],
  [1, 4, 5],
  [3, 5],
  [3, 4],
  [7],
  [2, 6, 8],
  [1, 7],
];

//방문처리할 빈 배열
let visited = [...Array(9).fill(false)];

bfs(graph, 1, visited);
```

## TIP

N x N 형태의 2차원 배열이고 데이터를 좌표라고 생각한다.

상하좌우로만 이동할 수 있는 그래프 형태로 바꿔서 풀이법을 고민해본다.

실제로 그래프를 그려서 규칙을 찾다보면 더 나은 방식을 찾을 수 있다.

<br>

## 실전문제

### 음료수 얼려 먹기 DFS

#### 문제

N x M 크기의 얼음 틀이 있다. 구멍이 뚫려 있는 부분은 0, 칸막이가 존재하는 부분은 1로 표시된다. 구멍이 뚫려 있는 부분끼리 상, 하, 좌, 우로 붙어 있는 경우 서로 연결되어 있는 것으로 간주한다. 이때 얼음 틀의 모양이 주어졌을 때 생성되는 총 아이스크림의 개수를 구하는 프로그램을 작성하시오. 다음의 4 x 5 얼음 틀 예시에서는 아이스크림이 총 3개 생성된다.

#### 입력 조건

- 첫 번째 줄에 얼음 틀의 세로 길이 N과 가로 길이 M이 주어진다. (1 ≤ N, M ≤ 1,000)
- 두 번째 줄부터 N + 1번째 줄까지 얼음 틀의 형태가 주어진다.
- 이때 구멍이 뚫려있는 부분은 0, 그렇지 않은 부분은 1이다.

#### 출력 조건

- 한 번에 만들 수 있는 아이스크림의 개수를 출력한다.

#### 💡풀이

총 개수. 쭉 탐색했다가 다 탐색하면 다시 돌아와서 반복하는  DFS 방식이라고 생각했다. → 단순히 모든 정점을 탐색하는 문제여서 BFS도 가능하다고 한다.

\*입력정제

얼음 틀의 형태를 graph에 맵 정보를 입력 받는다.

visited n행, m열 2차원 생성하는 법 `Array.from()` 메서드 + 두번째 요소에 반복하면서 false 채움

<br>

dfs 함수로 4방향을 지정해서 좌표 계산하고 (for문)

n,m 주어진 범위 내에 존재하는지 확인

현재 노드 방문안했고 and 구멍이 뚫려있는 부분 (0) 이라면 다음 노드 dfs함수 호출

<br>

solution 함수에서 이중 반복문으로 모든 정점을 순회한다.

현재 노드에서 방문처리 안했고, 구멍이 뚫려있는 부분 (0) 이라면 dfs 함수를 실행하겠다. → 연결되었기 때문에, 아이스크림의 개수를 `++` 증가시킨다.

```
let input = require("fs").readFileSync("/dev/stdin").toString().trim().split("\n");

const [nm, ...arr] = input;
const [n, m] = nm.split(" ").map((v) => +v);
const graph = arr.map((v1) => v1.split(""));

const dir = [[0, 1],[0, -1],[1, 0],[-1, 0],];
const visited = Array.from(Array(n), () => Array(m).fill(false));

const dfs = (graph, x, y) => {
  visited[x][y] = true;

  for (let i = 0; i < 4; i++) {
    const nx = x + dir[i][0];
    const ny = y + dir[i][1];

    if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;

    if (!visited[nx][ny] && graph[nx][ny] === "0") {
      dfs(graph, nx, ny);
    }
  }
};

function solution(n, m, graph) {
  let count = 0;

  for (let i = 0; i < n; i++) {
    for (let j = 0; j < m; j++) {
      const cur = graph[i][j];
      if (!visited[i][j] && cur === "0") {
        dfs(graph, i, j);
        count++;
      }
    }
  }

  return count;
}

console.log(solution(n, m, graph));
```

단순 완전 탐색이 아닌, dfs가 연결되어있는 횟수를 count 하는 문제였다.

dfs 함수까지는 잘 구현했는데, solution 함수와, dfs 탐색이 연결이 끊어졌을때의 조건이 고민되었다.

dfs 함수의 if 문으로 연결이 끊어지는 부분을 탐색하지 않는다는 처리를 하고, solution 함수에서 모든 부분이 연결이 끊어졌을때 count++ 처리를 해 개수를 구할 수 있었다.

<br>

### 미로 탈출 BFS

#### 문제

동빈이는 N x M 크기의 직사각형 형태의 미로에 갇혀 있다. 미로에는 여러 마리의 괴물이 있어 이를 피해 탈출해야 한다. 동빈이의 위치 (1, 1)이고 미로의 출구는 (N, M)의 위치에 존재하여 한 번에 한 칸씩 이동할 수 있다. 이때 괴물이 있는 부분은 0으로, 괴물이 없는 부분은 1로 표시되어 있다. 미로는 반드시 탈출할 수 있는 형태로 제시된다. 이때 동빈이가 탈출하기 위해 움직여야 하는 최소 칸의 개수를 구하시오. 칸을 셀 때는 시작 칸과 마지막 칸을 모두 포함해서 계산한다.

#### 입력 조건

- 첫째 줄에 두 정수 N, M(4 ≤ N, M ≤ 200)이 주어집니다. 다음 N개의 줄에는 각각 M개의 정수(0 혹은 1)로 미로의 정보가 주어진다. 각각의 수들은 공백 없이 붙어서 입력으로 제시된다. 또한 시작 칸과 마지막 칸은 항상 1이다.

#### 출력 조건

- 첫째 줄에 최소 이동 칸의 개수를 출력한다.

#### 💡풀이

최단 경로. 최소 개수. 이런 말이 나오면 BFS로 풀이하는게 좋다. 시작위치에서 가까운 노드부터 완전 탐색 하기 때문이다.

\*입력 정제 → DFS와 동일하다.

큐가 아닌, 그냥 배열로 구현을 했다.

<br>

**bfs 함수**에서 시작점 sx, sy를 배열에 넣고 방문처리를 한다.

배열이 빌때까지 탐색을 하는데 → bfs는 꺼냈다가 값을 삽입했다가 계속 반복해야 한다.

shift 로 배열에서 제일 앞에 있는, 오래된 노드를 x,y에 할당한다.

<br>

4방향을 지정해서 좌표 계산하고 (for문) 범위 체크한다.

방문한적 없고, 괴물이 없는 부분 (1)일 경우,

다음 노드를 배열에 넣고, 방문처리한다.

다른 노드로 이동할때마다 1씩 증가한다.

<br>

**solution 함수**으로 시작칸 (0,0)에서 출구 (N-1, M-1) 까지의 최단거리를 출력한다.

```
let input = require('fs').readFileSync("/dev/stdin").toString().trim().split('\n');

const [nm, ...arr] = input;
const [n, m] = nm.split(' ').map(v => +v);
const graph = arr.map(v1 => v1.split('').map(v2 => +v2));

const dir = [[0, 1], [0, -1], [1, 0], [-1, 0]];
const visited = Array.from(Array(n), () => Array(m).fill(false));

const bfs = (graph, sx, sy) => {
  const q = [];
  q.push([sx, sy]);
  visited[sx][sy] = true;

  while (q.length !== 0) {
    const [x, y] = q.shift();

    for (let i = 0; i < 4; i++) {
      const nx = x + dir[i][0];
      const ny = y + dir[i][1];

      if (nx < 0 || nx >= n || ny < 0 || ny >= m)
        continue;

      if (!visited[nx][ny] && graph[nx][ny]) {
        q.push([nx, ny]);
        visited[nx][ny] = true;
        graph[nx][ny] = graph[x][y] + 1;
      }
    }
  }
}

function solution(n, m, graph) {
  bfs(graph, 0, 0);
  return graph[n - 1][m - 1];
}

console.log(solution(n, m, graph));
```

개인적으로 DFS가 조금더 익숙하고, BFS가 조금 더 어려운 것 같다. 자료구조는 이해했지만, 코드로 구현해내는 흐름이 잘 안떠올랐다.

이 문제는 **탐색 횟수 측정**이다. 자바스크림트에서 큐를 구현해내지 못해도, 배열을 활용해 흉내내서 구현할 수 있다는 것을 앞에서 배워서 그나마 풀 수 있었다.

graph\[x\]\[y\]가 현재 정점까지의 최단거리를 나타내고, 이 값에 1을 더해 새로운 정점까지의 최단 거리를 계산한다는 풀이과정에서 이해가 되지 않아 혼자 풀어내지 못했다😢

## ☑️ 배운 점

이렇게 보면 쉬워보이지만, 문제 조건에 따라 겁나 어려울 수도 쉬울 수도 있는 것이 DFS BFS 풀이..

파이썬 구현보다 자바스크립트로 구현하는 것이 조금 더 복잡한 것 같다. 특히 Queue의 자유로운 사용이 안된다는 단점이 치명적이다만.. 어떻게든 코드 풀이는 있다!

DFS, BFS 둘 중 어떤 알고리즘이 적합한지 문제마다 다르고, 자료구조 + 문제조건을 부합하려면 DFS, BFS 풀이공식을 넘어서 많은 응용을 필요로 한다. 넓게 학습하고 많이 풀어서 익숙해져야 할 것 같다.

알고리즘 고수들의 훈련법이 궁금합니당~~
