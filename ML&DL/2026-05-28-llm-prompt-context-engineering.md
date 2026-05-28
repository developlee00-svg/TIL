# LLM 프롬프트 · 컨텍스트 엔지니어링

## 핵심

AWS Bedrock으로 Claude·GPT·Gemma를 단일 SDK에서 호출하고, 모델별 프롬프트 전략 차이를 실습. LangChain으로 프롬프트 템플릿과 체인을 추상화하는 흐름까지 다룸.

## 프롬프트 공통 구성 4요소

| 요소 | 설명 |
|------|------|
| **Role** | 페르소나·역할 부여 |
| **Context** | 배경 정보 제공 |
| **Task** | 구체적 요청 |
| **Format** | 출력 형식·제약 조건 |

## 모델별 특화 전략

* **Claude** — XML 태그로 섹션 구획, 논리·긴 글에 강함
* **GPT** — Few-Shot 샘플 제공, 단계별 결과 유도
* **Gemini** — 구글 검색 연동, 트렌드 반영 지시에 강함

```python
# Claude 특화
prompt = """
<role>20년차 카피라이터</role>
<context>24시간 얼음 유지 텀블러, 타겟: 20대 직장인</context>
<instruction>SNS 홍보 초안 작성</instruction>
<constraints>400자 이내, 위트 있게</constraints>
"""

# GPT 특화 — few-shot
messages = [
    {"role": "system",    "content": "당신은 카피라이터입니다."},
    {"role": "user",      "content": "텀블러 홍보 문구 써줘"},
    {"role": "assistant", "content": "아침에 담은 얼음이 퇴근까지..."},  # 샘플
    {"role": "user",      "content": "실제 요청"}
]
```

## 제로샷 · 원샷 · 퓨샷

* **Zero-shot** — 예시 없이 지시만으로 처리 (간단한 요약, 상식 질문)
* **Few-shot** — 예시 2~5개 제공, 파인튜닝 없이 고품질 분류 가능

```python
# 리뷰 감정 분류 — few-shot (Claude)
prompt = {
    "anthropic_version": "bedrock-2023-05-31",
    "max_tokens": 100,
    "system": "리뷰를 '긍정','부정','가격민감' 중 하나로 분류하세요.",
    "messages": few_shot_messages  # 예시 포함
}
```

## Bedrock 벤더별 응답 파싱

```python
# Claude
res['content'][0]['text']

# GPT
res['choices'][0]['message']['content']

# Gemma (converse API)
res['output']['message']['content'][0]['text']
```

> 동일 SDK, 다른 파싱 구조 → 인터페이스 클래스로 추상화 권장. sklearn의 `fit()` 패턴처럼.

## LangChain 프롬프트 템플릿

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_aws import ChatBedrock

prompt = ChatPromptTemplate.from_messages([
    ('system', '당신은 {style} 스타일의 투자자입니다.'),
    ('human',  '{input}')
])

chain = prompt | llm  # 파이프 연산자로 연결
res = chain.invoke({"input": query, "style": "공격적"})
res.content  # 모델 무관 동일 인터페이스
```

## 한 줄 회고

> "어떤 모델이 더 좋은가"가 아니라 "각 모델 특성에 맞게 프롬프트를 어떻게 설계하는가"가 핵심. LangChain은 이 과정을 코드 레벨에서 추상화하는 도구.

---
🔗 블로그 정리:
[LLM 1 - 프롬프트, 컨텍스트](https://dev-lee.tistory.com/108)