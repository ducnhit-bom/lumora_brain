---
status: active
created: 2026-06-25
updated: 2026-06-28
language: vi
sourceDocuments:
  - documents/01_PRD_Weekly_AI_Journal.docx
  - documents/02_FE_Architecture_Implementation.docx
  - documents/03_BE_Architecture_API.docx
  - documents/04_UI_UX_Mockup_Spec.docx
  - documents/Lumora_FE_BE_Phase_Plan.docx
blockedBy: []
blocks: []
---

# Lumora MVP Master Plan

> **Dành cho agent/dev triển khai:** đây là master plan duy nhất cho MVP. Khi bắt đầu code, triển khai theo từng phase, cập nhật checkbox ngay trong file này, và cập nhật tài liệu trong `lumora_brain` nếu có thay đổi về sản phẩm, API contract, kiến trúc hoặc UX.

## Mục Tiêu

Xây dựng Lumora thành một ứng dụng mobile cao cấp cho lập kế hoạch tuần, thực thi daily focus, phản tư ngắn, mood check-in và nhận insight AI cuối tuần. Trải nghiệm phải tạo cảm giác bình tĩnh, thân thiện, cá nhân và sang trọng; không giống Jira, Linear, Todoist, dashboard SaaS hoặc công cụ developer.

## Vòng Lặp MVP

1. Người dùng đăng ký hoặc đăng nhập.
2. Người dùng tạo weekly journey cho tuần hiện tại.
3. Người dùng thêm các focus session quan trọng.
4. AI hoặc mock AI gợi ý lịch tuần theo từng ngày.
5. Người dùng chấp nhận journey.
6. Mỗi ngày, người dùng mở Today, xem focus session, hoàn thành hoặc bỏ qua.
7. Sau khi hoàn thành session, Lumora mở reflection một dòng và mood tùy chọn.
8. Cuối tuần, người dùng xem weekly review với summary, mood, AI insight và recommendation cho tuần tiếp theo.

## Phạm Vi MVP

### Trong Phạm Vi

- Email/password auth.
- Weekly journey planning.
- Focus session management.
- AI-suggested weekly plan với mock/fallback mode.
- Today flow: xem session, detail, complete, undo, skip.
- Reflection ngắn sau completion.
- Mood selector đơn giản.
- Weekly review + AI insight/fallback insight.
- Settings cơ bản: profile, auto-open reflection, preferred focus time, max sessions/day, timezone, logout.
- Dev/staging readiness cho internal beta.

### Ngoài Phạm Vi MVP

- Google Calendar sync.
- Push notification thực sự.
- Native home widget.
- Social sharing.
- AI chat coach dài hạn.
- Dashboard analytics phức tạp.
- Monthly/quarterly insight.
- Figma-to-Flutter automation hoàn chỉnh.

## Source Coverage

| Source | Được đưa vào plan như thế nào |
| --- | --- |
| `01_PRD_Weekly_AI_Journal.docx` | Vision, personas, user flow, MVP scope, success metrics, acceptance criteria. |
| `02_FE_Architecture_Implementation.docx` | Flutter, Riverpod, routing, folder/module direction, screens, API mapping, state handling, reusable UI components. |
| `03_BE_Architecture_API.docx` | FastAPI, PostgreSQL, database schema direction, auth, API contracts, AI service boundary, deployment readiness. |
| `04_UI_UX_Mockup_Spec.docx` | Home/Today, Weekly Planner, AI Suggestion, Journal/Reflection, Weekly Review, Settings UX direction. |
| `Lumora_FE_BE_Phase_Plan.docx` | Phase order, FE/BE split, milestone demos, MVP boundaries, development workflow, success criteria. |

## Repo Và Trách Nhiệm

| Repo | Vai trò | Không chịu trách nhiệm |
| --- | --- | --- |
| `lumora_brain` | Product docs, architecture notes, API contracts, design direction, phase plan, release notes. | App runtime code. |
| `lumora_fe` | Flutter app, navigation, UI, local/session state, API integration, design system implementation, mobile builds. | AI prompt logic, DB rules, server validation. |
| `lumora_be` | FastAPI API, auth, persistence, business rules, AI orchestration, review generation, deployment readiness. | Pixel-perfect UI, mobile navigation. |

