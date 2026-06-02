# LangGraph 상태 그래프 기초 (State · Node · Edge)

> 2026-06-01 · ML&DL · LangGraph

## 개념

LLM 워크플로우를 "상태를 공유하는 노드들의 그래프"로 표현하는 프레임워크. LangChain이 직선 체인이라면, LangGraph는 조건·순환까지 표현 가능한 그래프 구조. 첫 단계는 가장 단순한 단방향 그래프.

## 핵심 3요소

- **State** — 모든 노드가 공유하는 전역 메모리 (`TypedDict`로 형태 규정)
- **Node** — 작은 단위의 Task, 단순한 함수 하나로 구성
- **Edge** — 노드 간 실행 순서(방향성) 지정

## 최소 구성

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class CustomState(TypedDict):
    msg: str

def add_prefix(state: CustomState):
    return {'msg': "하이 " + state['msg']}

def add_suffix(state: CustomState):
    return {'msg': state['msg'] + " !!"}

workflow = StateGraph(CustomState)
workflow.add_node("T1", add_prefix)
workflow.add_node("T2", add_suffix)
workflow.set_entry_point("T1")   # 시작점
workflow.add_edge('T1', 'T2')    # T1 -> T2 (단방향)
workflow.add_edge("T2", END)     # 끝점
app = workflow.compile()         # 실행 가능한 형태로 완성

app.invoke({"msg": "랭그래프"})   # {'msg': '하이 랭그래프 !!'}
```

각 노드는 변경된 부분만 dict로 반환하면 랭그래프가 상태에 병합해 다음 노드로 넘김.

> 흐름이 코드에 고정되어 분기·반복이 없음. 다음 단계에서 LLM이 직접 경로를 판단하게 만듦.

---

🔗 블로그 정리: [LangGraph 1 - 상태 그래프 기초](https://dev-lee.tistory.com/110)