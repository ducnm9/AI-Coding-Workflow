# Agent Settings — AI Coding Workflow

Cấu hình cho 4 AI coding agents, dựa trên pipeline TDD từ `ai_coding_workflow.md`.

## Cấu trúc

```
agents/
├── github-copilot/
│   └── .github/copilot-instructions.md    # Copilot custom instructions
├── claude/
│   └── CLAUDE.md                          # Claude Code project instructions
├── aider/
│   ├── .aider.conf.yml                    # Aider config (model, auto-test, lint)
│   └── .aider.prompt.md                   # Aider system prompt
└── kiro/
    └── .kiro/
        ├── steering/
        │   ├── workflow.md                # Pipeline TDD (auto-included)
        │   └── code-style.md             # Code style rules (auto-included)
        └── hooks/
            ├── lint-on-save.json          # Auto lint khi save
            ├── test-after-code.json       # Auto test sau task
            └── review-write.json          # Review trước khi write
```

## Cách sử dụng

### GitHub Copilot
Copy `github-copilot/.github/copilot-instructions.md` vào root project `.github/`.

### Claude Code
Copy `claude/CLAUDE.md` vào root project.

### Aider
Copy `aider/.aider.conf.yml` và `aider/.aider.prompt.md` vào root project.
Chỉnh `lint-cmd` và `test-cmd` theo project.

### Kiro
Copy folder `kiro/.kiro/` vào root project.
Chỉnh command trong hooks theo project (npm/yarn/pnpm).

## Pipeline chung

1. **Spec** → Định nghĩa rõ input/output/rules/edge cases
2. **Test** → Viết test TRƯỚC code (TDD)
3. **Code** → Implement để pass tests
4. **Run** → Chạy test thật, lấy error thật
5. **Fix** → Fix implementation, KHÔNG sửa test
6. **Debug** → First principles khi fix loop fail
7. **E2E** → Validate user flows
8. **Refactor** → Clean up, giữ behavior
