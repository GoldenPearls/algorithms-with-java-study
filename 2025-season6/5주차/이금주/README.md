# 📘 Linked List Problems – LeetCode Solutions
## 🔹 [92] Reverse Linked List II
* **파일명:** `LC92-ReverseLinkedListII-Solution.java`
* **문제 링크:** [LeetCode 92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)

**풀이 아이디어:**
1. `dummy` 노드를 만들어 `left=1`인 경우에도 일관되게 처리한다.
2. `prev`를 `left-1` 위치까지 이동시킨다.
3. `start`를 `left` 위치 노드로 설정한다.
4. `right - left`번 반복하며 **매번 `start.next` 노드를 맨 앞으로 이동**시킨다.
5. `start`는 계속 같은 노드를 가리키며, 위치만 뒤로 밀린다.

### 왜 4개의 포인터가 필요한가?
연결 리스트를 부분적으로 뒤집으려면 다음 4개 구간을 파악해야 한다:
```
[뒤집지 않는 앞부분] → [뒤집을 부분] → [뒤집지 않는 뒷부분]
```
1. **`prev`** = `left` 직전 노드
2. **`start`** = `left` 위치의 노드 (뒤집을 부분의 시작)
3. **`end`** = `right` 위치의 노드 (뒤집을 부분의 끝)
4. **`next`** = `right` 다음 노드

**시각적 예시:**
```
입력: [1,2,3,4,5], left=2, right=4

dummy → 1 → 2 → 3 → 4 → 5
        ↑   ↑       ↑   ↑
      prev start   end next
```

뒤집은 후:
```
dummy → 1 → 4 → 3 → 2 → 5
```

---

### 왜 `right - left`인가?
**이동 횟수**를 계산하기 위해

**예시:**
```
[1, 2, 3, 4, 5], left=2, right=4

start = 위치 2 (노드 2)
end = 위치 4 (노드 4)
몇 번 이동해야 할까?
→ 2 → 3 (1번)
→ 3 → 4 (2번)
→ 총 2번 = right - left = 4 - 2 = 2
```

**핵심:** 위치의 차이 = 이동 횟수

---

### 매번 노드를 앞으로 옮기는 방식

**동작 과정:**

**초기 상태:**
```
dummy → 1 → 2 → 3 → 4 → 5
        ↑   ↑
      prev start
```

**i=0: 3을 맨 앞으로**
```java
temp = start.next;           // temp = 3
start.next = temp.next;      // 2 → 4
temp.next = prev.next;       // 3 → 2
prev.next = temp;            // 1 → 3
```
결과:
```
dummy → 1 → 3 → 2 → 4 → 5
        ↑       ↑
      prev    start(여전히 2)
```

**i=1: 4를 맨 앞으로**
```java
temp = start.next;           // temp = 4
start.next = temp.next;      // 2 → 5
temp.next = prev.next;       // 4 → 3
prev.next = temp;            // 1 → 4
```
결과:
```
dummy → 1 → 4 → 3 → 2 → 5 ✅
```

---

### 장점
1. **추가 함수 없음** (reverse 함수 불필요)
2. **O(1) 공간 복잡도**
3. **한 번의 패스**로 해결
4. **start를 고정**하면서 나머지만 움직임

---

### 코드
```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode dummy = new ListNode(-1, head); // TC: O(1), SC: O(1)
        ListNode prev = dummy; // TC: O(1), SC: O(1)
        
        // prev를 left-1 위치까지 이동
        for(int i = 0; i < left - 1; i++){  // TC: O(left-1) ≤ O(n)
            prev = prev.next;  // TC: O(1)
        }

        ListNode start = prev.next; // TC: O(1), SC: O(1)

        // 뒤집기: right-left번 반복
        for(int i = 0; i < right - left; i++){  // TC: O(right-left) ≤ O(n)
            ListNode temp = start.next; // 이동시킬 노드 // TC: O(1), SC: O(1) (재활용)
            start.next = temp.next; // start를 temp 다음으로 연결 // TC: O(1)
            temp.next = prev.next; // temp 맨 앞 삽입 // TC: O(1)
            prev.next = temp; // prev와 temp 연결 // TC: O(1)
            // 루프 내부 = O(1 + 1 + 1 + 1) = O(4)
        }

        return dummy.next; // TC: O(1), SC: O(1)
    }
}
```

