# 머신러닝 전체 Flow — 알파벳 빈도 기반 언어 판별

머신러닝 전체 워크플로우를 베이스라인 모델로 직접 관통해본 학습 노트입니다.

---

## 핵심 요약

### 1. 프로젝트 목표

알파벳을 공유하는 4개 언어(`en`, `fr`, `id`, `tl`)를 **문자 사용 빈도**로 분류하는 다중 분류 모델 구축. 정확도보다 ML Flow 전 구간을 손으로 돌려보는 것이 목적.

### 2. 데이터 형태

| 구분 | 형태 | 설명 |
| --- | --- | --- |
| 피처 | `(n, 26)` | 알파벳 a~z 상대 빈도 |
| 타겟 | `(n,)` | `en` / `fr` / `id` / `tl` |

### 3. ML Flow (6단계)

1. **연구목표 수립** — 다중분류 / 지도학습 정의
2. **데이터 수집** — `.txt` 파일 집합 (Level 1)
3. **전처리** — 노이즈 제거 → 소문자 → 빈도 계산 → 정규화
4. **EDA** — `pivot_table` 집계, bar/line/hist 시각화로 가설 검증
5. **모델 구축** — `RandomForestClassifier` → fit → predict → `joblib` 덤프
6. **시스템 배포** — `gradio`로 웹 UI / ChatInterface 구성

---

## 다루는 주요 키워드

`scikit-learn` `RandomForestClassifier` `정규식` `상대 빈도 정규화` `pandas pivot_table` `joblib` `gradio` `다중 분류`

🔗 블로그 정리:  
[ML 2 - 간단한 구현](https://dev-lee.tistory.com/99)
