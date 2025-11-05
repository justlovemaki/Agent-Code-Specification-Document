全栈开发规范文档 (Next.js 15 技术栈)
0. 引言

本规范旨在统一团队在现代化 Next.js 项目中的开发实践，确保代码的高质量、高性能、可维护性和安全性。所有团队成员，包括代码生成 Agent，都应严格遵守此规范。规范涵盖了从项目结构、代码编写、数据交互到安全认证的全过程。

1. 项目结构规范 (Project Structure Specification)

清晰的项目结构是高效协作的基石。

【强制】 遵循 Next.js App Router 目录结构，所有页面和路由必须在 app/ 目录下创建。

【强制】 顶级目录结构必须清晰划分职责：

app/: 存放所有应用路由、页面、布局和 API 路由。

app/api/: 存放所有后端 API 路由处理器 (Route Handlers)。

app/[locale]/: 国际化的根目录，[locale] 为动态语言路由。

components/: 存放全局可复用的 React 组件。

components/ui/: 存放基础 UI 原子组件 (如 Button, Input)，与 shadcn/ui 类似。

components/shared/: 存放由 UI 组件构成的复杂共享组件 (如 PageHeader, UserProfile)。

components/features/: 存放特定业务功能的组件集合 (如 auth-form, chat-panel)。

lib/: 存放辅助函数、工具类和核心逻辑。

lib/db/: 存放 Drizzle ORM 的 schema 定义、数据库连接和迁移配置。

lib/auth/: 存放 better-auth 的配置文件和会话管理逻辑。

lib/llm/: 存放 LangChain 的客户端实例、链、Prompt 模板等。

lib/utils.ts: 存放通用的辅助函数 (如 cn 函数)。

lib/validators/: 存放 Zod 或其他验证库的 schema 定义。

hooks/: 存放自定义 React Hooks。

styles/: 存放全局 CSS 文件 (globals.css)。

messages/: 存放国际化 (i18n) 的语言包文件 (如 en.json, zh.json)。

public/: 存放静态资源，如图片、字体等。

prisma/ or drizzle/: 存放 Drizzle Kit 生成的迁移文件。

2. 代码开发规范 (Code Development Specification)

本规范关注组件设计、状态管理、数据获取和 TypeScript 的最佳实践。

2.1. 组件设计与数据获取

【强制】 默认使用 React Server Components (RSC)。只有在需要用户交互、状态管理 (useState) 或浏览器端 API (useEffect) 时，才在文件顶部添加 "use client" 声明，将其转换为 Client Component。

【强制】 数据获取 (Fetching) 必须优先在 Server Components 中完成。利用 async/await 直接在组件中获取数据，这能充分利用 Next.js 的服务端渲染和缓存能力。

【强制】 数据变更 (Mutations) 必须使用 Server Actions。为表单提交和数据修改等操作创建 Server Action，避免为此创建不必要的 API 路由。

【强制】 组件命名必须使用帕斯卡命名法 (PascalCase)，文件名与组件名保持一致 (如 UserProfile.tsx)。自定义 Hooks 必须以 use 开头 (如 useUserSession.ts)。

【推荐】 使用 Next.js 15 的 useActionState Hook 处理 Server Action 的 pending、error 和 data 状态，提升表单交互体验。

【推荐】 对可能存在延迟的数据变更操作，使用 Next.js 15 的 useOptimistic Hook 来实现乐观更新，提升用户感知性能。

【参考】 复杂的客户端状态管理，推荐使用 Zustand，它轻量且对 Server Components 友好。避免仅为简单场景引入大型状态管理库。

2.2. TypeScript 规范

【强制】 启用 tsconfig.json 中的 strict 模式。

【强制】 组件的 props 必须定义明确的 type 或 interface。

【强制】 所有 API 响应、数据库模型（Drizzle schema 会自动生成类型）和核心业务逻辑都必须有严格的类型定义。

【强制】 环境变量必须通过 process.env 访问，并建议使用 Zod 或类似工具进行环境变量的类型校验，确保启动时环境配置的正确性。

3. 数据库与 ORM 规范 (Drizzle ORM)

本规范确保数据库操作的类型安全和可维护性。

【强制】 数据库 schema 必须在 lib/db/schema.ts 文件中统一定义，使用 Drizzle ORM 的 TypeScript 语法。

【强制】 表名和字段名必须使用下划线命名法 (snake_case)。

正例: users, post_title

【强制】 所有数据库变更必须通过 Drizzle Kit 生成迁移文件并执行，禁止手动修改生产数据库结构。迁移文件应纳入版本控制。

命令: npx drizzle-kit generate:pg (以 PostgreSQL 为例)

【强制】 数据库连接凭证必须通过环境变量加载，严禁硬编码在代码中。

