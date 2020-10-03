## Chapter 5. Queue

> `학습목표`
>
> - 큐의 개념과 추상자료형을 이해한다.
> - 덱의 개념과 구현 방법을 이해한다.
> - 큐를 이용하여 프로그램 할 수 있는 능력을 키운다.

#### 5.1) 큐 추상 데이터 타입

----------

큐는 먼저 들어온 데이터가 먼저 나가는 구조 = **'선입선출(FIFO)'**

- 큐의 추상 자료형

```c
// 객체 : 0개 이상의 원소를 가지는 선형 리스트
// 연산 : 
create(max_size) ::= 최대 크기가 max_size인 공백 큐를 생성
init(q) ::= 
    큐를 초기화한다.
is_empty(q) ::=
    if(size==0) return 1;
	else return 0;
is_full(q) ::=
    if(size==max_size) return 1;
	else return 0;
enqueue(q,data) ::=
    if(is_full(q)) return Error_Message;
	else q의 후단(rear)에 data를 추가한다.
dequeue(q,data) ::=
    if(is_empty(q)) return Error_Message;
	else q의 전단의 data를 제거하여 반환한다.
peek(q) ::=
    if(is_empty(q)) return Error_Message;
	else 전단의 data를 읽어서(제거하지 않고) 반환한다.
     
```

#### 5.2) 선형 큐

스택에서는 삽입, 삭제와 관련하여 변수가 top 1개만 존재하는 반면, 큐에서는 두 개의 변수 front, rear가 사용됨

- front와 rear의 초기값은 -1
- 데이터가 증가되면 rear를 하나 증가하고 그 위치에 데이터를 저장
- 삭제할 경우 front를 하나 증가하고 front가 가리키는 위치에 있는 데이터를 삭제(실제로 삭제되진 않음)
  - front는 항상 큐의 첫 번째 요소의 앞을, rear는 마지막 요소를 가리키며 이는 원형 큐나 덱에서도 같다.
  - 원형 큐/덱의 경우 front/rear의 초기값이 다르다.

`fprintf`함수와 `exit`함수 

> exit() 함수는 'stdlib.h' 헤더 파일을 include해서 사용한다.  c에서 exit이라는 함수는 현재의 c 프로그램 자체를 완전 종료하는 기능을 가진다. 주로, 에러가 났을 때 강제 종료시키기 위하여 사용된다.
>
> exit(0)은 '정상종료', exit(1)은 '에러메시지 종료'를 의미한다.

----------

> fprintf()함수는 file printf의 약자이다. 이때 stderr, stdin, stdout은 C/C++로 작성한 프로그램에서 지정하지 않아도 자동으로 오픈되는 스트림(파일포인터)이다. stderr로 출력되는 메시지는 버퍼링없이 즉시 출력되며 문제가 생겼을때 출력하는 용도로 사용된다.

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_QUEUE_SIZE 5

typedef int element;
typedef struct ArrayQueue
{
    int front,rear;
    element data[MAX_QUEUE_SIZE];
}QueueType;

