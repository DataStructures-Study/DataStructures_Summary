# Chapter 4. Stack

> `학습목표`

- 스택의 **개념**과 추상 자료형을 이해한다.
- 스택의 **동작 원리**를 이해한다.
- 스택 응용 프로그램을 제작할 수 있는 능력을 배양한다.

----------------------------



## 4.1 스택이란?

### 추상 자료형 스택

- 스택을 ADT으로 정의

```c
// 객체 : 0개 이상의 원소를 가지는 유한 선형 리스트

// 연산 :
create(size) ::= 최대 크기가 size인 공백 스택을 생성
is_full(s) ::=
    if (스택의 원소 수 == size) return TRUE;
	else return FALSE;
is_empty(s) ::=
    if (스택의 원소 수 == 0) return TRUE;
	else return FALSE;
push(s, item) ::=
    if (is_full(s)) return ERROR_STACKFULL;
	else 스택의 맨 위에 item을 추가한다.
pop(s) ::=
    if (is_empty(s)) return ERROR_STACKEMPTY;
	else 스택의 맨 위의 원소를 제거해서 반환한다.
peek(s) ::=
    if (is_empty(s)) return ERROR_STACKEMPTY;
	else 스택의 맨 위의 원소를 제거하지 않고 반환한다.
```



## 4.2 스택의 구현

- 스택의 `is_empty` 연산

```c
is_empty(S):
	if top == -1
        then return TRUE
        else return FALSE
```

- 스택의 `is_full` 연산

```c
is_full(S):
	if top >= (MAX_STACK_SIZE -1)
		then return TRUE
		else return FALSE
```

- 스택의 `push` 연산

```c
push(S, x):
	if (is_full(S))
		then error "overflow"
	else {
        top = top + 1;
        stack[top] = x;
	}
```

- 스택의 `pop` 연산

```c
pop(S):
	if (is_empty(S))
		then error "underflow"
		else{
            e = stack[top];
            top = top - 1;
            return e;
		}
```



stack을 전역변수에 구현해 놓고 넣었다 뺐다 할 수 있다. 그러나 그렇게 하면, stack 배열과 top이 전역 변수로 선언되기 때문에 하나의 프로그램에서 여러 개의 스택을 동시에 사용하기가 어렵다. C에서 이 문제를 해결하기 위해서 

- top과 stack 배열을 하나의 구조체로 결합시킨다. 
- 이 구조체의 포인터를 함수로 전달한다. 
- 즉, `StackType`이라는 새로운 구조체 타입을 만들고 여기에 `stack 배열`과 `top`을 넣는다. 
- 그리고 이 구조체에 대한 `포인터`를 각 함수의 매개변수로 전달하는 것이다.

모든 함수에서는 만약 전달된 구조체 포인터가 s이면, 

- 기존의 `top`이라고 사용하던 것을 `s->top`으로 변경한다.
- `stack`배열도 `s->stack`으로 변경하여 사용한다.

```c
# include <stdio.h>
# include <stdlib.h>

// 차후에 스택이 필요하면 여기만 복사하여 붙인다.
// ======= 스택 코드의 시작 ========

#define MAX_STACK_SIZE 100

typedef int element;
typedef struct{
	element data[MAX_STACK_SIZE];
	int top;
}StackType;

// 스택 초기화 함수
void init_stack(StackType *s){
	s->top = -1;
} 

// 공백 상태 검출 함수
int is_empty(StackType *s){
	return (s->top==-1);
} 

// 포화 상태 검출 함수
int is_full(StackType *s){
	return (s->top==(MAX_STACK_SIZE));
} 

// 삽입 함수
void push(StackType *s, element item){
	if(is_full(s)){
		fprintf(stderr, "스택 포화 error!\n");
		return;	
	}
	else{
		s->data[++(s->top)] = item;
	}
}

// 삭제 함수 
element pop(StackType *s){
	if (is_empty(s)){
		fprintf(stderr, "스택 공백 error!\n");
		exit(1);
	}
	else return s->data[(s->top)--];
}

// peek 함수
element peek(StackType *s){
	if (is_empty(s)){
		fprintf(stderr, "스택 공백 error!\n");
		exit(1);
	}
	else return s->data[s->top];
} 
// ======== 스택 코드 끝 ==========

int main(){
	StackType s;
	
	init_stack(&s);
	push(&s, 1);
	push(&s, 2);
	push(&s, 3);
	printf("%d\n", pop(&s));
	printf("%d\n", pop(&s));
	printf("%d\n", pop(&s));
} 

```



