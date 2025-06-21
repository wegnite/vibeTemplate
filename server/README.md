# Server 后端项目

## 📋 项目概述

这是 Vibe CLI 项目的后端部分，基于 Node.js + TypeScript 构建，采用分层架构设计，按职责分离、按业务划分。

## 🏗️ 项目结构

```
server/
├── router/             # 接口层 (API 路由)
│   ├── investor/       # 投资者相关接口
│   ├── elite/          # 精英用户相关接口
│   ├── task/           # 任务相关接口
│   └── ai/             # AI 相关接口
├── service/            # 业务逻辑实现层
├── repository/         # 数据操作层
├── database/           # 数据库表结构
└── README.md           # 本文档
```

## 🛠️ 技术栈

### 核心框架
- **Node.js 18+** - 运行时环境
- **Express.js** - Web 框架
- **TypeScript** - 类型安全的 JavaScript

### 数据库
- **Supabase** - PostgreSQL 数据库
- **Prisma** - ORM 工具
- **Redis** - 缓存系统

### 认证和安全
- **JWT** - JSON Web Token
- **bcrypt** - 密码加密
- **helmet** - 安全中间件

### 开发工具
- **nodemon** - 开发服务器
- **ESLint** - 代码质量检查
- **Prettier** - 代码格式化

## 📁 目录详细说明

### `router/` - 接口层
定义 API 路由和请求处理，按业务模块划分：

```
router/
├── investor/           # 投资者相关接口
│   ├── profile.ts      # 投资者资料
│   ├── portfolio.ts    # 投资组合
│   └── index.ts        # 路由入口
├── elite/              # 精英用户相关接口
│   ├── auth.ts         # 认证相关
│   ├── settings.ts     # 用户设置
│   └── index.ts        # 路由入口
├── task/               # 任务相关接口
│   ├── create.ts       # 创建任务
│   ├── manage.ts       # 任务管理
│   └── index.ts        # 路由入口
└── ai/                 # AI 相关接口
    ├── chat.ts         # AI 对话
    ├── analysis.ts     # 数据分析
    └── index.ts        # 路由入口
```

### `service/` - 业务逻辑实现层
实现具体的业务逻辑，处理数据转换和业务规则：

```
service/
├── AuthService.ts      # 认证服务
├── UserService.ts      # 用户服务
├── TaskService.ts      # 任务服务
├── AIService.ts        # AI 服务
└── EmailService.ts     # 邮件服务
```

### `repository/` - 数据操作层
负责数据库操作，封装数据访问逻辑：

```
repository/
├── UserRepository.ts   # 用户数据操作
├── TaskRepository.ts   # 任务数据操作
├── AIRepository.ts     # AI 数据操作
└── BaseRepository.ts   # 基础数据操作
```

### `database/` - 数据库表结构
定义数据库表结构和迁移文件：

```
database/
├── migrations/         # 数据库迁移文件
├── schema.prisma       # Prisma 模式定义
├── seed.ts            # 初始化数据
└── types.ts           # 数据库类型定义
```

## 🚀 开发指南

### 环境要求
- Node.js 18+
- PostgreSQL 14+
- Redis 6+

### 开发流程

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 运行测试
npm run test

# 数据库迁移
npm run migrate

# 数据库重置
npm run reset
```

### API 开发规范

#### 1. 路由定义
```typescript
// router/auth/login.ts
import { Router } from 'express';
import { AuthService } from '@/service/AuthService';

const router = Router();

router.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    const result = await AuthService.login(email, password);
    res.json({ code: 200, data: result });
  } catch (error) {
    res.status(400).json({ code: 400, message: error.message });
  }
});

export default router;
```

#### 2. 服务层实现
```typescript
// service/AuthService.ts
import { UserRepository } from '@/repository/UserRepository';
import { TokenService } from '@/service/TokenService';
import bcrypt from 'bcrypt';

export class AuthService {
  static async login(email: string, password: string) {
    const user = await UserRepository.findByEmail(email);
    if (!user) {
      throw new Error('用户不存在');
    }

    const isValid = await bcrypt.compare(password, user.password);
    if (!isValid) {
      throw new Error('密码错误');
    }

    const token = TokenService.generate(user.id);
    return { user, token };
  }
}
```

#### 3. 数据层操作
```typescript
// repository/UserRepository.ts
import { prisma } from '@/database/client';
import { User } from '@/types/interface/user';

export class UserRepository {
  static async findByEmail(email: string): Promise<User | null> {
    return await prisma.user.findUnique({
      where: { email }
    });
  }

  static async create(userData: Partial<User>): Promise<User> {
    return await prisma.user.create({
      data: userData
    });
  }
}
```

## 🔧 配置管理

### 环境变量
```env
# 数据库配置
DATABASE_URL="postgresql://user:password@localhost:5432/vibe_db"

# JWT 配置
JWT_SECRET="your-secret-key"
JWT_EXPIRES_IN="7d"

# Redis 配置
REDIS_URL="redis://localhost:6379"

# 第三方服务
SUPABASE_URL="https://your-project.supabase.co"
SUPABASE_ANON_KEY="your-anon-key"
```

### 数据库配置
```prisma
// database/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  password  String
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

## 🤝 团队协作

### 开发责任
- **按功能模块负责**: 每个开发者负责特定业务模块的完整实现
- **端到端开发**: 从路由到数据库的完整功能开发
- **接口设计**: 与前端共同维护接口定义

### 代码规范
```typescript
// 统一的响应格式
interface ApiResponse<T> {
  code: number;
  message?: string;
  data?: T;
}

// 统一的错误处理
class ApiError extends Error {
  constructor(public code: number, message: string) {
    super(message);
  }
}
```

### 测试规范
```typescript
// 单元测试示例
describe('AuthService', () => {
  it('should login successfully with valid credentials', async () => {
    const result = await AuthService.login('test@example.com', 'password');
    expect(result).toHaveProperty('token');
    expect(result.user.email).toBe('test@example.com');
  });
});
```

## 📊 性能优化

### 数据库优化
- 使用数据库索引
- 查询优化和缓存
- 连接池管理

### API 性能
- 请求限流
- 响应缓存
- 异步处理

## 🐛 调试和监控

### 日志系统
```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});
```

### 健康检查
```typescript
// router/health.ts
router.get('/health', (req, res) => {
  res.json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    uptime: process.uptime()
  });
});
```

---

**Happy Coding! 🚀** 