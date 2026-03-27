# AI Coding Workflow — Hướng dẫn sử dụng

Bộ cấu hình chuẩn hóa cho 4 AI coding agents, giúp team làm việc với AI theo quy trình **TDD nghiêm ngặt** thay vì chat random.

> **Triết lý:** Không chat với AI → điều khiển AI như system. Không tin output → tin test.

---

## Tổng quan cấu trúc

```
.
├── ai_coding_workflow.md              # Tài liệu gốc — pipeline & nguyên tắc
├── ai/prompts/                        # Prompt templates dùng chung (copy-paste)
│   ├── spec.md                        #   Chuyển feature → technical spec
│   ├── test.md                        #   Viết unit tests (TDD)
│   ├── code.md                        #   Generate code từ tests
│   ├── fix.md                         #   Fix khi test fail
│   ├── debug.md                       #   Debug sâu khi fix loop fail
│   ├── e2e.md                         #   Viết E2E tests
│   └── refactor.md                    #   Refactor giữ behavior
├── agents/
│   ├── github-copilot/                # Settings cho GitHub Copilot
│   ├── claude/                        # Settings cho Claude Code
│   ├── aider/                         # Settings cho Aider
│   └── kiro/                          # Settings cho Kiro
└── README.md                          # File này
```

---

## Pipeline chung (áp dụng cho mọi agent)

```
[1] Spec Definition     →  Định nghĩa rõ input/output/rules/edge cases
        ↓
[2] Test Design (TDD)   →  Viết test TRƯỚC, cover happy + edge + invalid
        ↓
[3] Code Generation     →  Implement để pass tests, KHÔNG sửa tests
        ↓
[4] Execute & Validate  →  Chạy test thật, lấy error thật
        ↓
[5] Fix Loop            →  Paste error → AI fix implementation only
        ↓
[6] Deep Debug          →  First principles khi fix loop fail
        ↓
[7] E2E Validation      →  Playwright/Cypress cho user flows
        ↓
[8] Refactor            →  Clean up, giữ nguyên behavior
```

---

## Hướng dẫn cài đặt từng Agent

### 1. GitHub Copilot

**File:** `agents/github-copilot/.github/copilot-instructions.md`

**Cách cài:**
```bash
# Copy vào root project
cp -r agents/github-copilot/.github/ <your-project>/.github/
```

**Cách hoạt động:**
- GitHub Copilot tự động đọc file `.github/copilot-instructions.md` làm system instructions
- Áp dụng cho tất cả thành viên trong repo
- Copilot sẽ tuân theo pipeline TDD khi generate code

**Yêu cầu:** GitHub Copilot extension trong VS Code hoặc JetBrains.

---

### 2. Claude Code

**File:** `agents/claude/CLAUDE.md`

**Cách cài:**
```bash
# Copy vào root project
cp agents/claude/CLAUDE.md <your-project>/CLAUDE.md
```

**Cách hoạt động:**
- Claude Code tự động đọc `CLAUDE.md` ở root project khi mở terminal
- Hoạt động như project-level instructions
- Claude sẽ từ chối chat random, chỉ làm việc theo pipeline

**Yêu cầu:** Claude Code CLI (`claude` command).

---

### 3. Aider

**Files:**
- `agents/aider/.aider.conf.yml` — Config (model, auto-test, auto-lint)
- `agents/aider/.aider.prompt.md` — System prompt

**Cách cài:**
```bash
# Copy vào root project
cp agents/aider/.aider.conf.yml <your-project>/
cp agents/aider/.aider.prompt.md <your-project>/
```

**Cấu hình cần chỉnh theo project:**

Mở `.aider.conf.yml` và sửa:
```yaml
# Đổi model nếu cần
model: claude-sonnet-4-20250514

# Đổi lint command theo project
lint-cmd: "npm run lint"          # hoặc: yarn lint, pnpm lint, ruff check .

# Đổi test command theo project
test-cmd: "npm test"              # hoặc: yarn test, pytest, go test ./...
```

**Cách hoạt động:**
- `auto-test: true` → Aider tự chạy test sau mỗi lần sửa code
- `auto-lint: true` → Aider tự lint sau mỗi lần sửa code
- `auto-commits: false` → Không tự commit, bạn kiểm soát git
- `.aider.prompt.md` được load tự động làm system prompt

**Yêu cầu:** Aider CLI (`pip install aider-chat`).

---

### 4. Kiro