// 오류 함수
void error(char* message)
{
    fprintf(stderr,"%s",message);
    exit(1);
}
//큐 초기화
void init(QueueType *Q)
{
    Q->rear=-1;
    Q->front=-1;
}
//큐 출력 함수
void print(QueueType *q)
{
    for(int i=0;i<MAX_QUEUE_SIZE;i++)
    {
        if(i<=q->front || i>q->rear)
            printf(" | ");
        else
            printf("%d | ");
    }
    printf("\n");
}
int is_full(QueueType* q)
{
    if(q->rear==MAX_QUEUE_SIZE-1)
        return 1;
    else
        return 0;
}
int is_empty(QueueType *q)
{
    if(q->rear==q->front)
        return 1;
    else
        return 0;
}
// 삽입 함수
void enqueue(QueueType *q, int item)
{
    if(is_full(q))
        error("큐가 포화상태입니다.");
    	return;
   	q->data[++(q->rear)]=item;
}
// 삭제 함수
int dequeue(QueueType *q)
{
    if(is_empty(q))
    {
        error("큐가 공백상태입니다.");
        return;
    }
    return q->data[++(q->front)];
}
// main 함수 실행 부분
int main(void)
{
    QueueType *q;
    init(q);
    
    enqueue(q,10); print(q);
    enqueue(q,20); print(q);
    enqueue(q,30); print(q);
    
    dequeue(q); print(q);
    dequeue(q); print(q);
    dequeue(q); print(q);
    return 0;
}
```

#### 5.3) 원형 큐

선형큐는 이해하기는 쉽지만 문제점이 존재한다. 삽입/삭제 작업을 반복시 front와 rear 값이 계속해서 증가하기 때문에 언젠가는 배열의 끝에 도달하게 되고, 배열의 앞 부분이 비어있더라도 사용하지 못한다. 즉, 주기적으로 모든 요소들을 주기적으로 왼쪽으로 이동시켜야 한다.  

- `해결방법` : 배열을 선형이 아닌 원형으로 생각하자

  > front와 rear값이 배열의 마지막 index 값인 (MAX_QUEUE_SIZE-1)에 도달시 다음에 증가되는 값은 0이 되도록 한다. 개념상으로만 원형으로 배열의 index값을  변화시켜준다.

- 원형 큐에서는 front 와 rear 의 개념이 약간 변경된다.

  - 초기값이 -1이 아닌 0이다.
  - front는 항상 첫 번째 요소의 하나 앞을, rear는 마지막 요소를 가리킨다. 

- 포화 상태와 공백 상태

  >  원형 큐에서는 하나의 자리를 반드시 비워두는데, 이는 포화상태와 공백상태를 구별하기 위해서이다. 
  >
  > - 공백 상태 : front와 rear 값이 같을때 
  > - 포화 상태 : front가 rear보다 하나 앞에 있을때 
  >
  > 만약 추가적인 변수 count 변수를 사용하면, 한 자리를 비워두지 않아도 된다.

---------------------

원형 큐의 삽입/삭제 알고리즘의 경우, front/rear 값을 원형으로 회전시켜야 하므로 나머지 연산자(%)를 이용

- 원형 큐의 삽입 연산

```c
void enqueue(QueueType Q, int item)
{
    if(is_full(Q))
        error("큐가 포화상태입니다");
   	Q->rear=(Q->rear+1) % MAX_QUEUE_SIZE;
    data[Q->rear]=item;
}
```

- 원형 큐의 삭제 연산

```c
int dequeue(QueueType Q)
{
    if(is_empty(Q))
        error("큐가 공백상태입니다");
    int item=data[((Q->front)+1)%MAX_QUEUE_SIZE];
    return item;
}
```

```c
#include <stdio.h>
#include <stdlib.h>
//===== 원형큐 코드 시작 =====
#define MAX_SIZE 5
typedef struct ArrayQueue
{
    int front,rear;
    int data[MAX_SIZE];
}QueueType;

//오류 함수
void error(char * message)
{
    fprintf(stderr,"%s\n",message);
    exit(1);
}
//큐 초기화 함수
void init(QueueType *q)
{
    q->rear=0;
    q->front=0;
}
//공백 상태 검출 함수
int is_empty(QueueType *q)
{
    if(q->rear==q->front)
        return 1;
    else
        return 0;
}
//포화 상태 검출 함수
int is_full(QueueType *q)
{
    if((q->rear+1)%MAX_SIZE==q->front)
        return 1;
    else
        return 0;
}
//원형 큐 출력 함수
void print_queue(Queue q)
{
    printf("QUEUE(front=%d rear=%d)",q->front,q->rear);
    if(!is_empty(q))
        int i=q->front;
    	do{
            i=(i+1)%MAX_SIZE;
            printf("%d | ",q->data[i]);
            if(i==q->rear)
                break;
        }while(i!=q->front)
}
//삽입 함수
void enqueue(QueueType *q,int item)
{
    if(is_full(q))
    {
        error("큐가 포화상태입니다.");
        return;
    }
    q->rear = (q->rear+1)%MAX_SIZE;
    q->data[(q->rear)]=item
}
//삭제 함수
int dequeue(QueueType *q)
{
    if(is_empty(q))
    {
        error("큐가 공백상태입니다.");
        return;
    }
    q->front=(q->front+1)%MAX_SIZE;
    return q->data[q->front];
}
//===== 원형 큐 코드 끝 =====

