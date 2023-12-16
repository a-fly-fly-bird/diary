+++
title = '编程技巧'
date = 2023-12-17T00:09:12+08:00
draft = false
categories = ['前端']
tags = ['编程技巧']
+++

# 代数数据类型ADT

```typescript
type Props = {
  name: string
} & (
    {
      gender: 'male';
      salary: number
    }
    |
    {
      gender: 'female',
      weight: number
    }
  )
```

这样可以动态地根据`gender`来决定下一个需要的属性是`salary`还是`weight`。

```typescript
type ApiResponse<T> = 
| {status: 'success'; data: T; timestamp: Date}
| {status: 'error'; message: string; timestamp: Date}

let response1: ApiResponse<number> = {
  status: 'success',
  data: 100,
  timestamp: new Date()
};
let response2: ApiResponse<number> = {
  status: 'error',
  message: 'error la',
  timestamp: new Date()
};

```

