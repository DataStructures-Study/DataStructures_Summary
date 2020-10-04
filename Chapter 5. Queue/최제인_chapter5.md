### 5장

- 8번

  ```
  #include <stdio.h>
  #include <stdlib.h>
  
  // ===== 원형큐 코드 시작 ======
  #define MAX_QUEUE_SIZE 5
  typedef int element;
  typedef struct { // 큐 타입
  	element  data[MAX_QUEUE_SIZE];
  	int  front, rear;
  } QueueType;
  
  // 오류 함수
  void error(char* message)
  {
  	fprintf(stderr, "%s\n", message);
  	exit(1);
  }
  
  // 공백 상태 검출 함수
  void init_queue(QueueType* q)
  {
  	q->front = q->rear = 0;
  }
  
  // 공백 상태 검출 함수
  int is_empty(QueueType* q)
  {
  	return (q->front == q->rear);
  }
  
  // 포화 상태 검출 함수
  int is_full(QueueType* q)
  {
  	return ((q->rear + 1) % MAX_QUEUE_SIZE == q->front);
  }
  
  int get_count(QueueType* q) {
  	
  	int count = q->rear - q->front;
  	if (count < 0) count += MAX_QUEUE_SIZE;
  	return count;
  }
  // 원형큐 출력 함수
  void queue_print(QueueType* q)
  {
  	printf("QUEUE(front=%d rear=%d) = ", q->front, q->rear);
  	if (!is_empty(q)) {
  		int i = q->front;
  		do {
  			i = (i + 1) % (MAX_QUEUE_SIZE);
  			printf("%d | ", q->data[i]);
  			if (i == q->rear)
  				break;
  		} while (i != q->front);
  	}
  	printf("\n");
  }
  
  // 삽입 함수
  void enqueue(QueueType* q, element item)
  {
  	if (is_full(q))
  		error("큐가 포화상태입니다");
  	q->rear = (q->rear + 1) % MAX_QUEUE_SIZE;
  	q->data[q->rear] = item;
  }
  
  // 삭제 함수
  element dequeue(QueueType* q)
  {
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	q->front = (q->front + 1) % MAX_QUEUE_SIZE;
  	return q->data[q->front];
  }
  
  // 삭제 함수
  element peek(QueueType* q)
  {
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	return q->data[(q->front + 1) % MAX_QUEUE_SIZE];
  }
  // ===== 원형큐 코드 끝 ======
  
  int main(void)
  {
  	QueueType queue;
  	int element;
  
  	init_queue(&queue);
  	printf("--데이터 추가 단계--\n");
  	while (!is_full(&queue))
  	{
  		printf("정수를 입력하시오: ");
  		scanf_s("%d", &element);
  		enqueue(&queue, element);
  		queue_print(&queue);
  	}
  	printf("큐는 포화상태입니다.\n\n");
  
  	printf("--데이터 삭제 단계--\n");
  	while (!is_empty(&queue))
  	{
  		element = dequeue(&queue);
  		printf("꺼내진 정수: %d \n", element);
  		queue_print(&queue);
  	}
  	printf("큐는 공백상태입니다.\n");
  	return 0;
  }
  ```

- 9번

  ```c
  #include<stdio.h>
  
  #define MAX_SIZE 100
  
  typedef struct {
  	int stack[MAX_SIZE];
  	int top;
  }Stacktype;
  typedef struct { // 큐 타입
  	Stacktype s1, s2;
  } QueueType;
  
  
  
  // 공백 상태 검출 함수
  
  
  void Stack_push(Stacktype* s, int item) {
  	s->stack[++(s->top)] = item;
  	return;
  }
  
  int is_empty(Stacktype* s) {
  	return ((s->top) == -1);
  }
  
  int Stack_pop(Stacktype* s) {
  	int t = s->stack[s->top];
  	s->stack[s->top] = 0;
  	s->top--;
  	return t;
  }
  
  typedef struct Queuetype {
  	Stacktype s1, s2;
  };
  
  void Queue_init(QueueType* q) {
  	q->s1.top = -1;
  	q->s2.top = -1;
  }
  
  void Queue_push(QueueType* q, int item) {
  	Stack_push(&(q->s1), item);
  	return;
  }
  
  int Queue_pop(QueueType* q) {
  	if (is_empty(&(q->s2))) {
  		while (!is_empty(&(q->s1))) {
  			Stack_push(&(q->s2), Stack_pop(&(q->s1)));
  		}
  	}
  	return 	Stack_pop(&(q->s2));
  }
  
  int main(void) {
  	QueueType q;
  	Queue_init(&q);
  	Queue_push(&q, 5);
  	Queue_push(&q, 4);
  	Queue_push(&q, 3);
  	Queue_push(&q, 2);
  	Queue_push(&q, 1);
  	printf(" %d |", Queue_pop(&q));
  	printf(" %d |", Queue_pop(&q));
  	printf(" %d |", Queue_pop(&q));
  	printf(" %d |", Queue_pop(&q));
  	printf(" %d |", Queue_pop(&q));
  	
  	}
  ```

  