## Tech Stack

- FE: Flutter, Dart, Riverpod, go_router, Dio.
- BE: FastAPI, Python, Supabase PostgreSQL, SQLAlchemy, Alembic, DB-backed opaque bearer tokens.
- AI: provider abstraction, mock-first, fallback bắt buộc.
- Docs: Markdown trong `lumora_brain`; `.docx` là source reference.

## Quy Tắc Toàn Cục

- Treat `lumora_brain`, `lumora_fe`, `lumora_be` as separate repos.
- Edit FE qua `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe`, không qua symlink.
- Edit BE qua `/Users/ducnh/Documents/projects/lumora/lumora_be`, không qua symlink.
- Mỗi phase nên tạo được một flow có thể demo, không chỉ layer kỹ thuật rời rạc.
- FE có thể dùng mock data để unblock UI trước khi BE hoàn tất.
- BE phải validate ownership và auth cho mọi tài nguyên user-owned.
- AI không được quyết định security/ownership; output AI phải được validate server-side.
- Không leak raw provider errors ra client.
- UI phải tối giản, ấm, calm, editorial/luxury; tránh cảm giác task board nặng nề.
- Verify FE bằng `flutter analyze` từ `lumora_fe`.
- Verify BE tối thiểu bằng `python3 -m py_compile app/main.py` từ `lumora_be`.

## Quyết Định Phase 0 Cần Chốt

| Quyết định | Default đề xuất | Lý do |
| --- | --- | --- |
| App name | Lumora | Repo và docs đang dùng Lumora. |
| Auth method | Email/password trước | Nhanh nhất cho MVP; Google login để sau. |
| AI strategy | Provider abstraction + mock-first | Không block MVP khi chưa có API key/provider. |
| FE state management | Riverpod | Phù hợp docs hiện tại và Flutter app architecture. |
| BE ORM | SQLAlchemy | Tránh trộn style persistence; SQLAlchemy là default ổn định cho FastAPI MVP. |
| Week start | Monday | Phù hợp thói quen lập kế hoạch tuần ở VN. |
| Reflection max length | Tối đa 500 ký tự; UI khuyến khích khoảng 280 ký tự hoặc ít hơn | Giữ reflection nhẹ, không thành journal dài. |
| Empty reflection | Cho phép skip, không persist skipped reflection trong MVP | Không cản flow completion và giảm scope BE. |
| Analytics | Không hoặc event placeholder tối thiểu | Tránh dashboard/productivity bloat. |
| Deployment target | Railway cho staging MVP | Nhanh cho beta; chỉ đổi nếu Phase 8 phát hiện blocker cụ thể. |

## Phase Status

| Phase | Tên | Status | Repo chính | Kết quả |
| --- | --- | --- | --- | --- |
| 0 | Product Baseline | Completed | `lumora_brain` | Product decisions, contracts, MVP boundaries rõ ràng. |
| 1 | Foundations | Completed | FE + BE | FE/BE chạy local với app/API shell. |
| 2 | Auth + User Base | Completed | FE + BE | User register/login, persist session, vào Today. |
| 3 | Weekly Journey Planning | Completed | FE + BE | User tạo journey, thêm session, nhận và accept fallback AI plan. |
| 4 | Daily Focus Execution | Completed | FE + BE | User xem Today, complete/undo/skip session. |
| 5 | Reflection + Mood | Completed | FE + BE | Completion mở reflection một dòng + mood. |
| 6 | Weekly Review + AI Insight | Completed | FE + BE | User xem weekly summary, fallback insight, recommendation. |
| 7 | Settings + Preferences | Pending | FE + BE | User quản lý preferences cơ bản và logout. |
| 8 | MVP Hardening + Beta | Pending | FE + BE + Brain | App test được end-to-end, có staging/release notes/known issues. |

## Phase 0: Product Baseline

**Goal:** Chuyển các `.docx` hiện có thành quyết định và tài liệu triển khai đủ rõ trước khi code.

**Acceptance Criteria:**