---

### 합산하기
**시간 복잡도 (TC):**
* 초기 변수 설정: dummy O(1) + prev O(1) = O(2)
* 첫 번째 for 루프:
    - 조건 확인: O(1)
    - 루프 내부: prev 이동 O(1)
    - 반복 횟수: left-1번 (최악: n-1번)
    → 전체 = O(1 × (left-1)) ≤ O(n)
* start 설정: O(1)
* 두 번째 for 루프:
    - 조건 확인: O(1)
    - 루프 내부: temp 생성 O(1) + 링크 변경 O(3) = O(4)
    - 반복 횟수: right-left번 (최악: n-1번)
    → 전체 = O(4 × (right-left)) ≤ O(4n)
* return: O(1)
* 전체 시간복잡도: O(2) + O(left-1) + O(1) + O(4×(right-left)) + O(1)
                 ≤ O(2) + O(n) + O(1) + O(4n) + O(1)
                 = O(5n + 4)
                 → **최종: O(n)**

**공간 복잡도 (SC):**
* 추가 변수: dummy, prev, start, temp = 4개 포인터 → O(4)
* 첫 번째 for 루프: 추가 공간 없음 → O(0)
* 두 번째 for 루프: temp 재활용 → O(0)
* return: O(1)
* 전체 공간복잡도: O(4) + O(0) + O(0) + O(1) = O(5) → **최종: O(1)**

---

### 상세 동작 과정 (left=2, right=4)

**초기화:**
```
dummy(-1) → 1 → 2 → 3 → 4 → 5
  ↑
 prev
```

**prev 이동 (left-1 = 1번):**
```
dummy(-1) → 1 → 2 → 3 → 4 → 5
            ↑   ↑
          prev start
```

**i=0 (첫 번째 반복):**

1. `temp = start.next` (temp = 3)
```
dummy(-1) → 1 → 2 → 3 → 4 → 5
            ↑   ↑   ↑
          prev start temp
```

2. `start.next = temp.next` (2 → 4)
```
dummy(-1) → 1 → 2 ┐ 3 → 4 → 5
            ↑   ↑ └────→ ↑
          prev start    temp
```

3. `temp.next = prev.next` (3 → 2)
```
dummy(-1) → 1 → 2 ─────→ 4 → 5
            ↑   ↑ ↖
          prev start 3(temp)
```

4. `prev.next = temp` (1 → 3)
```
dummy(-1) → 1 → 3 → 2 → 4 → 5
            ↑       ↑
          prev    start
```

**i=1 (두 번째 반복):**

1. `temp = start.next` (temp = 4)
```
dummy(-1) → 1 → 3 → 2 → 4 → 5
            ↑       ↑   ↑
          prev    start temp
```

2. `start.next = temp.next` (2 → 5)
3. `temp.next = prev.next` (4 → 3)
4. `prev.next = temp` (1 → 4)

```
dummy(-1) → 1 → 4 → 3 → 2 → 5
            ↑           ↑
          prev        start
```

**최종 결과:**
```
1 → 4 → 3 → 2 → 5 
```

---

### 핵심 포인트

1. **`start`는 항상 같은 노드**
   - 노드 2를 계속 가리킴
   - 위치는 바뀌어도 노드 자체는 고정

2. **Dummy 노드의 중요성**
   - `left=1`인 경우 head가 바뀔 수 있음
   - dummy 노드로 일관된 처리 가능

3. **매번 앞으로 이동**
   - `start.next`를 떼어내서 `prev.next` 위치로 삽입
   - `prev`는 고정, `start`도 같은 노드 유지

4. **함수 정의 여부**
   - ❌ 정의되지 않은 `reverse()` 함수는 사용 불가
   - ✅ 직접 만들거나 직접 로직 작성
   - 이 방법은 추가 함수 없이 해결

---

# 📘 Stack Problems – LeetCode Solutions

## [20] Valid Parentheses

> “괄호는 닫히기 전까지 기다린다. 그리고 **Stack**은 그 기다림을 기억한다.”