- 10번

  ```c
  #include<stdio.h>
  #include<stdlib.h>
  
  #define QUEUE_MAX_SIZE 10
  typedef int element;
  
  typedef struct {
  	element Queue[QUEUE_MAX_SIZE];
  	int front, rear;
  }Queuetype;
  
  void init(Queuetype* q) {
  	q->front = q->rear = 0;
  	return;
  }
  
  int is_full(Queuetype* q) {
  	return ((q->rear + 1) % QUEUE_MAX_SIZE == q->front);
  }
  
  int is_empty(Queuetype* q) {
  	return q->front == q->rear;
  }
  
  void push(Queuetype* q, element item) {
  	if (!is_full(q)) {
  		q->rear = (q->rear + 1) % QUEUE_MAX_SIZE;
  		q->Queue[q->rear] = item;
  	}
  	else {
  		fprintf(stderr, "메모리가 가득찼습니다.\n");
  		exit(1);
  	}
  	return;
  }
  
  element pop(Queuetype* q) {
  	if (is_empty(q)) {
  		fprintf(stderr, "메모리가 비어있습니다.\n");
  		exit(1);
  	}
  	else {
  		q->front = (q->front + 1) % QUEUE_MAX_SIZE;
  		return q->Queue[q->front];
  	}
  }
  
  int fibonachi(Queuetype* q, int n) {
  	if (n == 0)
  		return 0;
  	else if (n == 1)
  		return 1;
  
  	for (int i = 2; i <= n; i++) {
  		int d = pop(q);
  		push(q, d + q->Queue[q->rear]);
  	}
  	return q->Queue[q->rear];
  }
  
  int main(void) {
  	Queuetype q;
  	init(&q);
  	push(&q, 0);
  	push(&q, 1);
  	int n = 4;
  	printf("%d", fibonachi(&q, n));
  }
  ```

  

- 11번

  ```c
  #include <stdio.h>
  #include <stdlib.h>
  
  #define MAX_QUEUE_SIZE 10
  typedef char element;
  typedef struct { // 큐 타입
  	element  data[MAX_QUEUE_SIZE];
  	int  front, rear;
  } DequeType;
  
  // 오류 함수
  void error(char* message)
  {
  	fprintf(stderr, "%s\n", message);
  	exit(1);
  }
  
  // 초기화 
  void init_deque(DequeType* q)
  {
  	q->front = q->rear = 0;
  }
  
  // 공백 상태 검출 함수
  int is_empty(DequeType* q)
  {
  	return (q->front == q->rear);
  }
  
  // 포화 상태 검출 함수
  int is_full(DequeType* q)
  {
  	return ((q->rear + 1) % MAX_QUEUE_SIZE == q->front);
  }
  
  // 원형큐 출력 함수
  void deque_print(DequeType* q)
  {
  	printf("DEQUE(front=%d rear=%d) = ", q->front, q->rear);
  	if (!is_empty(q)) {
  		int i = q->front;
  		do {
  			i = (i + 1) % (MAX_QUEUE_SIZE);
  			printf("%c | ", q->data[i]);
  			if (i == q->rear)
  				break;
  		} while (i != q->front);
  	}
  	printf("\n");
  }
  
  // 삽입 함수
  void add_rear(DequeType* q, element item)
  {
  	if (is_full(q))
  		error("큐가 포화상태입니다");
  	q->rear = (q->rear + 1) % MAX_QUEUE_SIZE;
  	q->data[q->rear] = item;
  }
  
  // 삭제 함수
  element delete_front(DequeType* q)
  {
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	q->front = (q->front + 1) % MAX_QUEUE_SIZE;
  	return q->data[q->front];
  }
  
  // 삭제 함수
  element get_front(DequeType* q)
  {
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	return q->data[(q->front + 1) % MAX_QUEUE_SIZE];
  }
  
  void add_front(DequeType* q, element val)
  {
  	if (is_full(q))
  		error("큐가 포화상태입니다");
  	q->data[q->front] = val;
  	q->front = (q->front - 1 + MAX_QUEUE_SIZE) % MAX_QUEUE_SIZE;
  }
  
  element delete_rear(DequeType* q)
  {
  	int prev = q->rear;
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	q->rear = (q->rear - 1 + MAX_QUEUE_SIZE) % MAX_QUEUE_SIZE;
  	return q->data[prev];
  }
  
  element get_rear(DequeType* q)
  {
  	if (is_empty(q))
  		error("큐가 공백상태입니다");
  	return q->data[q->rear];
  }
  int count_qu(DequeType* q) {
  	int count;
  	count = q->rear - q->front;
  	return count;
  }
  
  int main(void)
  {
  	DequeType queue;
  
  	init_deque(&queue);
  	char input[] = "radar";
  	
  	for (int i = 0; i < count_qu(&queue); i++) {
  		add_front(&queue, input[i]);
  		deque_print(&queue);
  	}
  
  		int r1, r2;
  	
  		r1 = (delete_front(&queue) == delete_rear(&queue));
  		r2 = (delete_front(&queue) == delete_rear(&queue));
  		if (r1 && r2) {
  			printf("회문이다.\n");
  		}
  	
  	
  	//for (int i = 0; i < 3; i++) {
  	//	delete_rear(&queue);
  	//	deque_print(&queue);
  	//}
  	return 0;
  }
  ```

  