## 4.3 동적 배열 스택

```c
# include <stdio.h>
# include <stdlib.h>

// 차후에 스택이 필요하면 여기만 복사하여 붙인다.
// ======= 스택 코드의 시작 ========

#define MAX_STACK_SIZE 100

typedef int element;
typedef struct{
	element *data;
	int capacity; // data 크기
	int top; 
}StackType;

// 스택 초기화 함수
void init_stack(StackType *s){
	s->top = -1;
	s->capacity = 1;
	s->data = (element *)malloc(s->capacity*sizeof(element));
} 

// 공백 상태 검출 함수
int is_empty(StackType *s){
	return (s->top==-1);
} 

// 포화 상태 검출 함수
int is_full(StackType *s){
	return (s->top==(MAX_STACK_SIZE));
} 

// 삽입 함수
void push(StackType *s, element item){
	if(is_full(s)){
//		fprintf(stderr, "스택 포화 error!\n");
//		return;	
		s -> capacity *= 2;
		s -> data = (element*)realloc(s->data, s->capacity*sizeof(element));
	}
	s->data[++(s->top)] = item;
//	else{
//		s->data[++(s->top)] = item;
//	}
}

// 삭제 함수 
element pop(StackType *s){
	if (is_empty(s)){
		fprintf(stderr, "스택 공백 error!\n");
		exit(1);
	}
	else return s->data[(s->top)--];
}

// peek 함수
element peek(StackType *s){
	if (is_empty(s)){
		fprintf(stderr, "스택 공백 error!\n");
		exit(1);
	}
	else return s->data[s->top];
} 
// ======== 스택 코드 끝 ==========

int main(){
	StackType s;
	
	init_stack(&s);
	push(&s, 1);
	push(&s, 2);
	push(&s, 3);
	printf("%d\n", pop(&s));
	printf("%d\n", pop(&s));
	printf("%d\n", pop(&s));
} 
```



## 4.4 스택의 응용 :  괄호 검사 문제

- 슈도코드

```c
check_matching(expr):

while (입력 expr의 끝이 아니면)
ch <- expr의 다음글자
switch(ch)
    case '(': case '[': case '{':
		ch를 스택에 삽입;
         break;
	case ')': case ']': case '}':
		if (스택이 비어있으면)
            then 오류
         else 스택에서 open_ch를 꺼낸다.
             if (ch와 open_ch가 같은 짝이 아니면)
                 then 오류 보고
         break;
if (스택이 비어 있지 않으면)
    then 오류

```



- `Parenthesis check.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_STACK_SIZE 100

# include <stdio.h>
# include <stdlib.h>

// 차후에 스택이 필요하면 여기만 복사하여 붙인다.
// ======= 스택 코드의 시작 ========
...
// ======== 스택 코드 끝 ==========

int check_matching(const char *in){
	StackType s;
	char ch, open_ch;
	int i, n = strlen(in); // n =  문자열의 길이 
	init_stack(&s);
	
	for (i=0; i<n; i++){
		ch = in[i];
		switch(ch){
			case '(': case '[': case '{':
				push(&s, ch);
				break;
			case ')': case ']': case '}':
				if (is_empty(&s)){
					return 0;
				}
				else{
					open_ch = pop(&s);
					if ((open_ch == '(' && ch!=')') || (open_ch == '[' && ch!=']') || (open_ch == '{' && ch!='}')){
						return 0;
					}
				}
				break;
		}
	}
	if (!is_empty(&s)) return 0;
	return 1;
}
```



## 4.5 스택의 응용 : 후위 표기 수식의 계산

- 후위 표기 수식 계산 알고리즘