- `lumora_brain/docs/product-overview.md` mô tả MVP loop, persona, success metrics, out-of-scope.
- `lumora_brain/docs/system-architecture.md` mô tả FE/BE split, AI boundary, data ownership.
- `lumora_brain/docs/api-contracts.md` có endpoint draft, request/response shape, auth requirement, error format.
- `lumora_brain/docs/design-guidelines.md` ghi rõ visual direction: warm ivory, champagne accent, calm notebook tone, không dense dashboard.
- Tất cả quyết định trong bảng Phase 0 được chốt hoặc ghi rõ open question.

**Tasks:**

- [x] Extract PRD direction từ `documents/01_PRD_Weekly_AI_Journal.docx` vào `docs/product-overview.md`.
- [x] Extract FE direction từ `documents/02_FE_Architecture_Implementation.docx` vào `docs/system-architecture.md`.
- [x] Extract BE direction từ `documents/03_BE_Architecture_API.docx` vào `docs/system-architecture.md` và `docs/api-contracts.md`.
- [x] Extract UI/UX direction từ `documents/04_UI_UX_Mockup_Spec.docx` vào `docs/design-guidelines.md`.
- [x] Dùng `documents/Lumora_FE_BE_Phase_Plan.docx` để kiểm tra milestone ordering.
- [x] Chốt app name, auth method, AI strategy, ORM, deployment target, week start, reflection length, analytics scope.

**Verification:**

- [x] Review docs mới để đảm bảo FE/BE contract nhất quán.
- [x] Confirm mỗi Phase 1-8 có source trace về ít nhất một `.docx`.

## Phase 1: Foundations

**Goal:** FE và BE chạy local ổn định với structure, theme/API shell, mock mode và docs setup.

### FE Tasks

- [x] Confirm Flutter project chạy từ `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe`.
- [x] Tạo centralized design tokens: warm ivory background, champagne accent, typography, spacing, cards, buttons.
- [x] Cấu hình routing với auth placeholder và main shell tabs: Today, Journey, Reflection/Review, Settings.
- [x] Tạo mock data mode để dev không phụ thuộc BE.
- [x] Tạo reusable base widgets: app scaffold, button, card, loading state, error state, empty state.
- [x] Cập nhật FE `README.md`: setup, run command, mock mode, troubleshooting.

### BE Tasks

- [x] Confirm FastAPI project chạy từ `/Users/ducnh/Documents/projects/lumora/lumora_be`.
- [x] Thêm config/env management và document `.env` keys, không commit secrets.
- [x] Thêm PostgreSQL connection, ORM baseline, Alembic baseline migration.
- [x] Thêm `GET /health` trả OK.
- [x] Thêm CORS config cho mobile development.
- [x] Chuẩn hóa API error response shape.
- [x] Cập nhật BE `README.md`: setup, env, migrations, server start, API docs.

### Integration Tasks

- [x] FE mở placeholder app không cần API.
- [x] BE health endpoint gọi được local.
- [x] Brain docs ghi local ports và run commands.

**Verification:**

- [x] Run `flutter analyze` từ `lumora_fe/lumora_fe`.
- [x] Run `python3 -m py_compile app/main.py` từ `lumora_be`.
- [x] Mở FastAPI docs local nếu có.

## Phase 2: Auth + User Base

**Goal:** User có thể register, login, persist session và vào app chính.

### FE Tasks

- [x] Build welcome/onboarding screen với copy calm/premium.
- [x] Build login screen với email/password validation.
- [x] Build register screen với name/email/password validation.
- [x] Thêm auth state provider và navigation guard.
- [x] Lưu token trong auth session state; secure local persistence sẽ harden thêm khi cần device persistence thật.
- [x] Inject bearer token vào API client requests.
- [x] Thêm logout action từ Settings/Profile.

### BE Tasks

- [x] Thêm user model và migration.
- [x] Thêm password hashing.
- [x] Thêm `POST /auth/register`.
- [x] Thêm `POST /auth/login`.
- [x] Thêm DB-backed opaque token generation, hashing, expiry, validation, revoke.
- [x] Thêm current-user dependency cho protected routes.
- [x] Thêm `GET /users/me`.
- [x] Chuẩn hóa auth errors: duplicate email, bad credentials, invalid token, missing token.

