# 博客图片管理方案

## 目录结构

博客文章图片按以下结构组织：

```
src/assets/blog/
├── YYYY/                    # 年份目录
│   └── MM-slug/            # 月份-文章slug目录
│       ├── cover.jpg       # 文章封面图
│       ├── diagram.png     # 文章内嵌图片
│       └── screenshot.jpg  # 其他相关图片
└── README.md              # 本文档
```

**命名规则**：

- `YYYY`: 4位年份，如 `2025`
- `MM`: 2位月份，如 `03`
- `slug`: 文章slug（与 `src/data/blog/` 中的文件名对应）

**示例**：

- 文章：`src/data/blog/2025/03-my-article.md`
- 图片目录：`src/assets/blog/2025/03-my-article/`
- 图片文件：`cover.jpg`, `screenshot.png` 等

## 引用方式

### 1. Markdown正文中引用

使用 `@/assets/blog/` 别名：

```markdown
![文章封面图](@/assets/blog/2025/03-my-article/cover.jpg)

![架构示意图](@/assets/blog/2025/03-my-article/diagram.png)
```

### 2. Frontmatter中引用（ogImage）

使用相对路径（相对于Markdown文件）：

```yaml
---
title: 我的文章
ogImage: ../../assets/blog/2025/03-my-article/cover.jpg
---
```

**路径计算**：

- Markdown文件：`src/data/blog/2025/03-my-article.md`
- 图片文件：`src/assets/blog/2025/03-my-article/cover.jpg`
- 相对路径：`../../assets/blog/2025/03-my-article/cover.jpg`

## 优势

1. **自动优化**：Astro会自动压缩、转换格式（WebP）、懒加载
2. **类型安全**：TypeScript支持 `@/` 别名
3. **逻辑清晰**：图片与文章关联紧密，便于管理
4. **渐进迁移**：现有 `@/assets/images/` 和外部URL不受影响

## 迁移现有图片

如需迁移旧图片：

```bash
# 1. 移动图片文件
mv src/assets/images/old-image.png \
   src/assets/blog/2020/older-and-older/old-image.png

# 2. 更新Markdown引用
# 将 @/assets/images/old-image.png
# 改为 @/assets/blog/2020/older-and-older/old-image.png
```

## SVG封面图片的特殊处理

对于文章封面图（ogImage），如果使用SVG格式，需要放在 `public/` 目录而非 `src/assets/` 目录：

### 目录结构

```
public/
└── assets/blog/
    └── YYYY/
        └── MM-slug/
            └── cover.svg
```

### 引用方式

```yaml
---
ogImage: /assets/blog/2025/03-my-article/cover.svg
---
```

### 原因

1. Astro的 `image()` 优化器不支持SVG格式
2. `ogImage` 需要绝对路径（以 `/` 开头）
3. 静态SVG文件无需优化，直接服务即可

### 文章内嵌图片

文章正文中的SVG图片仍可使用 `@/assets/blog/` 别名，但建议转换为PNG格式以获得更好的兼容性。

## 注意事项

1. 新文章使用新方案，旧文章可逐步迁移
2. 图片文件名建议使用英文、小写、连字符
3. 大图片建议提前压缩（保持原图质量）
4. 封面图建议使用SVG（矢量）或PNG（位图）格式

---

**Linus风格总结**：
"别想太多，直接按年月-slug放图片。新文章用新方案，旧文章让它继续工作。简单、清晰、不破坏现有功能——这就是好代码。"
