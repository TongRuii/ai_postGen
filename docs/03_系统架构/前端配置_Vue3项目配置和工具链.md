# Vue 3 项目配置和开发工具链

**版本**: V1.0
**创建日期**: 2025年10月26日

---

## 目录

1. [项目初始化](#项目初始化)
2. [依赖配置](#依赖配置)
3. [Vite 配置](#vite-配置)
4. [TypeScript 配置](#typescript-配置)
5. [ESLint 和 Prettier](#eslint-和-prettier)
6. [环境变量](#环境变量)
7. [开发脚本](#开发脚本)
8. [测试配置](#测试配置)
9. [部署配置](#部署配置)

---

## 项目初始化

### 1.1 使用 Vite 创建项目

```bash
# 创建 Vue 3 + TypeScript 项目
npm create vite@latest ai-postgen-vue -- --template vue-ts

cd ai-postgen-vue

# 安装依赖
npm install
```

### 1.2 项目结构初始化

```bash
# 创建目录结构
mkdir -p src/{views,components,composables,stores,services,router,utils,types,constants,styles,assets,plugins}
mkdir -p src/components/{common,editor,project,layout}
mkdir -p src/services/{api,business,storage}
mkdir -p src/types/models
```

---

## 依赖配置

### 2.1 package.json

```json
{
  "name": "ai-postgen-vue",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "type-check": "vue-tsc --noEmit",
    "lint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix",
    "format": "prettier --write src/",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "coverage": "vitest --coverage"
  },
  "dependencies": {
    "vue": "^3.4.0",
    "vue-router": "^4.2.5",
    "pinia": "^2.1.7",
    "pinia-plugin-persistedstate": "^3.2.1",

    "@tanstack/vue-query": "^5.17.0",
    "axios": "^1.6.5",

    "element-plus": "^2.5.0",
    "@element-plus/icons-vue": "^2.3.1",

    "pixi.js": "^7.3.3",

    "date-fns": "^3.0.6",
    "lodash-es": "^4.17.21",
    "@vueuse/core": "^10.7.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^5.0.0",
    "vite": "^5.0.0",

    "typescript": "^5.3.0",
    "vue-tsc": "^1.8.27",

    "@types/node": "^20.10.0",
    "@types/lodash-es": "^4.17.12",

    "eslint": "^8.56.0",
    "@vue/eslint-config-typescript": "^12.0.0",
    "@vue/eslint-config-prettier": "^9.0.0",
    "eslint-plugin-vue": "^9.19.0",

    "prettier": "^3.1.0",

    "tailwindcss": "^3.4.0",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",

    "@iconify/vue": "^4.1.1",
    "unplugin-auto-import": "^0.17.3",
    "unplugin-vue-components": "^0.26.0",
    "unplugin-icons": "^0.18.0",

    "vitest": "^1.0.0",
    "@vue/test-utils": "^2.4.0",
    "@vitest/ui": "^1.0.0",
    "@vitest/coverage-v8": "^1.0.0",
    "happy-dom": "^12.10.0",

    "@playwright/test": "^1.40.0"
  }
}
```

### 2.2 安装核心依赖

```bash
# 核心框架
npm install vue@latest vue-router@latest pinia@latest

# 状态持久化
npm install pinia-plugin-persistedstate

# API 层
npm install axios @tanstack/vue-query

# UI 组件库（选择一个）
npm install element-plus @element-plus/icons-vue

# Canvas 渲染
npm install pixi.js

# 工具库
npm install date-fns lodash-es @vueuse/core

# TypeScript 类型
npm install -D @types/node @types/lodash-es
```

### 2.3 安装开发依赖

```bash
# Vite 和 Vue 插件
npm install -D @vitejs/plugin-vue vite

# TypeScript
npm install -D typescript vue-tsc

# ESLint 和 Prettier
npm install -D eslint prettier
npm install -D @vue/eslint-config-typescript @vue/eslint-config-prettier
npm install -D eslint-plugin-vue

# Tailwind CSS
npm install -D tailwindcss autoprefixer postcss

# 自动导入插件
npm install -D unplugin-auto-import unplugin-vue-components unplugin-icons

# 测试工具
npm install -D vitest @vue/test-utils @vitest/ui @vitest/coverage-v8 happy-dom

# E2E 测试
npm install -D @playwright/test
```

---

## Vite 配置

### 3.1 vite.config.ts

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'

export default defineConfig({
  plugins: [
    vue(),

    // 自动导入 Vue API
    AutoImport({
      imports: ['vue', 'vue-router', 'pinia', '@vueuse/core'],
      resolvers: [
        ElementPlusResolver(),
        IconsResolver({
          prefix: 'Icon'
        })
      ],
      dts: 'src/auto-imports.d.ts',
      eslintrc: {
        enabled: true
      }
    }),

    // 自动导入组件
    Components({
      resolvers: [
        ElementPlusResolver(),
        IconsResolver({
          enabledCollections: ['carbon', 'mdi']
        })
      ],
      dts: 'src/components.d.ts'
    }),

    // 图标插件
    Icons({
      autoInstall: true
    })
  ],

  resolve: {
    alias: {
      '@': resolve(__dirname, 'src')
    }
  },

  server: {
    port: 5173,
    host: true,
    open: true,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  },

  build: {
    target: 'esnext',
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    },
    rollupOptions: {
      output: {
        manualChunks: {
          'vue-vendor': ['vue', 'vue-router', 'pinia'],
          'ui-vendor': ['element-plus'],
          'utils': ['axios', '@tanstack/vue-query', 'date-fns', 'lodash-es'],
          'canvas': ['pixi.js']
        }
      }
    },
    chunkSizeWarningLimit: 1000
  },

  optimizeDeps: {
    include: [
      'vue',
      'vue-router',
      'pinia',
      'axios',
      'element-plus',
      'pixi.js',
      '@vueuse/core'
    ]
  }
})
```

---

## TypeScript 配置

### 4.1 tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ESNext", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Path Mapping */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },

    /* Vue */
    "types": ["vite/client", "@vue/runtime-core", "@element-plus/icons-vue"]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue"
  ],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 4.2 tsconfig.node.json

```json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts"]
}
```

### 4.3 env.d.ts

```typescript
/// <reference types="vite/client" />

declare module '*.vue' {
  import type { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}

interface ImportMetaEnv {
  readonly VITE_API_BASE_URL: string
  readonly VITE_APP_TITLE: string
  readonly VITE_UPLOAD_MAX_SIZE: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

---

## ESLint 和 Prettier

### 5.1 .eslintrc.cjs

```javascript
module.exports = {
  root: true,
  env: {
    node: true,
    browser: true,
    es2022: true
  },
  extends: [
    'plugin:vue/vue3-recommended',
    'eslint:recommended',
    '@vue/typescript/recommended',
    '@vue/prettier'
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-explicit-any': 'warn',
    '@typescript-eslint/no-unused-vars': ['warn', { argsIgnorePattern: '^_' }]
  }
}
```

### 5.2 .prettierrc.json

```json
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "none",
  "arrowParens": "always",
  "endOfLine": "auto",
  "vueIndentScriptAndStyle": false
}
```

### 5.3 .eslintignore

```
node_modules
dist
dist-ssr
*.local
.env
.env.*
!.env.example
```

---

## 环境变量

### 6.1 .env.development

```env
# 开发环境配置
VITE_API_BASE_URL=http://localhost:8000/api/v1
VITE_APP_TITLE=智绘海报 - 开发环境
VITE_UPLOAD_MAX_SIZE=20971520
```

### 6.2 .env.production

```env
# 生产环境配置
VITE_API_BASE_URL=https://api.postgen.ai/api/v1
VITE_APP_TITLE=智绘海报
VITE_UPLOAD_MAX_SIZE=20971520
```

### 6.3 .env.example

```env
# API 基础 URL
VITE_API_BASE_URL=http://localhost:8000/api/v1

# 应用标题
VITE_APP_TITLE=智绘海报

# 上传文件大小限制（字节）
VITE_UPLOAD_MAX_SIZE=20971520
```

---

## 开发脚本

### 7.1 package.json scripts 详解

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "build:dev": "vue-tsc && vite build --mode development",
    "build:prod": "vue-tsc && vite build --mode production",
    "preview": "vite preview",

    "type-check": "vue-tsc --noEmit",

    "lint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix",
    "lint:check": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts",

    "format": "prettier --write src/",
    "format:check": "prettier --check src/",

    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:run": "vitest run",
    "coverage": "vitest --coverage",

    "e2e": "playwright test",
    "e2e:ui": "playwright test --ui",

    "prepare": "husky install"
  }
}
```

### 7.2 常用命令

```bash
# 开发服务器
npm run dev

# 类型检查
npm run type-check

# 代码检查和格式化
npm run lint
npm run format

# 构建
npm run build

# 预览构建结果
npm run preview

# 运行测试
npm run test
npm run test:ui
npm run coverage

# E2E 测试
npm run e2e
npm run e2e:ui
```

---

## 测试配置

### 8.1 vitest.config.ts

```typescript
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export default defineConfig({
  plugins: [vue()],
  test: {
    globals: true,
    environment: 'happy-dom',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'dist/',
        'src/main.ts',
        '**/*.d.ts',
        '**/*.config.*',
        '**/mockData/**'
      ]
    },
    include: ['src/**/*.{test,spec}.{js,ts,jsx,tsx}'],
    exclude: ['node_modules', 'dist', '.idea', '.git', '.cache']
  },
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src')
    }
  }
})
```

### 8.2 测试示例

#### src/components/common/Button/__tests__/Button.spec.ts

```typescript
import { describe, it, expect, vi } from 'vitest'
import { mount } from '@vue/test-utils'
import Button from '../Button.vue'

describe('Button Component', () => {
  it('renders button text', () => {
    const wrapper = mount(Button, {
      slots: {
        default: 'Click me'
      }
    })
    expect(wrapper.text()).toContain('Click me')
  })

  it('emits click event', async () => {
    const wrapper = mount(Button)
    await wrapper.trigger('click')
    expect(wrapper.emitted('click')).toBeTruthy()
  })

  it('is disabled when disabled prop is true', () => {
    const wrapper = mount(Button, {
      props: { disabled: true }
    })
    expect(wrapper.attributes('disabled')).toBeDefined()
  })

  it('shows loading spinner when loading', () => {
    const wrapper = mount(Button, {
      props: { loading: true }
    })
    expect(wrapper.find('.btn-spinner').exists()).toBe(true)
  })
})
```

### 8.3 playwright.config.ts (E2E 测试)

```typescript
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:5173',
    trace: 'on-first-retry'
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] }
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] }
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] }
    }
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI
  }
})
```

---

## 部署配置

### 9.1 Dockerfile

```dockerfile
# 构建阶段
FROM node:20-alpine AS builder

