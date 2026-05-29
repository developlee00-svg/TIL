# LLM 서비스 구축과 RAG

LangChain · Bedrock으로 LLM 채팅 서비스를 만들고, RAG로 외부 지식을 주입하는 과정을 정리한 학습 노트입니다.

---


## 핵심 요약

### 1. 서비스 아키텍처

`Streamlit(프론트) → FastAPI(백엔드) → LangChain(LLM 모듈) → Bedrock`으로 계층을 분리한다. 프론트는 `/chat` API만 호출하고 모델을 직접 알지 못해, 모델을 교체해도 화면 코드는 손대지 않는다.

### 2. LLM 모듈 구성

| 요소 | 설명 |
| --- | --- |
| ChatBedrockConverse | Bedrock converse API를 LangChain으로 래핑, 모델명만 교체하면 됨 |
| Few-Shot | 페르소나 + 예시로 일정 품질의 응답 유도 (파인튜닝 불필요) |
| 체인 | 프롬프트→LLM을 파이프 연산자로 연결, `chain.invoke()`로 호출 통일 |

### 3. RAG (검색 증강 생성)

LLM이 모르는 데이터(사내·최신 정보)를 검색해 프롬프트에 주입한다. 파인튜닝 대비 비용·보안에 유리하며, Vector DB만 갱신하면 됨.

| 단계 | 내용 |
| --- | --- |
| 적재 | TextLoader 로드 → RecursiveCharacterTextSplitter로 청크 분할(size 512 / overlap 100) |
| 저장 | BedrockEmbeddings로 벡터화 → `FAISS.from_documents` → `save_local` |
| 추론 | retriever(k=3) + format_docs → prompt → llm → StrOutputParser (LCEL) |

### 4. 핵심 인사이트

> 메뉴 추천이든 RAG든 결국 `chain.invoke()` 하나로 호출된다. 차이는 체인 앞단에 검색(retriever) 단계가 붙느냐일 뿐이며, RAG 체인은 프롬프트가 `(검색 문맥 + 질문)` 형태로 구성된다.
---


## 다루는 주요 키워드

`Streamlit` `FastAPI` `Bedrock` `LangChain` `ChatBedrockConverse` `Few-Shot` `LCEL` `RAG` `FAISS` `BedrockEmbeddings` `RecursiveCharacterTextSplitter` `Vector DB`

🔗 블로그 정리:
[LLM 2 - 서비스 구축 실습](https://dev-lee.tistory.com/109)