### Integration Tasks

- [x] FE register tạo BE user và vào Today trong API mode; mock mode vẫn hoạt động offline.
- [x] FE login giữ token trong session state và inject bearer header.
- [x] Logout clear session và quay về auth flow.

**Verification:**

- [x] Invalid credentials hiển thị friendly error.
- [x] Protected route reject missing/invalid token.
- [x] Password không bao giờ lưu plain text.

## Phase 3: Weekly Journey Planning

**Goal:** User tạo weekly journey, thêm focus sessions, request AI-curated plan và accept.

### FE Tasks

- [x] Build Weekly Journey screen.
- [x] Build add focus session form: title, optional note, category, priority, estimated duration.
- [x] Build focus session preview list.
- [x] Build Create AI Journey CTA.
- [x] Build AI Suggested Journey screen grouped by day.
- [x] Build accept journey action và return user to Today.
- [x] Giữ edit affordances nhẹ cho MVP.

### BE Tasks

- [x] Thêm weekly journey model: owner, week start, status, created timestamp.
- [x] Thêm focus session model: owner, journey, title, note, category, priority, scheduled datetime, status.
- [x] Thêm `POST /journeys`.
- [x] Thêm `POST /journeys/{journey_id}/sessions`.
- [x] Thêm `GET /journeys/current`.
- [x] Thêm fallback AI service behavior qua deterministic suggestion provider.
- [x] Thêm `POST /journeys/{journey_id}/suggest` trả grouped schedule.
- [x] Thêm `POST /journeys/{journey_id}/accept`.
- [x] Validate ownership trên mọi journey/session endpoint.

### Integration Tasks

- [x] FE tạo journey bằng mock BE hoặc real BE.
- [x] FE hiển thị suggested weekly schedule grouped by day.
- [x] Accepted journey cung cấp active journey data cho Today flow Phase 4.

**Verification:**

- [x] User không access được journey/session của user khác.
- [x] AI failure trả usable fallback plan.
- [x] Invalid AI output không leak raw provider error ra client.

## Phase 4: Daily Focus Execution

**Goal:** Xây core daily experience: Today, detail, complete, undo/skip.

### FE Tasks

- [x] Build Today screen với hero focus card.
- [x] Build Today journey timeline.
- [x] Build focus session detail screen chỉ có thông tin cần thiết.
- [x] Thêm complete session action.
- [x] Thêm undo completion nếu BE hỗ trợ.
- [x] Thêm skip session entry point.
- [x] Thêm friendly no-session empty state.
- [x] Route completion sang Reflection khi response yêu cầu bằng Phase 5 placeholder signal.

### BE Tasks

- [x] Thêm `GET /sessions/today`.
- [x] Thêm `GET /sessions/{session_id}`.
- [x] Thêm `POST /sessions/{session_id}/complete`.
- [x] Thêm `POST /sessions/{session_id}/undo-complete`.
- [x] Thêm `POST /sessions/{session_id}/skip`.
- [x] Return auto-reflection metadata trong completion response.
- [x] Duplicate completion reject an toàn với clear error.

### Integration Tasks

- [x] Today load sessions của current user.
- [x] Complete session update status và mở Reflection placeholder signal.
- [x] Undo trả session về scheduled state.

**Verification:**

- [x] No-session day hiển thị recovery-focused empty state.
- [x] Completing already done session không corrupt data.
- [x] Network failures hiển thị retry/safe error state.

## Phase 5: Reflection + Mood

**Goal:** Reflection sau focus completion phải nhẹ: một câu hỏi AI/fallback, một input ngắn, mood tùy chọn.

### FE Tasks

- [x] Build Reflection screen auto-open sau completion.
- [x] Hiển thị AI question hoặc fallback question.
- [x] Thêm short text input với max length từ Phase 0.
- [x] Thêm mood selector: energized, balanced, challenged.
- [x] Thêm save reflection action.
- [x] Thêm skip reflection action.
- [x] Return to Today sau save hoặc skip.

### BE Tasks

