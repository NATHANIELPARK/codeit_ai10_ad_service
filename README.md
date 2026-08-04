# 소형가전 광고 콘텐츠 생성 서비스

쇼핑몰에 입점한 소형가전 판매 소상공인이 제품 사진 1장을 올리면,
브랜드 톤 4종(감성/모던/실용/프리미엄)과 판매 시간대(아침/출근러시아워/오후/퇴근러시아워/저녁/심야)에
맞춘 광고 이미지·문구 세트를 만들어주는 서비스입니다.

## 담당 (R4+R5: 박재철)
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

## 오늘(Sprint 0) 완료 기준
- [x] 저장소 생성, `.gitignore`에서 `.env`/모델/업로드 이미지 차단, `.env.example` 커밋
- [x] Streamlit → FastAPI 실제 연동: 업로드→선택→생성요청→job 폴링→결과 관통 (더미 모델, 진짜 API)
- [x] `PromptRequest`/`PromptResult` 스키마 확정, 톤 4종·시간대 6종 템플릿 구조 작성
- [x] `docs/api_contract.md` 작성 (R3 model_server 계약 포함: enum, 생성단위, 성공/실패, 타임아웃)
- [x] 생성 단위 시간대×톤으로 수정 (출력 규격은 후처리로 분리)
- [x] Mock/실제 모델 서버를 교체 가능하게 `generation_service.py`로 인터페이스 분리, 실패 시 job "failed" 처리
- [x] 테스트 36개 작성·통과 (`docs/test_results_gate0.txt`)
- [ ] 팀원 초대, 브랜치 전략 공유
- [ ] R3와 API 입력·출력 최종 합의
- [ ] 협업일지에 결정 이유·수정 가능 항목 기록

## 기준 커밋
`gate0-service-v0.1` 태그가 Gate 0 기준(더미 모델 E2E 관통) 상태입니다.
이후 실제 모델 연동 중 문제가 생기면 이 태그로 되돌릴 수 있습니다.

## 참고
- API 계약서: [docs/api_contract.md](docs/api_contract.md)
- Prompt Spec: [docs/prompt_spec.md](docs/prompt_spec.md)
- 아키텍처: [docs/architecture.md](docs/architecture.md)
