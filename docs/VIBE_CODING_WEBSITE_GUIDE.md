# Vibe Coding 风格网站落地指南（全屏滚动 / 视差 / GSAP 级动效 / 自定义代码）

> 目标：做一个“视觉冲击强 + 交互细腻 + 可维护”的单页官网。

## 1. 先定技术方案（建议）

如果你希望同时兼顾效率和动效上限，推荐：

- 框架：`Next.js`（或 `Vite + React`）
- 动效：`GSAP + ScrollTrigger`
- 3D（可选）：`@react-three/fiber + drei`
- 样式：`Tailwind CSS`（便于快速搭视觉）
- 平滑滚动（可选）：`Lenis`（与 GSAP 时间轴联动）

## 2. 从 0 到 1 的执行步骤

### Step A：初始化项目

```bash
npm create vite@latest vibe-site -- --template react-ts
cd vibe-site
npm i gsap @studio-freight/lenis clsx
npm i -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Step B：建立页面结构

按“电影分镜”做分段，每段一个全屏 section：

1. Hero（品牌主叙事）
2. Feature（能力展示）
3. Showcase（案例/作品）
4. CTA（转化）

建议使用：

- 每个 section `min-h-screen`
- z-index 分层管理（背景层 / 内容层 / 特效层）
- 图片和视频做懒加载

### Step C：全屏滚动 + 视差

核心做法：

- 用 `ScrollTrigger` 把 section 动画与滚动绑定
- 用 `scrub: true` 让动画按滚动进度推进
- 背景与前景使用不同位移速度做视差

示例（简化版）：

```ts
useEffect(() => {
  const tl = gsap.timeline({
    scrollTrigger: {
      trigger: '.hero',
      start: 'top top',
      end: '+=200%',
      scrub: true,
      pin: true,
    },
  })

  tl.to('.hero-bg', { yPercent: 20, scale: 1.1 }, 0)
    .to('.hero-title', { yPercent: -30, opacity: 0.2 }, 0)
    .to('.hero-cta', { y: -80, opacity: 1 }, 0.3)
}, [])
```

### Step D：GSAP 级动效编排思路

1. **先做状态机**：每个 section 定义进入/停留/离开三态。
2. **再做时间轴**：把“镜头语言”拆成多个 tween。
3. **最后调节节奏**：统一 easing、持续时间、延迟关系。

常用参数建议：

- `ease: "power3.out"`（大多数过渡）
- `duration: 0.6 ~ 1.4`
- `stagger: 0.03 ~ 0.12`

### Step E：自定义代码（关键）

你要避免“模板味”，重点写这三层自定义：

1. **品牌视觉变量**：色彩、字体、网格、间距体系。
2. **交互语言**：悬停反馈、滚动过渡、进入离开一致性。
3. **内容节奏**：文案长度、镜头切换点、CTA 出现时机。

## 3. 项目结构建议（前端）

```text
src/
├─ app/
├─ components/
│  ├─ sections/
│  │  ├─ Hero.tsx
│  │  ├─ Feature.tsx
│  │  ├─ Showcase.tsx
│  │  └─ Cta.tsx
│  └─ motion/
│     ├─ useHeroTimeline.ts
│     ├─ useParallax.ts
│     └─ useReveal.ts
├─ styles/
├─ assets/
└─ lib/
   └─ gsap.ts
```

## 4. 性能与体验底线（非常重要）

1. 图片：webp/avif + 多尺寸 + 懒加载。
2. 动画：避免大面积 box-shadow / filter 动态变化。
3. 只动 `transform` 与 `opacity`。
4. 首屏控制：JS 包体尽量小，首屏动画延后 100~300ms 启动。
5. 无障碍：支持 `prefers-reduced-motion`。

## 5. 上线前检查清单

- [ ] Chrome / Safari / iOS 真机滚动体验通过
- [ ] 低性能设备不掉帧（至少主流程可用）
- [ ] Lighthouse 性能、可访问性达标
- [ ] 所有关键 CTA 可见且可点击
- [ ] 页面在禁用 JS 情况下仍能展示基础内容

## 6. 你可以直接这样开工（最短路径）

1. 先出 Figma 分镜（4 屏足够）。
2. 先实现无动效静态版页面。
3. 再给每屏加 1 个主动画 + 1 个次动画。
4. 最后统一调节节奏、光效、文案。

---

如果你需要，我可以下一步直接给你：

- 一份可运行的 `React + GSAP + ScrollTrigger` 最小模板代码；
- 或者一份 `Next.js + Tailwind + GSAP` 的生产可用目录与脚手架命令。
