# LangGraph 멀티 에이전트 협업 (self-refine)

> 2026-06-04 · ML&DL · LangGraph

역할이 다른 두 에이전트(Coder·Reviewer)가 협업하며 검수 통과까지 코드를 자기수정하는 구조. 같은 워크플로우를 **LangChain 선형 체인**으로 짠 뒤 **LangGraph 순환 그래프**로 재구성하며 차이를 비교함.

## 핵심

- **LangChain 선형 협업** — `developer → reviewer → refiner` 체인을 직렬 연결. 동작은 하지만 반복 불가(수정 1회 고정), 상태가 변수로 흩어짐, 분기를 if문으로 직접 처리해야 함.
- **LangGraph 순환** — 상태에 메시지 누적 + 반복 횟수를 묶고, 조건부 엣지로 `reviewer → coder` 순환을 구성. PASS 또는 반복 상한에서 종료.

## 상태 정의 — 누적 + 반복 카운터

```python
import operator
from typing import Annotated, List, TypedDict
from langchain_core.messages import BaseMessage

class AgentState(TypedDict):
    messages   : Annotated[List[BaseMessage], operator.add]  # 누적
    iterations : int                                          # 반복 횟수
```

`operator.add` 리듀서로 메시지를 덮어쓰지 않고 이어붙임.

## 조건부 엣지 — 순환 종료 규칙

```python
def is_continue(state: AgentState):
    last_msg   = state['messages'][-1].content
    iterations = state['iterations']

    if iterations >= 3:        # 안전장치: 무한 루프 = 비용 폭탄 방지
        return 'my_end'
    if "PASS" in last_msg:     # 리뷰 통과
        return 'my_end'
    return 'gogo'              # FAIL -> 재작성
```

`PASS` 부분 포함(`in`)으로 판정해 모델이 군더더기를 붙여도 통과됨.

## 그래프 구성

```python
from langgraph.graph import StateGraph, END

workflow = StateGraph(AgentState)
workflow.add_node('coder',    coder_node)
workflow.add_node('reviewer', reviewer_node)
workflow.set_entry_point('coder')
workflow.add_edge('coder', 'reviewer')

workflow.add_conditional_edges('reviewer', is_continue, {
    'my_end': END,        # 통과/상한 -> 종료
    'gogo'  : 'coder'     # 거절 -> 재작성(순환)
})
app = workflow.compile()
```

`'gogo' -> 'coder'` 매핑이 곧 자기수정 순환을 만든다. `coder_node`는 `placeholder`로 누적 messages를 받아 작성·수정을 한 노드에서 겸함.

## 비교

| 항목 | LangChain 선형 | LangGraph 멀티 에이전트 |
| --- | --- | --- |
| 흐름 | 작성→리뷰→1회 수정(고정) | 작성↔리뷰 순환(통과까지) |
| 상태 | 변수로 흩어짐 | messages 누적 + iterations |
| 반복 제어 | while 직접 구현 | 조건부 엣지 + 상한값 |
| 분기 | if문 혼합 | is_continue 분기 함수 분리 |

---

🔗 블로그 정리: [LangGraph 5 - 멀티 에이전트 협업](https://dev-lee.tistory.com/114)