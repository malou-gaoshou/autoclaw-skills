# HTML 样式规范

公众号文章 HTML 排版标准。

---

## 基础样式

### 前言区块
```html
<section style="background:#f7f7f7;border-left:4px solid #ff6b35;padding:20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#ff6b35;">前言</p>
  <p style="margin:0;line-height:1.8;color:#666;">内容</p>
</section>
```

### 一级标题
```html
<section style="margin:40px 0 20px 0;">
  <p style="font-size:20px;font-weight:bold;color:#333;margin:0;border-left:4px solid #ff6b35;padding-left:15px;">标题</p>
</section>
```

### 二级标题
```html
<section style="margin:30px 0 15px 0;">
  <p style="font-size:18px;font-weight:bold;color:#333;margin:0;">标题</p>
</section>
```

### 段落
```html
<p style="margin:15px 0;line-height:1.8;color:#333;">内容</p>
```

### 列表项
```html
<p style="margin:10px 0;line-height:1.8;color:#333;padding-left:20px;text-indent:-15px;">• 内容</p>
```

### 强调
```html
<strong>强调内容</strong>
```

### 图片
```html
<section style="text-align:center;margin:30px 0;">
  <img src="{图片URL}" style="max-width:100%;height:auto;display:block;margin:0 auto;" alt="描述">
</section>
```

### 引用区块
```html
<section style="background:#fffbe6;border-left:4px solid #faad14;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0;line-height:1.8;color:#666;">引用内容</p>
</section>
```

### 提示区块
```html
<section style="background:#e6f7ff;border-left:4px solid #1890ff;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#1890ff;">💡 提示</p>
  <p style="margin:0;line-height:1.8;color:#666;">提示内容</p>
</section>
```

### 警告区块
```html
<section style="background:#fff2f0;border-left:4px solid #ff4d4f;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#ff4d4f;">⚠️ 注意</p>
  <p style="margin:0;line-height:1.8;color:#666;">警告内容</p>
</section>
```

### 成功区块
```html
<section style="background:#f6ffed;border-left:4px solid #52c41a;padding:15px 20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#52c41a;">✅ 要点</p>
  <p style="margin:0;line-height:1.8;color:#666;">成功内容</p>
</section>
```

### 结尾互动区块
```html
<section style="background:#f7f7f7;border-left:4px solid #ff6b35;padding:20px;margin:30px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#ff6b35;">如果这篇文章对你有启发</p>
  <p style="margin:0;line-height:1.8;color:#666;">欢迎点赞、在看、转发</p>
  <p style="margin:10px 0 0 0;line-height:1.8;color:#666;">{互动问题}欢迎在评论区聊聊。</p>
</section>
```

### 标签
```html
<p style="margin:15px 0;line-height:1.8;color:#333;"><strong>#{标签1} #{标签2} #{标签3}</strong></p>
```

---

## 颜色方案

### 主题色
- 主色：`#ff6b35`（橙色）
- 辅助色：`#1890ff`（蓝色）

### 背景色
- 浅灰：`#f7f7f7`
- 浅蓝：`#e6f7ff`
- 浅绿：`#f6ffed`
- 浅黄：`#fffbe6`
- 浅红：`#fff2f0`

### 文字色
- 主文字：`#333333`
- 次要文字：`#666666`
- 强调文字：使用 `<strong>` 或对应区块的主题色

---

## 注意事项

1. **内联样式**：公众号不支持外部 CSS，所有样式必须内联
2. **行高**：统一使用 `line-height:1.8`
3. **边距**：段落间 `margin:15px 0`，章节间 `margin:30px 0`
4. **图片**：必须使用 `max-width:100%` 确保移动端适配
5. **字体**：不指定字体，使用系统默认
6. **emoji**：可以使用，但不要过度

---

## 完整示例

```html
<section style="background:#f7f7f7;border-left:4px solid #ff6b35;padding:20px;margin:20px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#ff6b35;">前言</p>
  <p style="margin:0;line-height:1.8;color:#666;">2026年，"一人公司"（OPC）突然火了。</p>
  <p style="margin:10px 0 0 0;line-height:1.8;color:#666;">看起来，这是AI时代给普通人的最大红利。</p>
</section>

<section style="text-align:center;margin:30px 0;">
  <img src="封面图URL" style="max-width:100%;height:auto;display:block;margin:0 auto;" alt="封面图">
</section>

<section style="margin:40px 0 20px 0;">
  <p style="font-size:20px;font-weight:bold;color:#333;margin:0;border-left:4px solid #ff6b35;padding-left:15px;">一、OPC的"高光时刻"</p>
</section>

<p style="margin:15px 0;line-height:1.8;color:#333;">过去一个月，我密集调研了杭州、深圳等地的OPC社区。</p>

<section style="margin:30px 0 15px 0;">
  <p style="font-size:18px;font-weight:bold;color:#333;margin:0;">✅ 1. 成本降到历史最低</p>
</section>

<p style="margin:10px 0;line-height:1.8;color:#333;padding-left:20px;text-indent:-15px;">• AI写代码、做设计、剪视频</p>
<p style="margin:10px 0;line-height:1.8;color:#333;padding-left:20px;text-indent:-15px;">• 年运营成本可以压到<strong>5000元以内</strong></p>

<section style="background:#f7f7f7;border-left:4px solid #ff6b35;padding:20px;margin:30px 0;border-radius:0 8px 8px 0;">
  <p style="margin:0 0 10px 0;font-weight:bold;font-size:16px;color:#ff6b35;">如果这篇文章对你有启发</p>
  <p style="margin:0;line-height:1.8;color:#666;">欢迎点赞、在看、转发</p>
</section>

<p style="margin:15px 0;line-height:1.8;color:#333;"><strong>#一人公司 #AI创业 #副业</strong></p>
```
