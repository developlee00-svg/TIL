# LangGraph Tool + LLM (조건부 엣지로 자율 판단)

> 2026-06-01 · ML&DL · LangGraph

## 발전 포인트

고정 단방향 흐름에서 벗어나, LLM이 "직접 답할지 / 도구를 쓸지" 스스로 판단하는 구조로 발전. `add_conditional_edges`로 분기를 주고, `MessagesState`로 대화 메시지를 누적.

- **chatbot 노드** — Bedrock LLM 호출, 직접 답변 또는 도구 호출 판단
- **tools 노드** — `ToolNode`로 실제 기능(곱셈) 수행
- **조건부 엣지** — `tools_condition`이 응답을 보고 도구 노드 / END 자동 분기

## 구성

```python
@tool   # LLM이 이해할 수 있는 형식으로 자동 변환
def multiply(a: int, b: int) -> int:
    '''두 수를 곱한 후 반환'''
    return a * b

tools = [multiply]
llm_with_tools = llm.bind_tools(tools)   # "이런 도구를 쓸 수 있다" 알림

def chatbot_node(state: MessagesState):
    res = llm_with_tools.invoke(state['messages'])
    return {"messages": [res]}

workflow = StateGraph(MessagesState)
workflow.add_node('chatbot', chatbot_node)
workflow.add_node('tools', ToolNode(tools))
workflow.add_edge(START, 'chatbot')
workflow.add_conditional_edges('chatbot', tools_condition)  # 텍스트->END / 도구->tools
workflow.add_edge('tools', 'chatbot')   # 도구 결과 -> 다시 chatbot (순환)
app = workflow.compile()
```

- 직접 응답: 질의 → chatbot → 응답 → END
- 도구 사용: 질의 → chatbot → tools → 결과 → chatbot → 응답 → END

함수 docstring과 타입 힌트가 곧 LLM이 읽는 "도구 설명서". Claude는 도구를 적극 사용, OpenAI 계열은 직접 추론하는 경우가 있었음.

> ①편 단방향 엣지가 `tools → chatbot`으로 순환 가능한 엣지로 진화. 단, 새 질의마다 기억이 초기화됨.

---

🔗 블로그 정리: [LangGraph 2 - Tool + LLM](https://dev-lee.tistory.com/111)