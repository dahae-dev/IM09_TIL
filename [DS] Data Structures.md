# Data Structures



#### 1. Linked List

node가 연결된 linear한 구조

하나의 node는 value(info), next(다음 node에 대한 주소값)에 대한 정보를 가지고 있다

맨 앞의 node는 head, 맨 뒤의 node는 tail

cf) array : index를 통해 값에 직접 접근 가능, 

​		 자바스크립트에서 array는 object

*** Memory Allocation**

Static : arrays - 일련의 연결된 공간에 할당 cf) 자바스크립트에서는 array가 object이기 때문에 static한 구조로 저장되지 않는다

Dynamic: linked lists - 남는 공간에 적절히 할당

사용 예) 웹사이트의 이동 (go back / go foward)



#### 2. Tree

root - node - leaf가 연결된 hierarchical한 구조

root : parent가 없고 children만 있는 경우

node: parent와 children을 모두 가지고 있는 경우

leaf: parent만 있고 children이 없는 경우

사용 예) DOM tree

*** Binaray Search Tree**

Child node를 2개까지만 가질 수 있다

정렬/탐색 알고리즘 : BFS(Breadth-First Search), DFS(Depth-First Search)



#### 3. Graph

directed graph, undirected graph



#### 4. Hash Table

hash function에 의해 input에 대한 unique한 값을 output으로 내어준다

반대 방향으로는 작동하지 않는다

*** 회피 알고리즘 (collision handling)**

separate chaining

opening addressing

사용 예) 인증, 검색(주소록, 전화번호부), 자바스크립트의 object