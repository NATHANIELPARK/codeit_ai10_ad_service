# 소형가전 광고 콘텐츠 생성 서비스

쇼핑몰에 입점한 소형가전 판매 소상공인이 제품 사진 1장을 올리면,
브랜드 톤 4종(감성/모던/실용/프리미엄)과 판매 시간대(아침/출근러시아워/오후/퇴근러시아워/저녁/심야)에
맞춘 광고 이미지·문구 세트를 만들어주는 서비스입니다.

- `app/frontend` — Streamlit UI/UX
- `app/backend` — FastAPI 로직·상태관리
- `app/prompt` — Prompt Builder (톤×시간대 템플릿 → image_prompt/ad_copy)
- 이미지 생성 자체(`model_server/`)는 R2·R3 담당. 이 레포의 backend는 **호출만** 합니다.

## 디렉토리 구조
```
project-root/
├── app/
│   ├── frontend/
│   │   ├── streamlit_app.py
│   │   └── pages/           # 1_Product / 2_Generate / 3_History
│   ├── backend/
│   │   ├── api/              # products / generations / jobs / history / usage
│   │   ├── schemas/
│   │   ├── services/         # openai_client, model_server_client, overlay, store
│   │   └── main.py
│   └── prompt/
│       ├── builder.py
│       ├── templates.py
│       └── schemas.py
├── model_server/              # R2·R3 작업 공간 (placeholder)
├── data/{samples,outputs}/
├── tests/
├── docs/{api_contract,prompt_spec,architecture}.md
├── .env.example / .gitignore
├── requirements.txt / pyproject.toml
└── README.md
```

## 브랜치 전략
- `main` — 항상 실행 가능한 상태만
- `develop` — 통합 브랜치
- `feature/*` — 예: `feature/frontend-skeleton`, `feature/prompt-builder`, `feature/api-contract`
- 흐름: `feature/* → develop → main`
- PR 최소 1인 리뷰 (기본 리뷰어: 안은남) — 그라운드룰
- 8/22 Gate 2 이후 신규 기능 PR 금지 (기능 프리즈)

## 로컬 셋업
```bash
# 1. 저장소 clone
git clone <repo-url> && cd ad-service-v2

# 2. 가상환경 생성
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. requirements 설치
pip install -r requirements.txt

# 4. .env.example -> .env 복사 (실제 값 채우기, 커밋 금지)
cp .env.example .env

# 5. FastAPI 실행
uvicorn app.backend.main:app --reload --port 8000

# 6. Streamlit 실행 (다른 터미널)
streamlit run app/frontend/streamlit_app.py

# 7. 테스트 실행
pytest -q
```



## 기준 커밋
`gate0-service-v0.1` 태그가 Gate 0 기준(더미 모델 E2E 관통) 상태입니다.
이후 실제 모델 연동 중 문제가 생기면 이 태그로 되돌릴 수 있습니다.

## 참고
- API 계약서: [docs/api_contract.md](docs/api_contract.md)
- Prompt Spec: [docs/prompt_spec.md](docs/prompt_spec.md)
- 아키텍처: [docs/architecture.md](docs/architecture.md)