- [x] Thêm reflection model link với user, journey, session, content, mood, created timestamp.
- [x] Thêm `POST /reflections`.
- [x] Thêm `GET /journeys/{journey_id}/reflections`.
- [x] Thêm `GET /sessions/{session_id}/reflection-question` hoặc endpoint tương đương.
- [x] Thêm AI reflection question service với fallback question.
- [x] Validate reflection max length.
- [x] Skip reflection chỉ là frontend navigation action; không persist skipped reflection trong MVP.

### Integration Tasks

- [x] Completion response pass session context sang Reflection.
- [x] Saved reflection data available cho Weekly Review qua journey reflection list.
- [x] Skipping reflection không block Today flow.

**Verification:**

- [x] User save reflection được với hoặc không có mood.
- [x] Empty reflection được handle theo Phase 0 decision.
- [x] Long content bị reject graceful.

## Phase 6: Weekly Review + AI Insight

**Goal:** Cuối tuần hiển thị summary và AI insight truyền cảm hứng, không KPI-heavy.

### FE Tasks

- [x] Build Weekly Review screen.
- [x] Hiển thị completion summary không theo kiểu dense analytics dashboard.
- [x] Hiển thị reflection count.
- [x] Hiển thị mood summary.
- [x] Hiển thị fallback insight card.
- [x] Hiển thị recommendation card.
- [x] Thêm insufficient-data empty state.
- [x] Thêm Create Next Week Journey CTA.

### BE Tasks

- [x] Thêm review aggregation service.
- [x] Calculate sessions completed, reflection count, mood distribution.
- [x] Deferred AI weekly insight provider; Phase 6 dùng fallback deterministic.
- [x] Thêm rule-based fallback insight.
- [x] Thêm `GET /journeys/{journey_id}/review`.
- [x] Return clear empty-state response khi data insufficient.

### Integration Tasks

- [x] FE load review data từ API.
- [x] FE hiển thị fallback insight giống AI insight.
- [x] CTA start next weekly planning flow.

**Verification:**

- [x] Copy supportive, ngắn, không judgmental.
- [x] Không leak raw AI provider errors ra client.
- [x] Empty state rõ ràng khi chưa có data.

## Phase 7: Settings + Preferences

**Goal:** Cung cấp user control cơ bản, không làm MVP phình to.

### FE Tasks

- [ ] Build Settings screen.
- [ ] Hiển thị profile từ current user.
- [ ] Thêm auto-open reflection toggle.
- [ ] Thêm preferred focus time preference.
- [ ] Thêm max sessions per day preference.
- [ ] Thêm theme placeholder nếu design cần.
- [ ] Thêm notification settings placeholder nếu push deferred.
- [ ] Thêm logout action.

### BE Tasks

- [ ] Thêm settings entity hoặc user settings fields.
- [ ] Tạo default settings cho new users.
- [ ] Thêm `GET /settings`.
- [ ] Thêm `PATCH /settings`.
- [ ] Validate auto-open reflection, preferred focus time, max sessions/day, timezone.
- [ ] Expose settings cho future AI planning inputs.

### Integration Tasks

- [ ] FE read settings khi app load hoặc Settings screen open.
- [ ] FE update settings và phản ánh saved state.
- [ ] Logout vẫn reliable sau settings changes.

**Verification:**

- [ ] Invalid preference values bị reject.
- [ ] Defaults hoạt động cho account mới.
- [ ] Settings UI theo cùng luxury design system.

## Phase 8: MVP Hardening + Beta

**Goal:** Chuẩn bị Lumora cho internal testing và beta distribution.

### FE Tasks

- [ ] Thêm global loading/error patterns cho auth, journey, today, reflection, review, settings.
- [ ] Thêm offline/retry behavior ở nơi hợp lý.
- [ ] Review form validation toàn app.
- [ ] Visual polish pass toàn bộ MVP screens.
- [ ] Check accessibility basics: text scale, contrast, touch targets, screen-reader labels nếu thực tế.
- [ ] Thêm analytics event placeholders chỉ nếu Phase 0 approve minimal analytics.
- [ ] Chuẩn bị iOS/Android build configs.
- [ ] Thêm crash/error logging placeholder nếu được chọn.

### BE Tasks

