# LangGraph 단기기억 (Checkpointer + thread_id)

> 2026-06-01 · ML&DL · LangGraph

## 발전 포인트

매 질의가 "새 채팅창"이라 이전 대화를 기억 못 하던 문제를, `Checkpointer`로 상태를 저장하고 `thread_id`로 사용자별 기억을 관리해 해결. 그래프 구조 자체는 ②편과 동일.

- **MemorySaver** — 상태 저장 단기기억 (현재 RAM 기반, 종료 시 삭제)
- **checkpointer 옵션** — `compile` 시 기억 공간 연결
- **thread_id** — 대화 세션 식별자, 같은 id면 기억 누적

## 변경 지점 (2곳뿐)

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()

# 1. 컴파일 옵션으로 단기기억 공간 제공
app = workflow.compile(checkpointer=memory)

# 2. 실행 시 config에 thread_id 전달 (없으면 기억이 안 이어짐)
config = {"configurable": {"thread_id": "user-1"}}
for evt in app.stream(prompt, stream_mode='values', config=config):
    msg = evt['messages'][-1]
    print("Agent", msg.content)
```

기억은 그래프 외부(체크포인터)에서 관리되므로 LLM·도구·노드 로직은 그대로. 1차 `100x2=200`, 2차 `100x3=300` 후 3차 "그간 질문 정리해줘"에 앞선 두 질문을 스스로 정리해냄. 실서비스에서는 인메모리 대신 물리적 DB(Redis, Postgres 등)로 교체.

> 질문을 거듭할수록 이전 대화가 상태에 누적되어 프롬프트로 함께 전달됨. 맥락 의존 질의가 가능해지는 이유.

---

🔗 블로그 정리: [LangGraph 3 - 단기기억](https://dev-lee.tistory.com/112)