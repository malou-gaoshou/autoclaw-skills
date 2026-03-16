# WeChat Article Publisher Skill

## 描述
公众号内容自动化创作工作流。支持从主题询问、热点搜索、文章生成、配图创建到草稿上传的完整流程。

## 触发场景
- 用户提到"公众号创作"、"写公众号文章"、"发布到公众号"
- 需要自动化公众号内容生产流程
- 定时任务触发内容创作

## 流程概览
```
定时触发 → 询问主题 → 搜索热点 → 生成文章 → 生成配图 → 上传草稿箱
```

---

## 配置步骤

### 1. 获取公众号 AppID 和 AppSecret

#### 步骤1：登录微信公众平台
访问：https://mp.weixin.qq.com

使用公众号管理员微信扫码登录。

#### 步骤2：进入基本配置
登录后，点击左侧菜单：**开发 → 基本配置**

#### 步骤3：获取 AppID
在「公众号开发信息」区域，直接可以看到 **AppID(应用ID)**，复制保存。

#### 步骤4：获取 AppSecret
在「公众号开发信息」区域：
1. 点击 **AppSecret(应用密钥)** 旁边的「重置」或「查看」按钮
2. 需要管理员扫码验证
3. 验证通过后，会显示 AppSecret（**注意：只显示一次，务必立即复制保存**）

> ⚠️ **重要提示**：AppSecret 只在生成时显示一次，如果忘记了需要重置，重置后旧的 Secret 会立即失效。

#### 步骤5：配置 IP 白名单
在「公众号开发信息」区域：
1. 点击 **IP白名单** 旁边的「修改」按钮
2. 添加你的服务器 IP 地址
3. 保存后等待生效（通常立即生效）

> 💡 **如何查看你的 IP**：访问 https://www.ip.cn 或在服务器上运行 `curl ifconfig.me`

---

### 2. 创建配置文件
在 workspace 根目录创建 `wechat_config.json`：

```json
{
  "wechat": {
    "appid": "你的公众号AppID",
    "appsecret": "你的公众号AppSecret"
  },
  "ai": {
    "article_model": "glm-5",
    "image_provider": "doubao",
    "image_model": "doubao-seedream-4-0-250828",
    "image_api_url": "https://ark.cn-beijing.volces.com/api/v3",
    "image_api_key": "你的图片生成API密钥",
    "image_size": "1920x1080"
  },
  "paths": {
    "articles": "articles/",
    "images": "images/",
    "logs": "logs/"
  }
}
```

### 2. 配置 IP 白名单
登录公众号后台，将服务器 IP 添加到白名单：
- 路径：开发 → 基本配置 → IP白名单

### 3. 创建工作目录

```
workspace/
├── articles/                    # 文章存储
│   └── 2026-03-16-{主题}/      # 按日期+主题分文件夹
│       ├── article.md          # 文章内容
│       ├── images/             # 配图
│       └── images.json         # 配图信息
├── logs/                       # 日志文件
└── wechat_config.json          # 配置文件
```

---

## 详细工作流程

### 步骤1：定时触发（可选）

**Windows 任务计划程序配置**：
```
触发器: 每天 09:00
操作: 发送消息给 Agent 询问今日主题
```

或通过 OpenClaw cron 设置定时任务。

---

### 步骤2：询问主题

**消息模板**：
```
🌅 早安！今天是 {日期} {星期}

📚 准备开始今天的内容创作

请告诉我今天想要创作什么主题？
（例如：一人公司、AI工具、职场成长、创业故事等）

💡 如果今天不需要创作，请直接回复"跳过"或"不需要"
```

**等待逻辑**：
- 超时时间：2小时
- 回复"跳过"/"不需要"/"休息"：结束流程
- 用户未回复：超时后自动结束
- 用户回复主题：继续下一步

---

### 步骤3：搜索热点信息

**搜索工具**：AutoGLM WebSearch（智能网络搜索）

**搜索脚本位置**：
```
~/.openclaw-autoclaw/skills/autoglm-websearch/websearch.py
```

**搜索策略**：分平台搜索，每次搜索一个平台

**搜索命令**：
```bash
python websearch.py "{主题} 抖音 热门 最新"
python websearch.py "{主题} 小红书 热门 笔记"
python websearch.py "{主题} 公众号 文章 观点"
```

**搜索顺序**：
1. 抖音（短视频热点）
2. 小红书（生活方式/观点）
3. 公众号（深度分析）

**提取信息**：
- 标题（name）
- 摘要内容（snippet）
- 来源链接（url）

**输出格式**：
```json
{
  "douyin": [
    {"title": "标题1", "snippet": "摘要", "url": "链接"}
  ],
  "xiaohongshu": [...],
  "weixin": [...]
}
```

**失败处理**：
1. 重试3次
2. 简化搜索关键词
3. 通知用户手动提供素材

