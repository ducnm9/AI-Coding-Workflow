# AI Coding Workflow (Production-Grade)

## Nguyên tắc cốt lõi
- Không chat với AI → điều khiển AI như system
- Không viết code trước → viết test trước
- Không fix cảm tính → fix bằng feedback loop
- Không tin output → tin test

---

## Tổng thể pipeline

[1] Spec Definition  
↓  
[2] Test Design (TDD)  
↓  
[3] AI Code Generation  
↓  
[4] Execute & Validate  
↓  
[5] AI Fix Loop  
↓  
[6] E2E Validation  
↓  
[7] Template hóa  

---

## 1. Spec Definition

Define rõ:
- Input
- Output
- Business rules
- Edge cases
- Error handling

### Ví dụ

Function: calculateDiscount(user, order)

Rules:
- user.vip = true → giảm 20%
- order.total > 1,000,000 → giảm thêm 10%
- max discount = 30%

Edge cases:
- order.total = 0
- user null

---

## 2. Test Design

Prompt:

Write unit tests for the following spec.  
Use Jest.  
Cover normal + edge + invalid.

---

## 3. Code Generation

Prompt:

Implement function to pass ALL tests.  
Do not modify tests.

---

## 4. Execute & Validate

- Run test thật
- Lấy error thật

---

## 5. AI Fix Loop

Prompt:

The following tests are failing:

[error logs]

Constraints:
- Do not change tests
- Fix implementation only

---

## 6. E2E Validation

- Playwright / Cypress
- Cover real user flow

---

## 7. Template hóa

Lưu lại:
- Prompt template
- Code pattern
- Test pattern

---

## Anti-pattern

- Chat random với AI
- Skip test
- Fix tay quá sớm
- Không reuse

---

## Kết luận

AI chỉ mạnh khi có system control.  
Không có system → entropy  
Có system → scale