- [ ] Standardize validation và error codes.
- [ ] Thêm unit tests cho services.
- [ ] Thêm integration tests cho auth, planning, daily completion, reflection, review flows.
- [ ] Verify migrations từ clean database.
- [ ] Thêm deployment config cho staging target đã chọn.
- [ ] Thêm basic logging.
- [ ] Thêm rate limit/abuse guard cho AI endpoints nếu provider cost risk cao.

### Brain/Docs Tasks

- [ ] Update `docs/api-contracts.md` với final endpoint shapes.
- [ ] Update `docs/system-architecture.md` với final deployment/runtime decisions.
- [ ] Add release notes cho internal MVP.
- [ ] Add known issues list.

### Integration Tasks

- [ ] Run end-to-end beta smoke test: register, create journey, accept suggestion, complete session, reflect, view review.
- [ ] Confirm FE connect staging API.
- [ ] Confirm AI fallback hoạt động khi provider unavailable.

**Verification:**

- [ ] `flutter analyze` pass.
- [ ] BE syntax check pass.
- [ ] Core BE tests pass khi test suite có.
- [ ] App không có critical navigation dead ends.
- [ ] MVP deploy được lên staging và test end-to-end.

## Cross-Repo Milestones

| Milestone | FE Required | BE Required | Demo Scenario |
| --- | --- | --- | --- |
| M1 App Shell | Navigation shell, placeholder screens | Health endpoint, local API running | Open app và API local. |
| M2 Auth Demo | Login/register screens integrated | Auth endpoints và DB-backed opaque tokens | Register và vào Today. |
| M3 Planning Demo | Weekly Journey + AI suggestion UI | Journey/session APIs + mock AI | Create plan và accept suggested week. |
| M4 Daily Demo | Today + detail + complete flow | Today sessions + complete endpoint | Complete today's focus session. |
| M5 Reflection Demo | Reflection screen + save flow | Reflection API + AI question | Complete session, write one-line reflection. |
| M6 Review Demo | Weekly Review screen | Review aggregation + AI insight | View weekly insight và recommendation. |
| M7 MVP Beta | Polished release build | Staging deploy + tests | End-to-end beta-ready app. |

## Dependencies

- Phase 0 block mọi implementation phase vì product/API/UX decisions chưa chốt.
- Phase 1 block feature phases vì FE/BE shell phải chạy local trước.
- Phase 2 auth block journeys, sessions, reflections, reviews, settings.
- Phase 3 journey/session models block Today, Reflection, Review.
- Phase 4 daily completion block auto Reflection flow.
- Phase 5 reflection data block meaningful Weekly Review.
- Phase 6 có thể mock aggregation sau Phase 3, nhưng final validation cần Phase 5.
- Phase 7 có thể bắt đầu sau Phase 2, nhưng AI planning preferences chỉ có ý nghĩa sau Phase 3.
- Phase 8 cần toàn bộ MVP phases tích hợp.

## Success Criteria

- New user có thể register, create weekly journey, add focus sessions, accept AI-curated plan.
- User có thể mở Today, complete focus session, write one-line reflection, optionally select mood.
- User có thể xem Weekly Review với session summary, reflection count, mood summary, AI insight, recommendation.
- FE và BE đều chạy local theo README.
- UI cảm giác premium, calm, approachable, không giống developer tool hoặc task manager truyền thống.
- Backend có fallback behavior khi AI fail.
- MVP deploy được lên staging và test end-to-end.

## Execution Handoff

Khi bắt đầu triển khai:

1. Hoàn tất Phase 0 docs trong `lumora_brain`.
2. Implement Phase 1 FE và BE foundations song song nếu có agent/dev tách biệt.
3. Deliver từng milestone như vertical slice và demo trước khi đi tiếp.
4. Giữ FE mock mode hoạt động khi BE tạm unavailable.
5. Cập nhật `lumora_brain/docs/api-contracts.md` mỗi khi FE/BE contract đổi.
6. Không commit secrets, `.env`, credentials hoặc API keys.

Suggested next command for interactive implementation:

```bash
/ck:cook /Users/ducnh/Documents/projects/lumora/lumora_brain/plans/260625-2055-lumora-mvp-phase-plan/plan.md --interactive
```