---

### 步骤4：生成文章

**生成工具**：blog-writer skill

**核心要求**：
1. **结合热点素材**：必须基于步骤3搜索到的热点文章来写，模仿爆款结构和观点
2. **公众号风格**：口语化、有情绪、有互动、易读
3. **合规用语**：严禁违反平台规定的内容（敏感词、夸大宣传、政治敏感等）

**调用方式**：
```
触发 blog-writer skill，提供：
- 主题
- 热点文章素材（标题+摘要+链接）
- 目标平台：微信公众号
- 字数要求：3000-5000字
```

**文章结构**（公众号优化版）：
```markdown
# 标题（吸引眼球，可用数字/疑问/反差）

> 前言（制造悬念/引发共鸣）

## 一、{主题}为什么这么火？
- 现象描述
- 数据支持
- 引发原因

## 二、{主题}的真相
- 表面现象 vs 实际情况
- 案例分析
- 深度解读

## 三、普通人怎么做？
- 建议1（具体可执行）
- 建议2
- 建议3
- 建议4
- 建议5

## 四、写在最后
- 总结
- 升华主题
- 引发思考

---

**互动引导**
👇 你怎么看？评论区聊聊~

**标签**
```

**风格要点**：
- ✅ 口语化、有温度
- ✅ 短句、短段（2-4句）
- ✅ 小标题清晰
- ✅ 有数据/案例支撑
- ✅ 引发情绪共鸣
- ❌ 学术化、说教
- ❌ 长段落、长句
- ❌ 敏感词/违规用语
- ❌ 夸大宣传/绝对化表述

**输出格式**：Markdown 文件，保存到 `articles/{日期}-{主题}/article.md`

---

### 步骤5：生成配图

**配图工具**：
- 主方案：autoglm-generate-image（AI生成）
- 备用方案：autoglm-search-image（搜索图片）

**脚本位置**：
```
~/.openclaw-autoclaw/skills/autoglm-generate-image/generate-image.py
~/.openclaw-autoclaw/skills/autoglm-search-image/search-image.py
```

**文件存储结构**：
```
articles/
└── 2026-03-16-{主题}/
    ├── article.md           # 文章内容
    └── images/
        ├── cover.jpg        # 封面图
        ├── content-1.jpg    # 内容配图1
        ├── content-2.jpg    # 内容配图2
        └── ...
```

---

#### 5.1 判断配图数量

**根据文章内容和结构判断**：
- 封面图：1张（必须）
- 内容配图：根据文章段落/小节数量决定（2-4张）
- 总数：3-5张

**判断逻辑**：
```
如果文章有 3-4 个主要段落 → 3张配图（封面+2张内容）
如果文章有 5+ 个主要段落 → 5张配图（封面+4张内容）
```

---

#### 5.2 生成配图描述

**根据文章内容生成描述**（用于 generate-image）：

**描述要求**：
- 与文章段落内容相关
- 无文字、无英文
- 风格统一（建议：写实+科技感/简约插画）
- 尺寸：公众号推荐 1920x1080 或 16:9

**描述模板**：
```
封面图：{主题}概念图，{场景描述}，无文字，高质量
内容图1：{段落核心观点}场景，{视觉元素}，无文字
内容图2：{段落核心观点}场景，{视觉元素}，无文字
...
```

---

#### 5.3 生成配图（主方案）

**命令**：
```bash
python generate-image.py "{配图描述}"
```

**成功返回**：
```json
{
  "code": 0,
  "data": {
    "image_url": "https://..."
  }
}
```

**失败处理**：
1. 重试3次
2. 简化描述
3. 切换到备用方案（搜索图片）

---

#### 5.4 搜索配图（备用方案）

**触发条件**：
- AI生成失败（重试3次后仍失败）
- 用户要求使用真实图片

**命令**：
```bash
python search-image.py "{关键词}"
```

**搜索关键词**：
- 从文章段落中提取核心名词
- 例如："办公场景"、"团队协作"、"科技感"

**返回结果**：
```json
{
  "data": {
    "results": [
      {
        "original_url": "图片链接",
        "caption": "描述",
        "source": "来源"
      }
    ]
  }
}
```

**选择标准**：
- 与文章内容相关
- 图片清晰、质量高
- 尺寸合适（优先横图）

---

#### 5.5 下载并保存配图

**下载命令**（使用 Invoke-WebRequest）：
```powershell
# 下载封面图
Invoke-WebRequest -Uri "{图片URL}" -OutFile "articles/{日期}-{主题}/images/cover.jpg"

# 下载内容配图
Invoke-WebRequest -Uri "{图片URL}" -OutFile "articles/{日期}-{主题}/images/content-1.jpg"
```

**保存位置**：`articles/{日期}-{主题}/images/`

