# Nestjs rule & SonarQube

## 1. Yêu cầu Quality Gate mặc định

### 1.1 Các chỉ số chất lượng tối thiểu

```yaml
Quality Gate Requirements:
- Coverage: ≥80%
- Duplicated Lines: ≤3%
- Maintainability Rating: A
- Reliability Rating: A
- Security Rating: A
- Security Hotspots Reviewed: 100%
- Technical Debt Ratio: ≤5%

```

### 1.2 Các tiêu chí đánh giá code

```yaml
Code Smells:
- Blocker: 0
- Critical: 0
- Major: ≤5 per 1000 lines
- Minor: ≤10 per 1000 lines

Bugs:
- Blocker: 0
- Critical: 0
- Major: 0
- Minor: ≤2 per 1000 lines

Vulnerabilities:
- Blocker: 0
- Critical: 0
- Major: 0
- Minor: 0

```

### 5.1 Yêu cầu báo cáo

- Báo cáo SonarQube scan sau mỗi lần push code
- Review và fix các issues được phát hiện trong vòng 2 ngày làm việc
- Duy trì Quality Gate "Passed" status
- Weekly report về technical debt và các metrics

### 5.2 Format báo cáo

```markdown
## Weekly Code Quality Report
Project: [Project Name]
Date: [Report Date]

### Metrics
- Quality Gate Status: [Passed/Failed]
- Coverage: [x]%
- Bugs: [x]
- Vulnerabilities: [x]
- Code Smells: [x]
- Technical Debt: [x]h
- Duplicated Lines: [x]%

### Issues Fixed This Week
- [Issue Description] - [Severity]
- [Issue Description] - [Severity]

### Pending Issues
- [Issue Description] - [Severity] - [Planned Fix Date]
- [Issue Description] - [Severity] - [Planned Fix Date]

```

# Code Formatting & Convention Guidelines

## 1. Quy tắc đặt tên

### 1.1 Class/Interface

- Sử dụng PascalCase
- Tên phải là danh từ hoặc cụm danh từ
- Đặt tên rõ ràng, thể hiện mục đích sử dụng

```tsx
// Good ✅
class UserService
interface PaymentMethod
class AuthenticationMiddleware

// Bad ❌
class Service1
interface DoPayment
class Authentication

```

### 1.2 Method/Function

- Sử dụng camelCase
- Bắt đầu bằng động từ
- Tên phải mô tả được hành động

```tsx
// Good ✅
getUserById()
calculateTotalAmount()
validateEmail()

// Bad ❌
user()
amount()
emailValidation()

```

### 1.3 Variable

- Sử dụng camelCase
- Tên có ý nghĩa, tránh viết tắt
- Constant sử dụng UPPER_SNAKE_CASE

```tsx
// Good ✅
const firstName = 'John'
let totalAmount = 0
const MAX_RETRY_COUNT = 3

// Bad ❌
const fn = 'John'
let ta = 0
const maxretrycount = 3

```

## 2. Code Format

### 2.1 Indentation & Spacing

- Sử dụng 2 spaces cho indentation
- Một dòng không quá 80 ký tự
- Để một dòng trống giữa các blocks logic

```tsx
// Good ✅
function calculateTotal(items: Item[]): number {
  const total = items.reduce((sum, item) => {
    return sum + item.price;
  }, 0);

  return total;
}

// Bad ❌
function calculateTotal(items: Item[]): number {
  const total = items.reduce((sum, item) => { return sum + item.price; }, 0);
  return total;
}

```

### 2.2 Dấu ngoặc & Blocks

- Mở ngoặc nhọn trên cùng dòng
- Luôn sử dụng ngoặc nhọn cho blocks
- Không có khoảng trắng thừa

```tsx
// Good ✅
if (condition) {
  doSomething();
}

// Bad ❌
if (condition)
{
  doSomething();
}
if(condition){ doSomething(); }

```

## 3. Code Organization

### 3.1 File Structure

- Mỗi file chỉ chứa một class/component chính
- Import được nhóm và sắp xếp
- Export ở cuối file

```tsx
// Good ✅
// Third-party imports
import { Injectable } from '@nestjs/common';
import { Repository } from 'typeorm';

// Local imports
import { User } from './user.entity';
import { CreateUserDto } from './dto';

@Injectable()
export class UserService {
  // ... code
}

export { UserService };

// Bad ❌
import { User } from './user.entity';
export class UserService {}
import { Injectable } from '@nestjs/common';

```

### 3.2 Method Organization

- Nhóm methods theo chức năng
- Public methods trước private methods
- Constructor ở đầu class

```tsx
// Good ✅
class UserService {
  constructor() {}

  // Public methods
  async getUsers() {}
  async createUser() {}

  // Private helper methods
  private validateUser() {}
  private formatResponse() {}
}

```

## 4. Documentation

### 4.1 Comments

- Viết comment giải thích WHY, không phải WHAT
- Sử dụng JSDoc cho public APIs
- Tránh comments thừa

```tsx
// Good ✅
/**
 * Validates user age for legal requirements
 * @param age User's age
 * @throws ValidationError if age is under legal limit
 */
function validateAge(age: number): void {
  // Special handling for region-specific age restrictions
  if (age < LEGAL_AGE_LIMIT) {
    throw new ValidationError();
  }
}

// Bad ❌
// Check if age is valid
function validateAge(age: number): void {
  // If age less than 18
  if (age < 18) { // Compare age with 18
    throw new ValidationError(); // Throw error
  }
}

```

## 5. Best Practices

### 5.1 Xử lý Logic

- Một function chỉ làm một việc
- Tránh điều kiện lồng nhau quá 3 cấp
- Ưu tiên early return

```tsx
// Good ✅
function processUser(user: User) {
  if (!user.isActive) {
    return;
  }

  if (!user.hasPermission) {
    throw new Error('No permission');
  }

  // Process user
}

// Bad ❌
function processUser(user: User) {
  if (user.isActive) {
    if (user.hasPermission) {
      // Process user
    } else {
      throw new Error('No permission');
    }
  }
}

```

### 5.2 Error Handling

- Sử dụng custom errors
- Xử lý lỗi ở đúng cấp độ
- Log errors đầy đủ thông tin

```tsx
// Good ✅
try {
  await processPayment();
} catch (error) {
  logger.error('Payment failed', {
    error,
    orderId,
    userId
  });
  throw new PaymentError('Transaction failed');
}

// Bad ❌
try {
  await processPayment();
} catch (error) {
  console.log(error);
  throw error;
}

```

## 6. Kiểm tra code quality

- Sử dụng ESLint với config đã cung cấp
- Chạy Prettier trước khi commit
- Code coverage tối thiểu 80%
- Không có warning từ linter

## 7. Tools yêu cầu

- ESLint
- Prettier
- Git hooks (husky)
- SonarQube