## 문제 요약

주어진 문자열 `s`가 `()`, `{}`, `[]` 형태의 괄호로만 이루어져 있을 때,
**모든 괄호가 올바르게 열리고 닫히는지**를 판별하라.

🔗 **문제 링크:** [LeetCode 20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
📄 **파일명:** `LC20-ValidParentheses-Solution.java`

---

## 💡 핵심 아이디어

### Stack + LIFO 구조 활용

* 괄호는 반드시 **가장 마지막에 열린 것부터 닫혀야 함**
* 이 구조는 **LIFO(Last-In-First-Out)** 인 Stack이 완벽히 대응

```text
입력: "([])"
'(' → push → ['(']
'[' → push → ['(', '[']
']' → pop '[' (짝 맞음) → ['(']
')' → pop '(' (짝 맞음) → []
→ stack 비었으므로 true ✅
```

## 풀이 1 – HashMap 방식

```java
/*
최종 복잡도 정리

시간복잡도
- 초기 변수 설정: stack 생성, HashMap 생성,map.put() 3회 =  O(1+1+3) = O(5)
- toCharArray(): 문자열을 배열로 변환 O(n)
- for 루프:
    - 반복 횟수: n번
→ 전체 = O(1 × n) = O(n)
- return stack.isEmpty(): O(1)
- 전체 시간복잡도: O(5) + O(n) + O(n) + O(1) = O(2n + 6) → 최종: O(n)

공간복잡도
- 추가 변수: stack, map, map 내부 데이터 3쌍 , c = 3개 → O(6)
- toCharArray(): 문자열 길이만큼 배열 생성 → O(n)
- stack에 저장되는 데이터:
    - 최악의 경우: 모든 문자가 여는 괄호 "(((("
    - 최대 n개 저장 → O(n)
- return: O(1)
- 전체 공간복잡도: O(6) + O(n) + O(n) + O(1) = O(2n + 7) → 최종: O(n)
*/

class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>(); // TC: O(1), SC: O(1)

        HashMap<Character, Character> map = new HashMap<>(); // TC: O(1), SC: O(1)
        map.put(')', '('); // TC: O(1), SC: O(1)
        map.put(']', '['); // TC: O(1), SC: O(1)
        map.put('}', '{'); // TC: O(1), SC: O(1)

        for(char c: s.toCharArray()){ // TC: O(n) - n번 반복, SC: O(n) - toCharArray()
            // 닫는 괄호
            if(map.containsKey(c)){ // TC: O(1)
                //stack이 비어있거나 짝이 안맞으면 false
                if(stack.isEmpty() || stack.pop() != map.get(c)){ // TC: O(1) + O(1) + O(1)
                    return false; // TC: O(1)
                }
            }
            // 여는 괄호 
            else{
                stack.push(c); // TC: O(1), SC: O(1) - 최악 n개 쌓임
            }
        } 
        return stack.isEmpty();  // TC: O(1)
    }
}
```

### 작동 방식

| 단계 | 입력 문자                  | 동작              | 스택 상태     |
| -- | ---------------------- | --------------- | --------- |
| 1  | `(`                    | push            | `[`(`)`]` |
| 2  | `[`                    | push            | `[('[')]` |
| 3  | `]`                    | pop `'['`, 짝 맞음 | `['(']`   |
| 4  | `)`                    | pop `'('`, 짝 맞음 | `[]`      |
| ✅  | stack.isEmpty() → true | 결과: 유효한 괄호      |           |

### 시간 복잡도 (TC)

| 구문                            | 복잡도      |
| ----------------------------- | -------- |
| `toCharArray()`               | O(n)     |
| 반복문 (n회)                      | O(n)     |
| 내부 연산 (containsKey, pop, get) | O(1)     |
| **총합**                        | **O(n)** |

### 공간 복잡도 (SC)

| 항목                  | 복잡도      |
| ------------------- | -------- |
| HashMap (3쌍 저장)     | O(1)     |
| Stack (최대 n개 저장 가능) | O(n)     |
| **총합**              | **O(n)** |

### HashMap 방식의 장점

| 항목         | 설명                              |
| ---------- | ------------------------------- |
| **가독성**    | 괄호 매핑이 명확하게 드러남                 |
| **확장성**    | 괄호 추가 시 map만 수정                 |
| **유지보수성**  | if문보다 수정 범위 적음                  |
| **명확한 구분** | `containsKey()`로 여는/닫는 괄호 자동 구분 |

---

## 풀이 2 – 조건문 방식 (직관적)

```java