**Folder:** `agents/kiro/.kiro/`

**Cách cài:**
```bash
# Copy vào root project
cp -r agents/kiro/.kiro/ <your-project>/.kiro/
```

**Cấu hình cần chỉnh theo project:**

Mở các file trong `.kiro/hooks/` và sửa command:
```json
// lint-on-save.json → đổi command
"command": "npm run lint --fix"     // hoặc: yarn lint, pnpm lint

// test-after-code.json → đổi command
"command": "npm test"               // hoặc: yarn test, pnpm test
```

**Kiro có 3 thành phần:**

**Steering files** (tự động load vào mọi conversation):
| File | Mô tả |
|------|--------|
| `.kiro/steering/workflow.md` | Pipeline TDD đầy đủ 8 bước |
| `.kiro/steering/code-style.md` | Quy tắc output: code chạy được ngay, không giải thích dài |

**Hooks** (tự động trigger theo event):
| Hook | Trigger | Hành động |
|------|---------|-----------|
| `lint-on-save.json` | Save file `.ts/.tsx/.js/.jsx` | Chạy `npm run lint --fix` |
| `test-after-code.json` | Agent hoàn thành task | Chạy `npm test` |
| `review-write.json` | Trước khi agent write file | Kiểm tra: không sửa test file, follow clean code |

**Yêu cầu:** Kiro IDE.

---

## Prompt Templates (`ai/prompts/`)

Dùng cho bất kỳ AI tool nào (ChatGPT, Claude web, Gemini...) bằng cách copy-paste.

| Bước | File | Cách dùng |
|------|------|-----------|
| Spec | `ai/prompts/spec.md` | Paste feature request vào `[PASTE FEATURE HERE]` |
| Test | `ai/prompts/test.md` | Paste spec vào `[PASTE SPEC HERE]` |
| Code | `ai/prompts/code.md` | Paste tests vào `[PASTE TEST HERE]` |
| Fix | `ai/prompts/fix.md` | Paste error log + code vào placeholders |
| Debug | `ai/prompts/debug.md` | Paste code + error vào `[CODE + ERROR]` |
| E2E | `ai/prompts/e2e.md` | Paste spec vào `[PASTE SPEC HERE]` |
| Refactor | `ai/prompts/refactor.md` | Paste code vào `[PASTE CODE]` |

**Workflow mẫu:**
```
1. Copy spec.md    → paste feature → nhận spec
2. Copy test.md    → paste spec    → nhận tests
3. Copy code.md    → paste tests   → nhận code
4. Chạy test thật  → nếu fail:
5. Copy fix.md     → paste error   → nhận fix
6. Vẫn fail?       → copy debug.md → paste code + error
7. Pass?           → copy e2e.md   → nhận E2E tests
8. Done?           → copy refactor.md → clean up
```

---

## Nguyên tắc cốt lõi

| ❌ Không làm | ✅ Làm thay thế |
|-------------|-----------------|
| Chat random với AI | Dùng prompt template có cấu trúc |
| Viết code trước | Viết test trước (TDD) |
| Fix bằng cảm tính | Fix bằng error log thực tế |
| Tin output AI | Tin test results |
| Sửa test cho pass | Sửa implementation cho pass |
| Rewrite toàn bộ | Fix chính xác chỗ sai |

---

## Quick Start

```bash
# 1. Clone repo này
git clone <repo-url>

# 2. Chọn agent bạn dùng và copy vào project
# Ví dụ: dùng Kiro
cp -r agents/kiro/.kiro/ ~/my-project/.kiro/

# 3. Chỉnh commands trong config cho phù hợp project

# 4. Bắt đầu code theo pipeline:
#    Spec → Test → Code → Run → Fix → E2E → Refactor
```

---

## FAQ

**Q: Dùng nhiều agent cùng lúc được không?**
A: Được. Mỗi agent đọc file config riêng, không conflict. Bạn có thể copy tất cả vào cùng 1 project.

**Q: Dùng Python thay vì JavaScript?**
A: Đổi test/lint commands: `pytest` thay `npm test`, `ruff check .` thay `npm run lint`.

**Q: Prompt templates dùng cho ChatGPT/Gemini được không?**
A: Được. Folder `ai/prompts/` là prompt thuần text, dùng cho bất kỳ AI nào.

**Q: Tôi muốn thêm bước vào pipeline?**
A: Tạo thêm prompt template trong `ai/prompts/`, rồi cập nhật agent instructions tương ứng.