【推荐】 将复杂的数据库查询逻辑封装在 lib/queries 或 lib/services 目录下的函数中，保持 Server Components 的简洁性。

4. 样式与 UI 规范 (Tailwind CSS & react-icons)

本规范旨在确保 UI 的一致性和性能。

【强制】 严格遵循 Utility-First 原则。优先使用 Tailwind 的原子类，避免编写自定义 CSS。

【强制】 项目的颜色、字体、间距等设计规范必须在 tailwind.config.ts 的 theme 对象中统一定义。禁止在类名中使用魔术数字 (magic numbers)。

反例: className="top-[13px]"

正例: 在 theme 中定义 spacing: { '3.25': '13px' }，然后使用 className="top-3.25"

【强制】 使用 clsx 或 tailwind-merge 库来处理条件性或合并的 classNames，确保逻辑清晰且无样式冲突。

【强制】 所有图标必须从 react-icons 库中按需导入，以利用 tree-shaking 减小打包体积。

正例: import { FaBeer } from "react-icons/fa";

反例: import { ... } from "react-icons";

【推荐】 避免使用 @apply。只有在处理第三方库样式或极少数可复用的组件级样式时才酌情在 globals.css 中使用。

5. 安全与认证规范 (better-auth)

本规范确保应用的认证流程安全可靠。

【强制】 所有敏感的认证密钥 (如 AUTH_SECRET, GITHUB_ID) 必须存储在 .env.local 文件中，并加入 .gitignore。

【强制】 必须使用 middleware.ts 来保护需要登录才能访问的路由。这是保护整个路由段的最有效方式。

【强制】 RBAC (Role-Based Access Control) 必须在服务端实现。可以在 Server Components、Server Actions 或 API 路由中获取用户会话和角色，进行权限校验。严禁仅在客户端进行权限判断。

【强制】 获取用户会话信息时，在服务端组件或 Server Actions 中使用 better-auth 提供的服务端方法，在客户端组件中使用其提供的 Hook (如 useSession)。

6. AI (LangChain) 集成规范

本规范确保 LLM 集成的高效、安全和可维护性。

【强制】 LLM 提供商的 API 密钥 (如 OPENAI_API_KEY) 必须存储在 .env.local 中，并且只能在服务端代码（Server Components, Server Actions, API Routes）中访问。

【强制】 创建一个集中的 LLM 客户端实例（如 lib/llm/client.ts），并利用 Next.js 的缓存机制或 React.cache 避免重复实例化。

【强制】 复杂的、可复用的 prompts 必须使用 PromptTemplate 进行管理，存放于 lib/llm/prompts.ts。这使得 prompts 的版本控制和维护变得容易。

【推荐】 对于流式响应（如聊天机器人），必须使用 StreamingTextResponse 或 Vercel AI SDK 提供的工具，将 LLM 的响应以流的形式直接返回给客户端，以获得最佳用户体验。

【推荐】 将复杂的 AI 逻辑（如多步骤链、RAG）封装在 LangChain Expression Language (LCEL) 链中，并将其定义在 lib/llm/chains.ts 中，使业务逻辑与 AI 逻辑解耦。

7. 国际化 (i18n) 规范

本规范确保应用支持多语言。

【强制】 使用基于路径的国际化路由 (e.g., /en/about, /zh/about)。推荐使用 next-intl 库来简化实现。

【强制】 所有面向用户的文本必须使用 i18n key，并存储在 messages/{locale}.json 文件中。禁止在代码中硬编码任何 UI 文本。

【强制】 i18n key 的命名应具有层级结构和语义，以便于查找和管理。

正例: "page.home.hero.title": "Welcome!"

【推荐】 在 middleware.ts 中处理默认语言重定向和语言检测逻辑。

8. SEO 与构建规范 (next-sitemap)

本规范确保应用对搜索引擎友好。

【强制】 必须使用 Next.js 内置的 Metadata API (generateMetadata 函数) 来动态生成每个页面的 title, description 等元数据。

【强制】 配置 next-sitemap.js 文件，并在构建脚本 (package.json 的 postbuild 命令) 中自动生成 sitemap.xml 和 robots.txt。

【强制】 网站的基础 URL 必须通过环境变量 (NEXT_PUBLIC_SITE_URL) 进行配置。

9. 文档与注释规范 (Documentation & Commenting)

本规范旨在提高代码的可读性和可维护性。

【强制】 可复用的组件、自定义 Hooks 和核心工具函数必须使用 JSDoc 格式进行注释，说明其功能、参数 (@param) 和返回值 (@returns)。

【推荐】 复杂的业务逻辑、算法或非直观的代码块上方应添加 // 注释，解释其“为什么”这么做，而不是“做什么”。

【参考】 使用 // TODO: 标记待办事项，// FIXME: 标记已知问题或需要修复的代码。