# LangGraph RAG 에이전트 (FewShot + VectorDB)

> 2026-06-01 · ML&DL · LangGraph

## 발전 포인트

LLM이 모르는 사내·최신 데이터를 RAG로 끌어와 추론에 반영하는 식사 추천 에이전트. 도구의 성격이 "계산(행동)"에서 "지식 검색"으로 확장.

- **할루시네이션 방지** — 실제 데이터를 검색해 근거로 제공
- **내부 데이터 접근** — LLM이 모르는 private 데이터 보완
- **최신 정보 부재 해소** — 학습 시점 이후 정보를 외부에서 조회

## 구성 (모듈 분리)

```python
# rag_store.py — FAISS 인메모리 벡터 DB + 유사도 검색
tokenizer = BedrockEmbeddings(model_id="amazon.titan-embed-text-v2:0", ...)
vector_db = FAISS.from_texts(data, embedding=tokenizer)

def search_stores(query, k=2):
    docs = vector_db.similarity_search(query, k)
    return '\n'.join([doc.page_content for doc in docs])

# tools.py — 검색 함수를 @tool로 래핑
@tool
def rag_search(cate: str) -> str:
    '''가격/특징/메뉴/카테고리로 벡터 유사도 검색 -> 실제 식당 정보 제공'''
    return search_stores(cate) or '관련 식당 정보를 찾을 수 없습니다.'
```

## FewShot 프롬프트 + 노드 3분할

```python
final_prompt = ChatPromptTemplate.from_messages([
    ('system', '당신은 센스있는 식사 메뉴 추천 전문가입니다...'),  # 페르소나
    few_shot_prompt,    # 입력-출력 예시쌍 (톤 학습)
    ('human', '{query}')
])

# thinking(1차 추론) -> tool(RAG 검색) -> final_answer(근거 기반 최종 답변)
workflow.set_entry_point('thinking')
workflow.add_conditional_edges('thinking', custom_check_tool_node)  # 커스텀 분기
workflow.add_edge('tool', 'final_answer')
workflow.add_edge('final_answer', END)
```

- 베스트: thinking → END
- RAG 경유: thinking → (부족) → tool → RAG → final_answer → END

②편은 라이브러리 `tools_condition`을 썼지만, 여기선 `tool_calls` 유무를 직접 검사하는 분기 함수를 작성해 동작을 명시. FewShot은 규칙 설명보다 예시 한두 개가 톤을 더 정확히 잡아줌.

> 4단계 발전: 고정 흐름 → 자율 분기 → 기억 → 외부 지식 보강. 다음 확장은 외부 영속 벡터 DB, 다중 도구 선택, MCP 연계.

---

🔗 블로그 정리: [LangGraph 4 - RAG 에이전트](https://dev-lee.tistory.com/113)