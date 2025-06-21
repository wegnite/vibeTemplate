# Client 前端项目

## 📋 项目概述

这是 Vibe CLI 项目的前端部分，基于现代化的 JavaScript/TypeScript 技术栈构建，采用组件化开发模式。

## 🏗️ 项目结构

```
client/
├── components/          # 组件层 (基于 shadcn/ui)
├── pages/              # 页面层
├── hooks/              # React Hooks 钩子层
├── services/           # 服务层 (面向对象)
├── node_modules/       # 依赖包
└── README.md           # 本文档
```

## 🛠️ 技术栈

### 核心框架
- **React 18+** - 用户界面库
- **Next.js 14+** - 全栈 React 框架
- **TypeScript** - 类型安全的 JavaScript

### UI 组件库
- **shadcn/ui** - 基于 Radix UI 的组件库
- **Tailwind CSS** - 原子化 CSS 框架
- **Lucide React** - 图标库

### 状态管理
- **Zustand** - 轻量级状态管理
- **React Query** - 服务端状态管理

### 开发工具
- **ESLint** - 代码质量检查
- **Prettier** - 代码格式化
- **Husky** - Git hooks

## 📁 目录详细说明

### `components/` - 组件层
存放所有可复用的 React 组件，按功能模块划分：

```
components/
├── ui/                 # 基础 UI 组件 (shadcn/ui)
├── layout/             # 布局组件
├── forms/              # 表单组件
├── charts/             # 图表组件
└── common/             # 通用组件
```

### `pages/` - 页面层
存放页面级组件，每个文件对应一个路由：

```
pages/
├── dashboard/          # 仪表板页面
├── settings/           # 设置页面
├── profile/            # 用户资料页面
└── auth/               # 认证相关页面
```

### `hooks/` - 钩子层
存放自定义 React Hooks：

```
hooks/
├── useAuth.ts          # 认证相关 Hook
├── useApi.ts           # API 调用 Hook
├── useStorage.ts       # 本地存储 Hook
└── useTheme.ts         # 主题切换 Hook
```

### `services/` - 服务层
存放业务逻辑和 API 调用的服务类：

```
services/
├── AuthService.ts      # 认证服务
├── ApiService.ts       # API 基础服务
├── UserService.ts      # 用户相关服务
└── PaymentService.ts   # 支付相关服务
```

## 🚀 开发指南

### 环境要求
- Node.js 18+
- npm/yarn/pnpm

### 开发流程

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 类型检查
npm run type-check

# 代码质量检查
npm run lint

# 代码格式化
npm run format
```

### 组件开发规范

#### 1. 组件命名
- 使用 PascalCase 命名组件文件
- 组件导出使用 default export
- 组件内部使用 named export

```typescript
// components/ui/Button.tsx
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary';
}

export default function Button({ children, variant = 'primary' }: ButtonProps) {
  return (
    <button className={`btn btn-${variant}`}>
      {children}
    </button>
  );
}
```

#### 2. 页面组件结构
```typescript
// pages/Dashboard.tsx
import { useEffect } from 'react';
import { useAuth } from '@/hooks/useAuth';
import { DashboardLayout } from '@/components/layout';

export default function Dashboard() {
  const { user, isLoading } = useAuth();

  useEffect(() => {
    // 页面初始化逻辑
  }, []);

  if (isLoading) return <div>Loading...</div>;

  return (
    <DashboardLayout>
      <h1>仪表板</h1>
      {/* 页面内容 */}
    </DashboardLayout>
  );
}
```

#### 3. Hook 开发规范
```typescript
// hooks/useAuth.ts
import { create } from 'zustand';
import { AuthService } from '@/services/AuthService';

interface AuthState {
  user: User | null;
  isLoading: boolean;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuth = create<AuthState>((set, get) => ({
  user: null,
  isLoading: false,
  
  login: async (email, password) => {
    set({ isLoading: true });
    try {
      const user = await AuthService.login(email, password);
      set({ user, isLoading: false });
    } catch (error) {
      set({ isLoading: false });
      throw error;
    }
  },
  
  logout: () => {
    AuthService.logout();
    set({ user: null });
  }
}));
```

## 🔧 配置文件

### TypeScript 配置
项目使用根目录的 `tsconfig.json` 配置，支持路径别名：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["client/*"],
      "@/components/*": ["client/components/*"],
      "@/hooks/*": ["client/hooks/*"],
      "@/services/*": ["client/services/*"],
      "@/types/*": ["types/*"]
    }
  }
}
```

## 🤝 团队协作

### 开发责任人
- **负责人**: 前端开发者
- **职责范围**: 
  - 用户界面设计和实现
  - 前端状态管理
  - 组件库维护
  - 前端性能优化

### 与后端协作
- 共同维护 `types/interface/` 中的接口定义
- 使用 TypeScript 确保类型安全
- 定期进行前后端联调测试

### 代码提交规范
```bash
# 功能开发
git commit -m "feat(client): add user profile component"

# 问题修复
git commit -m "fix(client): resolve login form validation issue"

# 样式调整
git commit -m "style(client): update button hover states"
```

## 📊 性能优化

### 代码分割
- 使用 Next.js 的自动代码分割
- 懒加载非关键组件
- 优化第三方库导入

### 性能监控
- 使用 React DevTools Profiler
- 监控 Core Web Vitals
- 定期性能审计

## 🐛 调试指南

### 开发工具
- React Developer Tools
- Redux DevTools (如使用 Redux)
- Browser DevTools

### 常见问题
1. **样式不生效**: 检查 Tailwind CSS 配置
2. **组件不更新**: 检查状态更新逻辑
3. **API 调用失败**: 检查网络请求和错误处理

---

**Happy Coding! 🎉** 