int main(void)
{
    QueueType* q;
    init(q);
    int item;
    printf("----데이터 추가 단계----\n");
    while(!is_full(q))
    {
        printf("정수를 입력하시오 :");
        scanf("%d",&item);
        enqueue(q,item);
        print_queue(q);
    }
    printf("----데이터 삭제 단계----\n");
    while(!is_empty(q))
    {
        item=dequeue(q);
        printf("꺼내진 정수: %d\n",item);
        print_queue(q);
    }
    printf("큐는 공백 상태입니다.\n");
    return 0;
}
```

#### 5.4) 큐의 응용 : 버퍼

큐는 서로 다른 속도로 실행되는 두 프로세스 간의 상호작용을 조화시키는 버퍼 역할을 담당한다. 

CPU와 프린터 사이의 프린터 버퍼, 또는 CPU와 키보드 사이의 키보드 버퍼 등이 해당한다. 대개 데이터를 생산하는 생산자 프로세스와 데이터를 소비하는 소비자 프로세스가 있으며, 이 사이에 큐로 구성되는 버퍼가 존재한다.

> 큐 응용 프로그램
>
> - 큐에 일정한 비율(20%)로 난수를 생성하여 큐에 입력하고, 일정한 비율(10%)로 큐에서 정수를 꺼내는 프로그램을 작성하자.(생산자의 속도  > 소비자의 속도)
>
>   ```c
>   #include <stdio.h>
>   #include <stdlib.h>
>   #include <time.h>
>   //=====원형 큐 코드 시작=====
>   //....
>   //=====원형 큐 코드 종료=====
>   int main(void)
>   {
>    	QueueType *q;
>       int item;
>       init(q);
>       srand((unsigned)time(NULL)); 
>       // time함수를 seed값으로 설정하여 rand()함수가 계속해서 난수를 생성할 수 있도록 한다.
>       
>       for(int i=0;i<100;i++)
>       {
>           if(rand()%5=0)
>               enqueue(q,rand()%100); 
>           // rand()함수는 %연산자를 이용해 난수를 생성하는 범위를 정하는게 가능하다.
>           //위의 경우, rand()함수는 0-99까지의 임의의 수를 반환한다.
>           print(q);
>           if(rand()%10==0)
>               int data= dequeue(q);
>           print(q);
>       }
>       return 0;
>   }
>   ```



#### 5.5) 덱

덱(dequeue)은 double-ended queue의 줄임말로서 큐의 전단(front)과 후단(rear)에서 모두 삽입/삭제가 가능한 큐를 의미한다.

- 덱의 ADT

```c
//객체 : n개의 element형(int형)의 원소들의 순서 있는 모임
//연산 : 
	create() ::= 덱을 생성한다.
    init(dq) ::= 덱을 초기화한다.
    is_empty(dq) ::= 덱이 공백 상태인지를 검사한다.
    is_full(dq) ::= 덱이 포화 상태인지르 검사한다.
    add_front(dq,item) ::= 덱의 앞에 원소를 추가한다.
    add_rear(dq,item) ::= 덱의 뒤에 요소를 추가한다.
    delete_front(dq) ::= 덱의 앞에 있는 요소를 반환한 후 삭제한다.
    delete_rear(dq) ::= 덱의 뒤에 있는 요소를 반환한 후 삭제한다.
    get_front(dq) ::= 덱의 앞에서 삭제하지 않고 앞에 있는 요소를 반환한다.
    get_rear(dq) ::= 덱의 뒤에서 삭제하지 않고 뒤에 있는 요소를 반환한다.
