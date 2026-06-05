> 2026-06-05 · ML&DL · LangGraph/LangSmith

# LangGraph - LangSmith로 에이전트 관측하기

에이전트가 복잡해질수록 "어디서 느리고 얼마 쓰는지"가 안 보임. **LangSmith**로 흐름·지연·비용을 자동 추적. 그래프 코드는 안 건드리고 실행만 감쌈.

## 배운 것

- **설정은 .env 3줄** — `LANGCHAIN_API_KEY`, `LANGCHAIN_PROJECT`, `LANGCHAIN_TRACING_V2=true`. 키만 있으면 LangChain이 자동 감지해 추적 시작.
- **추적은 감싸기만** — `tracing_v2_enabled` with 블록 안의 모든 단계가 한 Run으로 기록. 그래프 로직과 분리됨.
- **관측은 부가 기능** — 키 없으면 추적 없이 그대로 동작하게 설계.
- **LangSmith가 잡는 것** — 데이터 흐름(Flow), 지연(전체/LLM/Tool latency), 메트릭(토큰·비용·성공률).

## 핵심 코드

추적 켜기 — 그래프 무수정, `invoke`만 감쌈

```python
from langchain_core.tracers.context import tracing_v2_enabled

if self.langsmith_client:
    with tracing_v2_enabled(project_name=langsmith_project):
        result = self.graph.invoke({"messages": messages})
else:
    result = self.graph.invoke({"messages": messages})
```

콘솔용 단계별 시간 직접 계량

```python
step_start = time.time()
response = llm_with_tools.invoke(messages)
self.metrics.log_step("llm_call", time.time() - step_start, {
    "has_tool_calls": bool(getattr(response, 'tool_calls', []))
})
```

## 정리

- 5편 `iterations` 상한 = 비용 **사전 차단**, LangSmith = 비용 **사후 관측**. 둘은 짝.
- 1편 상태그래프 → 6편 MCP → 7편 관측으로, "동작하고 / 외부도구 쓰고 / 관측 가능한" 에이전트 완성.

---

🔗 블로그 정리: [LangGraph 7 - LangSmith로 에이전트 관측하기](https://dev-lee.tistory.com/117)