> 2026-06-05 · ML&DL · LangGraph/MCP

# LangGraph - MCP 도구 연계

도구를 코드에 박는 대신 **별도 프로세스(MCP 서버)** 로 빼고, JSON-RPC로 통신해 가져다 쓰는 구조. 어댑터로 MCP 도구를 LangChain `StructuredTool`로 변환하면 기존 그래프(2~5편)에 그대로 꽂힘.

## 배운 것

- **FastMCP** — `@mcp.tool()` 데코레이터로 함수를 도구로 등록. docstring·타입힌트가 곧 LLM이 읽는 설명서. (2편 `@tool`과 동일, 위치만 서버 측)
- **STDIO 함정** — stdout은 JSON-RPC 전용 채널. 로그를 stdout에 찍으면 프로토콜이 깨짐 → 로깅은 반드시 `stderr`로.
- **결과는 content 블록** — `call_tool` 반환값은 문자열이 아니라 content 리스트. `content.text`로 꺼내야 함.
- **어댑터 함정 2개**
  - `async with` 못 씀 — 세션을 여러 메서드에서 써야 해서 `__aenter__`/`__aexit__` 수동 호출.
  - 클로저 늦은 바인딩 — 루프에서 도구 함수를 그냥 만들면 전부 마지막 이름 참조. 한 겹 감싸 독립시킴.
- **스키마 → pydantic** — MCP의 JSON Schema를 `create_model`로 동적 변환. number→float 등 타입 매핑.

## 핵심 코드

MCP 도구 → LangChain 변환 (클로저로 도구별 독립 함수 생성)

```python
def create_tool_func(name: str):          # name을 인자로 고정
    async def async_tool_func(**kwargs) -> str:
        return await self.call_tool(name, kwargs)
    return async_tool_func

tool_func = create_tool_func(tool_name)
```

에이전트 결합 — 그래프는 5편 그대로, 도구 출처만 MCP로 교체

```python
self.mcp_adapter = MCPToolAdapter('server.py')
await self.mcp_adapter.initialize()
self.tools = self.mcp_adapter.create_langchain_tools()
llm_with_tools = self.llm.bind_tools(self.tools)
```

## 트러블슈팅

- import명 불일치(`MCPClient` ↔ `MCPToolAdapter`) → ImportError
- `await` 누락 → 코루틴 객체만 생기고 연결 안 됨
- MCP 로드 전 `bind_tools([])` → LLM이 도구 못 봄 (초기화 순서 고정으로 해결)

---

🔗 블로그 정리: [LangGraph 6 - MCP 도구 연계](https://dev-lee.tistory.com/116)