WORKDIR /app

# 复制依赖文件
COPY package*.json ./

# 安装依赖
RUN npm ci

# 复制源代码
COPY . .

# 构建应用
RUN npm run build

# 生产阶段
FROM nginx:alpine

# 复制构建产物
COPY --from=builder /app/dist /usr/share/nginx/html

# 复制 nginx 配置
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 9.2 nginx.conf

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    # Gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # SPA 路由支持
    location / {
        try_files $uri $uri/ /index.html;
    }

    # API 代理（生产环境可选）
    location /api/ {
        proxy_pass http://backend-api:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # 缓存静态资源
    location ~* \.(js|css|png|jpg|jpeg|gif|svg|ico|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### 9.3 docker-compose.yml

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80:80"
    environment:
      - NODE_ENV=production
    depends_on:
      - backend

  backend:
    image: ai-postgen-backend:latest
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/postgen
```

### 9.4 .dockerignore

```
node_modules
dist
.git
.env
.env.*
!.env.example
*.log
coverage
.vscode
.idea
```

---

## Tailwind CSS 配置

### 10.1 tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        }
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
    },
  },
  plugins: [],
}
```

### 10.2 postcss.config.js

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### 10.3 src/styles/main.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  * {
    @apply box-border;
  }

  html {
    @apply h-full;
  }

  body {
    @apply h-full font-sans antialiased;
  }

  #app {
    @apply h-full;
  }
}

@layer components {
  .btn {
    @apply inline-flex items-center justify-center
           rounded-lg font-medium transition-colors
           focus:outline-none focus:ring-2 focus:ring-offset-2
           disabled:opacity-50 disabled:cursor-not-allowed;
  }
}
```

---

**配置完成！**

这份文档涵盖了：
- ✅ 项目初始化和依赖安装
- ✅ Vite 完整配置
- ✅ TypeScript 配置
- ✅ ESLint 和 Prettier
- ✅ 环境变量管理
- ✅ 开发脚本
- ✅ 测试配置（Vitest + Playwright）
- ✅ 部署配置（Docker + Nginx）
- ✅ Tailwind CSS 配置

使用这些配置文件可以快速启动 Vue 3 项目开发。