```

--------

- 덱은 원형 큐의 확장판이다.(연결리스트로 구현하지 않는 이상)
  - 즉, 덱을 출력할 때도 원형 큐처럼 `do while` 문을 사용한다.
  - front와 rear값도 마찬가지로 0부터 시작. + front와 rear의 의미 또한 동일하다.
    - front는 가장 앞에 있는 값의 앞 index를 가리키며, rear는 가장 뒤의 값의 index를 가리킨다.
  - 포화상태의 기준도 배열이 1칸만 남았을때이다.
- 덱은 스택과 큐의 연산들을 모두 지닌다.
  - add_front와 delete_front 연산은 스택에서의 push,pop연산과 동일하다.
  - add_rear 연산과 delete_front 연산은 각각 큐의 enqueue, dequeue 연산과 동일하다.

- 새롭게 추가되는 연산은 add_front, delete_rear, get_rear 가 있다. 

  - get_rear의 경우, 공백상태가 아닐때 rear가 가리키는 항목을 반환하면 된다

  - add_front나 delete_rear의 경우 원형 큐에서와 다르게 반대 방향의 회전이 필요하다.

    - ```
      front <- (front-1+MAX_SIZE) % MAX_SIZE
      rear <- (rear-1+MAX_SIZE) % MAX_SIZE
      ```

- 원형 덱 프로그램(원형 큐의 코드를 그대로 사용)

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_SIZE 5
typedef struct ArrayQueue
{
    int front,rear;
    int data[MAX_SIZE];
}DequeType;

//오류 함수
void error(char* message)
{
    fprintf(stderr,"%s\n",message);
    exit(1);
}
// 초기화 함수
void init(DequeType* dq)
{
    q->front=q->rear=0;
}
//공백 상태 검출 함수
int is_empty(DequeType* dq)
{
    if(dq->front==dq->rear)
        return 1;
    else
        return 0;
}
//포화 상태 검출 함수
int is_full(DequeType* dq)
{
    if(dq->front==(dq->rear+1)%MAX_SIZE)
        return 1;
    else
        return 0;
}
//원형 큐 출력 함수
void dq_print(DequeType *dq)
{
    printf("DEQUE(front=%d rear=%d)",dq->front,dq->rear)
    if(!is_empty(dq))
    {
        int i=dq->front;
        do {
            i=(i+1)%MAX_SIZE;
            printf("%d | ",dq->data[i]);
            if(i==dq->rear)
                break;
        }while(i!=dq->front);
    }
    printf("\n");
}
//후단 삽입 함수
void add_rear(DequeType* dq,int item)
{
    if(is_full(dq))
        error("큐가 포화상태입니다.");
    dq->rear=(dq->rear+1)%MAX_SIZE;
    dq->data[dq->rear]=item;
}
//전단 삽입 함수
void add_front(DequeType* dq, int item)
{
    if(is_full(dq))
        error("큐가 포화상태입니다.");
    dq->front=(dq->front-1+MAX_SIZE)%MAX_SIZE;
    dq->data[dq->front]=item;
}
//전단 삭제 함수
int delete_front(DequeType* dq)
{
    if(is_empty(dq))
        error("큐가 공백상태입니다.");
   	dq->front=(dq->front+1)%MAX_SIZE;
    return dq->data[dq->front];
}
//후단 삭제 함수
int delete_rear(DequeType* dq)
{
    if(is_empty(dq))
    	error("큐가 공백상태입니다.");
   	dq->rear=(dq->rear-1+MAX_SIZE)%MAX_SIZE;
    return dq->data[dq->rear];
}
//전단 항목 반환 함수
int get_front(DequeType * dq)
{
    if(is_empty(dq))
        error("큐가 공백상태입니다.");
    return dq->data[(dq->front+1)%MAX_SIZE];
}
//후단 항목 반환 함수
int get_rear(DequeType* dq)
{
    if(is_empty(dq))
        error("큐가 공백상태입니다.");
    return dq->data[dq->rear];
}
//main 함수
int main(void)
{
    DequeType dq;
    init(&dq);
    for(int i=0;i<3;i++)
    {
        add_front(&dq,i);
        dq_print(&dq);
    }
    for(int i=0;i<3;i++)
    {
        delete_rear(&dq);
        dq_print(&dq);
    }
    return 0;
}
```