```c
calc_posfix:
	스택 s를 생성하고 최화한다.
     for item in 후위표기식 do
         if (item이 피연산자이면)
             push(s, item)
         else if (item이 연산자 op이념)
             second = pop(s)
             first = pop(s)
             resutl = first op second // op 는 +_*/ 중에 하나
             push(s, result)
     final_result = pop(s);
```



- eval_postfix.c

```c
// 프로그램 4.3에서 스택 코드 추가
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 차후에 스택이 필요하면 여기만 복사하여 붙인다.
// ======= 스택 코드의 시작 ========
...
// ======== 스택 코드 끝 ==========

int eval(char exp[]){
	int op1, op2, value, i=0;
	int len;
	char ch;
	StackType s;
	
	len =  strlen(exp);
	
	init_stack(&s);
	for (i=0; i<len; i++){
		ch = exp[i];
		if (ch!='+' && ch!='-' && ch!='*' && ch!='/'){ // 피연산자 이면, 
			value = ch - '0';
			push(&s, value);
		}
		else{
			op2 = pop(&s);
			op1 = pop(&s);
			switch(ch){
				case '+': push(&s, op1+op2); break;
				case '-': push(&s, op1-op2); break;
				case '*': push(&s, op1*op2); break;
				case '/': push(&s, op1 / op2); break; 
			}
		}
	}
	return pop(&s);
}

int main(){
	int result;

	printf("후위 표기식은 82/3-32*+\n");
	result = eval("82/3-32*+");
	printf("결과값은 %d\n", result);
	return 0;
} 
```



- infix를 postfix로 변환
    - infix와 postfix의 공통점 : 피연산자의 순서가 동일하다.
    - 다만, 연산자들의 순서가 달라진다.
    - 연산자들의 순서는 **우선순위**에 따라 결정된다.
    - infix를 postfix로 변환하기 위하여 입력 수식을 왼쪽에서 오른쪽으로 스캔한다.
        - 만약, 피연산자를 만나게 되면 바로 후위 표기 수식에 출력한다.
        - 연산자를 만나게 되면 어딘가에 잠시 저장한다. 적절한 위치를 찾을 때까지 출력을 보류하여야 한다.
            - **적절한 위치**를 찾는 방법이 스택 안에 들어있는 놈과 우선순위 비교
        - 스택에 존재하는 연산자가 현재 처리중인 연산자보다 우선순위가 높으면, 일단 스택에 있는 연산자를 먼저 pop한 다음에 (높으신 분이 왜 이리 누추한 곳에 계셔요) 처리 중인 연산자를 스택에 넣어야 한다.
            - 우선순위가 같다면?? -> 이것도 문제. 우선순위가 같은 경우에도 pop하여 stack 안에 있는 것을 출력해야 한다.
        - 괄호는 어떻게 처리해야 하는가? 
            - 왼쪽괄호는 무조건 스택에 삽입한다.
            - 왼쪽 괄호가 일단 스택에 삽입되면, 우리는 왼쪽괄호를 가장 우선순위가 낮은 찌끄레기 취급을 한다. (즉, 다음에 만나는 어떤 연산자도 스택에 삽입된다.)
            - 오른쪽 괄호를 만나게 되면, 왼쪽 괄호가 삭제될 때까지 왼쪽 괄호 위에 쌓여있는 모든 연산자들을 출력한다.

- infix to postfix

```c
infix_to_postfix(exp):

스택 s를 생성하고 초기화
while (exp에 처리할 문자가 남아 있으면)
    ch <- 다음에 처리할 문자
    switch (ch)
    case 연산자:
        while (peek(s)의 우선순위 >= ch의 우선순위) do
        	e <- pop(s)
            e를 출력
         push(s, ch);
		break;
	case 왼쪽 괄호:
		push(s, ch);
		break;
	case 오른쪽 괄호:
		e <-pop(s)
         while(e!=왼쪽괄호) do
             e를 출력
             e <- pop(s)
         break;
	case 피연산자:
		ch를 출력
     	 break;
while(not is_empty(s)) do
    e <- pop(s)
    
       
```



## 4.6 스택의 응용 : 미로 문제