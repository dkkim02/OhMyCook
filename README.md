<div align="center">
<img width="1200" height="475" alt="OhMyCook Banner" src="https://github.com/user-attachments/assets/0aa67016-6eaf-458a-adb2-6e31a0763ed6" />

# 🍳 OhMyCook

**냉장고 속 재료만 입력하면, AI가 만들 수 있는 요리를 추천해주는 모바일 우선 레시피 어시스턴트**

[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.8-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Vite](https://img.shields.io/badge/Vite-6-646CFF?logo=vite&logoColor=white)](https://vite.dev)
[![Gemini](https://img.shields.io/badge/Google_Gemini-2.5_Flash-4285F4?logo=google&logoColor=white)](https://ai.google.dev)
[![Supabase](https://img.shields.io/badge/Supabase-3FCF8E?logo=supabase&logoColor=white)](https://supabase.com)

</div>

---

## 📖 소개

> "집에 재료는 있는데 오늘 뭐 해먹지?"

OhMyCook은 이 일상적인 고민을 해결하기 위해 만든 AI 레시피 앱입니다. 보유한 식재료를 등록하면 **Google Gemini**가 그 재료로 만들 수 있는 요리를 추천하고, 부족한 재료·대체 재료·단계별 조리법까지 제안합니다. 영수증을 찍으면 재료를 자동으로 인식해주고, AI 셰프와 대화하며 요리 중 궁금한 점을 바로 물어볼 수도 있습니다.

## ✨ 주요 기능

| | 기능 | 설명 |
|---|---|---|
| 🧊 | **재료 관리 (Pantry)** | 보유 재료를 직접 등록하거나, **영수증 사진을 찍으면 AI가 품목을 자동 인식**해 추가 |
| 🍳 | **AI 레시피 추천** | 재료·인분·맵기·난이도·조리시간 필터를 기반으로 맞춤 레시피 5종을 생성 |
| ⚡ | **2단계 로딩** | 요리 목록·요약을 먼저 빠르게 보여주고, 선택한 레시피의 상세 조리법만 추가로 불러와 체감 속도 개선 |
| 👨‍🍳 | **AI 셰프 챗봇** | 요리 수준·알레르기·보유 도구 등 사용자 프로필과 현재 레시피를 맥락으로 한 대화형 요리 상담 |
| 🖼️ | **자동 요리 이미지** | AI가 생성한 검색 키워드로 이미지를 찾아 레시피 썸네일을 자동 매칭 |
| 👥 | **커뮤니티** | 만든 레시피를 공유하고 좋아요·댓글, Supabase Realtime으로 실시간 반영 |
| 🛒 | **장보기 리스트 & 저장한 레시피** | 부족한 재료는 장보기 목록으로, 마음에 든 레시피는 북마크 |
| 🌐 | **한 / 영 다국어** | 전체 UI와 AI 응답 언어를 전환 가능 |

## 🏗️ 기술 스택

- **Frontend** — React 19, TypeScript, Vite, Framer Motion (페이지 전환·애니메이션)
- **AI** — Google Gemini 2.5 Flash (구조화된 JSON 출력 · 이미지 OCR · 멀티턴 챗)
- **Backend** — Vercel Edge Function (API 프록시), Supabase (Auth · Postgres · Realtime · RLS)
- **저장** — localStorage (게스트/디바이스 캐시) + Supabase (영속 데이터)

## 💡 설계 포인트

- **API 키 보안** — Gemini 호출을 프론트에서 직접 하지 않고 Vercel Edge Function(`/api/gemini`)으로 프록시해 키를 서버에만 노출합니다.
- **Structured Output** — Gemini `responseSchema`로 레시피를 타입 안전한 JSON으로 강제해 파싱 안정성을 확보했습니다.
- **2단계 페칭 (Overview → Detail)** — 목록은 가볍게, 상세는 사용자가 클릭한 레시피에 한해 온디맨드로 불러와 비용과 지연을 줄였습니다.
- **회복탄력성** — `503 / overloaded` 응답에 지수 백오프 재시도를 적용했습니다.
- **멀티모달 입력** — 영수증 이미지 → 재료 추출(Vision OCR)로 입력 마찰을 최소화했습니다.

## 🗂️ 프로젝트 구조

```
OhMyCook/
├── api/gemini.ts          # Vercel Edge Function — Gemini 프록시 (추천/상세/OCR/챗)
├── components/            # UI 컴포넌트 (재료 관리, 추천, AI 셰프, 커뮤니티 등)
├── services/              # geminiService · supabaseClient · supabaseService
├── context/               # LanguageContext (다국어)
├── hooks/                 # useLocalStorage
├── i18n/                  # 한/영 번역 리소스
├── data/                  # 기본 재료·인기 레시피 데이터
├── types.ts               # 공통 타입 정의
└── App.tsx                # 라우팅·상태 관리 진입점
```

## 🚀 로컬 실행

**사전 준비:** Node.js

1. 의존성 설치
   ```bash
   npm install
   ```
2. 프로젝트 루트에 `.env.local` 파일을 만들고 키를 채웁니다
   ```bash
   # Gemini (서버에서 API_KEY로도 사용됨)
   GEMINI_API_KEY=your_gemini_key

   # 레시피 이미지 검색용 (선택)
   GOOGLE_SEARCH_CX=your_custom_search_engine_id

   # Supabase
   VITE_SUPABASE_URL=https://your-project.supabase.co
   VITE_SUPABASE_ANON_KEY=your_public_anon_key
   ```
3. 개발 서버 실행 (기본 포트 `3000`)
   ```bash
   npm run dev
   ```

> 💡 `getRecipeRecommendations`의 이미지 검색은 Google Custom Search(`GOOGLE_SEARCH_CX`)를 사용합니다. 설정하지 않으면 레시피는 정상 동작하되 썸네일만 비어 있습니다.

## 🗄️ Supabase 설정

SQL 에디터에서 아래 테이블을 생성합니다.

```sql
-- Supabase Auth 사용자와 매핑되는 기본 프로필
create table if not exists public.user_profiles (
  id uuid primary key references auth.users(id),
  email text not null unique,
  display_name text,
  has_completed_onboarding boolean default false,
  preferred_cuisines text[] default '{}',
  inserted_at timestamptz default now(),
  updated_at timestamptz default now()
);

-- 검색 로그 (분석/관측용)
create table if not exists public.search_events (
  id bigint generated always as identity primary key,
  user_id uuid references auth.users(id),
  recipe_name text not null,
  search_term text not null,
  created_at timestamptz default now()
);
create index if not exists search_events_recipe_idx on public.search_events (recipe_name);

-- 인기 레시피 집계 카운터
create table if not exists public.recipe_search_counts (
  recipe_name text primary key,
  search_count integer not null default 0
);

-- 사용자가 보유한 재료
create table if not exists public.user_ingredients (
  id bigint generated always as identity primary key,
  user_id uuid not null references auth.users(id),
  ingredient_name text not null,
  quantity text not null,
  created_at timestamptz default now()
);
create index if not exists user_ingredients_user_idx on public.user_ingredients (user_id);
```

이어서 Row Level Security를 활성화하고 정책을 추가합니다.

```sql
alter table public.user_profiles enable row level security;
alter table public.search_events enable row level security;
alter table public.recipe_search_counts enable row level security;
alter table public.user_ingredients enable row level security;

-- 본인 프로필/재료만 관리
create policy "users manage their profile" on public.user_profiles
  for all using (auth.uid() = id) with check (auth.uid() = id);

create policy "users manage their ingredients" on public.user_ingredients
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);

-- 인기 레시피는 익명 읽기 허용
create policy "read recipe counts" on public.recipe_search_counts
  for select using (true);

-- 검색 이벤트 기록 허용 (익명 포함)
create policy "log search events" on public.search_events
  for insert using (true) with check (true);
```

테이블이 준비되면 `services/supabaseService.ts`의 헬퍼 함수를 사용할 수 있습니다.

- `saveUserProfile(profile)` — 가입/온보딩 데이터 upsert
- `recordRecipeSearch({ userId, recipeName, searchTerm })` — 검색 로그 기록 및 인기 카운트 증가
- `getPopularRecipes(limit)` — 가장 많이 검색된 레시피 조회
- `replaceUserIngredients(userId, ingredients)` / `appendUserIngredient` — 사용자별 보유 재료 저장

## 📜 스크립트

| 명령어 | 설명 |
|---|---|
| `npm run dev` | 개발 서버 실행 |
| `npm run build` | 프로덕션 빌드 |
| `npm run preview` | 빌드 결과 미리보기 |