/*
최종 복잡도 정리

시간복잡도
- 초기 변수 설정: stack 생성 O(1)
- toCharArray(): 문자열을 배열로 변환 O(n)
- for 루프:
    - 조건 확인: O(1)
    - stack.push(), stack.isEmpty(), stack.pop()  : O(1 + 1 + 1) = O(3)
    - 짝 확인 (3개 조건): O(3) = O(1)
    - 총 루프 내부: O(1)
    - 반복 횟수: n번
→ 전체 = O(1 × n) = O(n)
- return stack.isEmpty(): O(1)
- 전체 시간복잡도: O(1) + O(n) + O(n) + O(1) = O(2n + 2) → 최종: O(n)

공간복잡도
- 추가 변수: stack, c, top = 3개 → O(3)
- toCharArray(): 문자열 길이만큼 배열 생성 → O(n)
- stack에 저장되는 데이터:
    - 최악의 경우: 모든 문자가 여는 괄호 "(((("
    - 최대 n개 저장 → O(n)
- return: O(1)
- 전체 공간복잡도: O(3) + O(n) + O(n) + O(1) = O(2n + 4) → 최종: O(n)
*/

class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();  // TC: O(1), SC: O(1)

        for(char c : s.toCharArray()){  // TC: O(n) - n번 반복, SC: O(n) - toCharArray()
            if(c =='(' || c=='[' || c=='{'){ // TC: O(1)
                stack.push(c); // TC: O(1), SC: O(1) - 최악 n개 쌓임
            }
            else{
                if(stack.isEmpty()){ // TC: O(1)
                    return false; // TC: O(1)
                }

                char top = stack.pop();  // TC: O(1), SC: O(1)

                if(c ==')' && top !='(') return false; // TC: O(1)
                if(c ==']' && top !='[') return false; // TC: O(1)
                if(c =='}' && top !='{') return false; // TC: O(1)
            }
        }

        // 스택이 비어있어야 true
        return stack.isEmpty(); // TC: O(1)
    }
}
```

### 두 방식 비교

| 항목        | HashMap 방식         | 조건문 방식         |
| --------- | ------------------ | -------------- |
| **코드 길이** | 짧고 깔끔 ✅            | 약간 길다          |
| **가독성**   | 높음 ✅               | 보통             |
| **확장성**   | 좋음 ✅ (괄호 추가 용이)    | 불편 (if문 추가 필요) |
| **성능**    | 약간 느림 (HashMap 조회) | 약간 빠름 ✅        |
| **추천 상황** | 유지보수/가독성 중시        | 단순 구현 시 빠르게 작성 |

---

## 최악 & 최선의 경우

| 상황     | 입력 예시         | 시간 복잡도 | 공간 복잡도 | 설명                      |
| ------ | ------------- | ------ | ------ | ----------------------- |
| **최악** | `"((((((((("` | O(n)   | O(n)   | 모두 여는 괄호 → stack에 n개 저장 |
| **최선** | `")"`         | O(n)   | O(n)   | 첫 문자에서 return false     |


## 학습 포인트

1. **Stack 구조를 활용한 괄호 매칭 로직**
2. **HashMap을 통한 코드 확장성 확보**
3. **시간복잡도 O(n), 공간복잡도 O(n)** 최적화
4. **엣지 케이스 처리의 중요성**
5. **코드 품질 vs 성능 트레이드오프** 이해

---

## 결론

| 방식             | 장점               | 단점       | 추천  |
| -------------- | ---------------- | -------- | --- |
| **HashMap 방식** | 깔끔, 확장성, 유지보수성 ✅ | 약간의 오버헤드 | 실무용 |
| **조건문 방식**     | 빠름, 직관적 ✅        | 가독성 낮음   | 코테용 |





