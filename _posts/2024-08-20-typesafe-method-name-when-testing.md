---
layout: post
title: Typesafe method name when testing
author: Gabriel Galer
tags: [typescript, testing, jest]
---

First, let's say we have the following code structure:

```typescript
// src/boundaries/LanguageModelService.ts
export interface LanguageModelService {
  getLanguageModel: () => void;
};

// src/services/MyLanguageModelService.ts
import { LanguageModelService } from '../boundaries/LanguageModelService';

export const myLanguageModelService: LanguageModelService = {
  getLanguageModel: () => {
    console.log('log!');
  },
};
```

You can use a simple util function called `getCoreMethodName` to <span class="highlight-yellow">get the method name in a typesafe way when testing</span>.

```typescript
// src/utils/getCoreMethodName.ts
export const getCoreMethodName = <T>(method: keyof T) => method;
```

Now, when we test the `getLanguageModel` method in `MyLanguageModelService`, we can use the `getCoreMethodName` function to get the method name in a typesafe way.

```typescript
// src/services/MyLanguageModelService.test.ts
import { myLanguageModelService } from './MyLanguageModelService';
import { getCoreMethodName } from '../utils/getCoreMethodName';

const mn = getCoreMethodName<LanguageModelService>;

describe(mn('getLanguageModel'), () => {
  it('should log something', () => {
    const spy = jest.spyOn(console, 'log');

    myLanguageModelService.getLanguageModel();

    expect(spy).toHaveBeenCalledWith('log!');
  });
});
```

This works great because if we change the method name in `LanguageModelService`, <span class="highlight-yellow">the test will fail to compile, and we'll know we need to update the test</span>. This is a great way to ensure that your tests stay in sync with your code.