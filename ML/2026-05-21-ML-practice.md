# 머신러닝 전체 Flow 실습

알파벳 빈도로 4개 언어(`en`, `fr`, `id`, `tl`) 판별하는 분류 모델.

## 한 일

- 텍스트 파일에서 알파벳 빈도 추출 → `(n, 26)` 피처 구성
- pandas로 EDA, 언어별 빈도 차이 시각화
- `RandomForestClassifier` 학습 → `joblib`로 모델 덤프
- `gradio`로 간단한 웹 UI 배포

## Stack

`scikit-learn` · `pandas` · `matplotlib` · `gradio`