#### 5.6) 큐의 응용 : simulation

> EX) 은행 서비스 시뮬레이션 프로그램
>
> > - 서비스하는 행원은 한  사람이며, 고객의 대기행렬은 큐로 시뮬레이션 된다.
> > - 주어진 시간동안 고객은 랜덤한 간격으로 큐에 들어오고, 고객들의 서비스 시간 또한 랜덤하게 결정
> > - 정해진 시간동안의 시뮬레이션이 끝나면 고객들의 평균 대기 시간을 계산하여 출력한다. 
>
> ------
>
> > 반복문내의 실행들
> >
> > 1. 현재 시각을 나타내는 clock 변수를 하나 증가한다
> > 2. [0,10] 사이의 난수를 생성해 3보다 작으면 새로운 고객이 들어왔다고 판단 / 새로운 고객이 들어오면 구조체를 생성하고 고객의 아이디, 도착시간, 서비스 시간 등의 정보를 복사한다. / 이 구조체를 enqueue()함수를 호출해 큐에 추가하고, 전역변수인 service_time에 현재 처리 중인 고객의 서비스 시간을 저장해둔다.
> > 3.  service_time이 0인지 아닌지 살펴본다. 만약 service_time이 0이 아니라면 어떤 고객이 지금 서비스를 받고 있는 중임을 의미하며, service_time값이 0이면 현재 서비스 받는 고객이 없다는 것을 의미한다. 따라서 고객 구조체를 하나 꺼내서 서비스를 시작한다. 
> > 4. 서비스를 시작한다는 것은 전역변수 service_time에 고객의 서비스 시간을 저장하는 것이다.
> > 5. 60분이 지나면 고객들이 기다린 시간을 전부 합해서 화면에 출력한다.
>
> ```c
> #include <stdio.h>
> #include <stdlib.h>
> 
> // =================== 원형 큐 코드 시작 ===================
> typedef struct personal_Data
> {
>     int id;
>     int arrival_time;
>     int service_time;
> }element;
> //...
> // =================== 원형 큐 코드 종료 ===================
> 
> int main(void)
> {
>     int minutes=60;
>     int total_wait=0;
>     int total_customers=0;
>     int service_time=0;
>     int service_customer;
>     QueueType q;
>     init queue(&q);
>     
>     srand((unsigned)time(NULL));
>     for(int clock=0;clock<minutes;clock++)
>     {
>         printf("현재시각=%d\n",clock);
>         if(rand()%10<3)
>         {
>             element customer;
>             customer.id=total_customers++;
>             customer.arrival_time=clock;
>             customer.service_time=(rand()%3)+1;
>             enqueue(&q,customer) // 구조체를 원소로 갖는 원형 큐
>             printf("고객 %d가 %d분에 들어옵니다. 업무처리시간 = %d분\n",
>                   customer.id,customer.arrival_time,customer.service_time);
>         }
>         if(service_time>0)
>         {
>             
>         }
>         else
>         {
>             if(!is_empty(&q)) //이 조건문은 반드시 있어야 함(초기 값을 위해)
>             {
>                 element customer=dequeue(&q);
>                 service_customer=customer.id;
>                 service_time=customer.service_time;
>                 printf("고객 %d이 %d분에 업무를 시작합니다. 대기 시간은 %d분이었습니다.\n",
>                       customer.id,clock,clock-customer.arrival_time);
>                 total_wait+=clock-customer.arrival_time; // 총 대기시간을 구함.
>             }
>         }
>         
>     }
>     printf("전체 대기 시간=%d분 \n",total_wait);
>     return 0;
> }
> ```
>
> 처음으로 온 고객의 경우 기다리는 사람이 없으므로 대기시간=0, 바로 업무를 시작한다.