**文件命名**：
- 封面图：`cover.jpg`
- 内容配图：`content-1.jpg`、`content-2.jpg`、...

**记录配图信息**（保存到同目录 `images.json`）：
```json
{
  "topic": "主题",
  "date": "2026-03-16",
  "images": [
    {
      "type": "cover",
      "filename": "cover.jpg",
      "local_path": "articles/2026-03-16-主题/images/cover.jpg",
      "url": "https://...",
      "source": "generated",
      "downloaded": true
    },
    {
      "type": "content",
      "filename": "content-1.jpg",
      "local_path": "articles/2026-03-16-主题/images/content-1.jpg",
      "url": "https://...",
      "source": "generated",
      "downloaded": true
    }
  ]
}
```

---

### 步骤6：上传到公众号草稿箱

#### 6.1 获取 access_token
```
GET https://api.weixin.qq.com/cgi-bin/token
?grant_type=client_credential
&appid={appid}
&secret={appsecret}
```

**有效期**：7200秒（2小时）

#### 6.2 上传图片素材（永久素材）
```
POST https://api.weixin.qq.com/cgi-bin/material/add_material
?access_token={token}
&type=image

Content-Type: multipart/form-data
media: 图片文件
```

**返回**：
```json
{
  "media_id": "素材ID",
  "url": "图片URL"
}
```

#### 6.3 创建草稿
```
POST https://api.weixin.qq.com/cgi-bin/draft/add
?access_token={token}

{
  "articles": [{
    "title": "文章标题",
    "author": "",
    "digest": "摘要",
    "content": "HTML内容",
    "thumb_media_id": "封面图media_id",
    "need_open_comment": 0,
    "only_fans_can_comment": 0
  }]
}
```

---

## HTML 样式规范

见 `html_styles.md` 文件

---

## 异常处理

### 用户未回复
- 2小时后自动结束，记录日志

### 搜索失败
1. 重试3次
2. 切换搜索源
3. 通知用户手动提供素材

### 图片生成失败
1. 重试3次
2. 降低提示词复杂度
3. 使用默认配图

### 上传失败
1. 检查 access_token 是否过期
2. 重新获取 token
3. 重试上传
4. 检查 IP 白名单配置

---

## 日志记录

**日志位置**：`logs/{日期}.log`

**日志格式**：
```
[2026-03-12 09:00:00] 定时任务触发
[2026-03-12 09:00:01] 向用户询问主题
[2026-03-12 09:05:23] 用户回复主题: 一人公司
[2026-03-12 09:05:24] 开始搜索热点信息
[2026-03-12 09:06:45] 搜索完成，找到15条信息
[2026-03-12 09:06:46] 开始生成文章
[2026-03-12 09:08:32] 文章生成完成，字数: 3577
[2026-03-12 09:08:33] 开始生成配图
[2026-03-12 09:12:18] 配图生成完成，共4张
[2026-03-12 09:12:19] 开始上传到公众号
[2026-03-12 09:14:35] 草稿创建成功
[2026-03-12 09:14:36] ✅ 全流程完成
```

---

## 使用示例

### 手动触发
```
用户: 帮我写一篇关于"AI工具"的公众号文章
Agent: [执行完整流程]
Agent: ✅ 草稿创建成功！查看: https://mp.weixin.qq.com
```

### 定时触发
```
09:00 - Agent: 早安！今天想创作什么主题？
09:05 - 用户: AI工具
09:06 - [自动开始搜索、生成、上传]
09:15 - Agent: ✅ 草稿创建成功！
```

---

## 文件清单

- `SKILL.md` - 本文件，主描述
- `config_template.json` - 配置文件模板
- `article_template.md` - 文章结构参考
- `html_styles.md` - HTML 样式规范
- `api_reference.md` - API 接口参考

---

## 依赖技能

| 技能 | 用途 |
|------|------|
| autoglm-websearch | 搜索热点信息 |
| blog-writer | 生成文章内容 |
| autoglm-generate-image | AI 生成配图 |
| autoglm-search-image | 搜索配图（备用） |

---

## 注意事项

1. **配置安全**：`wechat_config.json` 包含敏感信息，不要提交到公开仓库
2. **IP 白名单**：确保服务器 IP 已添加到公众号白名单
3. **Token 有效期**：access_token 有效期2小时，需缓存并定时刷新
4. **图片限制**：图片大小不超过10MB，支持 PNG/JPG 格式
5. **内容合规**：文章需符合公众号内容规范，避免敏感词和违规用语

---

**版本**: 1.2.0
**作者**: AutoClaw
**更新日期**: 2026-03-16
**更新记录**:
- 1.2.0 - 添加图片下载步骤，成功上传草稿到公众号
- 1.1.0 - 更新为使用 AutoGLM 系列工具 + blog-writer
- 1.0.0 - 初始版本
