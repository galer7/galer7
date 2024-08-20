---
layout: post
title: Using given-when-then in testing
author: Gabriel Galer
tags: [testing, jest]
---

What I like about given-when-then is that <span class="highlight-yellow">you can deduce the "state tree" just from the test structure</span> and, implicitly, from the terminal output. This is surprisingly helpful when practicing TDD (Test-Driven Development).

<span class="highlight-yellow">I also like to use `And` or `But` to separate the different states of the same test case</span>. This makes the test more readable and easier to understand.

Let's say we have the following code structure:

```typescript
// src/services/AuthService.ts
export class AuthService {
  login(username: string, password: string): boolean {
    const user = this.userRepository.getUserByUsername(username);
    if (!user) {
      return false;
    }
    return user.password === password;
  }
}

// src/repositories/UserRepository.ts
export class UserRepository {
  getUserById(id: string): User {
    return Math.random() > 0.5 ? { id, username: 'existent', password: 'correct' } : null;
  }
}
```

And the following use case:

```typescript
// src/use-cases/LoginUseCase.ts
export class LoginUseCase {
  constructor(
    private authService: AuthService,
    private userRepository: UserRepository
  ) {}

  login(userId: string): boolean {
    const user = this.userRepository.getUserById(userId);
    if (!user) {
      return false;
    }
    return this.authService.login(user.username, user.password);
  }
}
```

For this example, we will test the `LoginUseCase` class. We will use the given-when-then structure to make the test more readable and easier to understand.

```typescript
// src/use-cases/LoginUseCase.test.ts
describe('LoginUseCase', () => {
  describe('Given a login request is fired', () => {
    // Initialize the SUT (System Under Test)
    const sut = new LoginUseCase(new AuthService(), new UserRepository());
    
    describe('When the user does not exist', () => {
      it('It should return false', () => {
        const result = sut.login('nonexistent');
        expect(result).toBe(false);
      });
    });

    describe('When the user exists', () => {
      describe('But the password is incorrect', () => {
        it('It should return false', () => {
          const result = sut.login('existent');
          expect(result).toBe(false);
        });
      });

      describe('And the password is correct', () => {
        it('It should return true', () => {
          const result = sut.login('existent');
          expect(result).toBe(true);
        });
      });
    });
  });
});
```

The output of the test file will be:

```
$ npm test

 PASS  src/use-cases/LoginUseCase.test.ts
  LoginUseCase
    Given a login request is fired
      When the user does not exist
        ✓ It should return false (5ms)
      When the user exists
        ✓ But the password is incorrect (1ms)
        And the password is correct
          ✓ It should return true

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        0.5s
```

This is a simple example, but it shows how you can use given-when-then to structure your tests in a way that makes them easier to understand and maintain. By breaking down your tests into these three parts, you can make your tests more readable and easier to follow, which can help you catch bugs and issues more quickly.