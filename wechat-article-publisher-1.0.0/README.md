# WeChat Article Publisher Skill

公众号内容自动化创作工作流。

## 快速开始

### 1. 获取公众号配置

#### 登录微信公众平台
访问 https://mp.weixin.qq.com，用管理员微信扫码登录。

#### 获取 AppID 和 AppSecret
1. 左侧菜单：**开发 → 基本配置**
2. 在「公众号开发信息」区域：
   - **AppID**：直接可见，复制保存
   - **AppSecret**：点击「查看」，需管理员扫码验证，验证通过后显示（**只显示一次，务必保存**）

#### 配置 IP 白名单
1. 同一页面，点击 **IP白名单** 旁的「修改」
2. 添加你的服务器 IP
3. 查看本机 IP：访问 https://www.ip.cn

> ⚠️ AppSecret 忘记后需要重置，重置后旧的立即失效。

### 2. 创建配置文件
在 workspace 根目录创建 `wechat_config.json`：

```json
{
  "wechat": {
    "appid": "你的AppID",
    "appsecret": "你的AppSecret"
  }
}
```

### 3. 开始使用
```
用户: 帮我写一篇关于"AI工具"的公众号文章
Agent: [自动执行完整流程]
```

## 工作流程

```
询问主题 → 搜索热点 → 生成文章 → 生成配图 → 上传草稿
```

### 步骤说明

| 步骤 | 工具 | 说明 |
|------|------|------|
| 1. 询问主题 | - | 9:00 定时询问今日创作主题 |
| 2. 搜索热点 | autoglm-websearch | 搜索抖音/小红书/公众号热点 |
| 3. 生成文章 | blog-writer | 结合热点，生成公众号风格文章 |
| 4. 生成配图 | autoglm-generate-image / autoglm-search-image | 根据文章内容生成/搜索配图 |
| 5. 上传草稿 | 微信 API | 上传到公众号草稿箱 |

## 依赖技能

| 技能 | 用途 |
|------|------|
| autoglm-websearch | 网络搜索热点信息 |
| blog-writer | 生成文章内容 |
| autoglm-generate-image | AI 生成配图 |
| autoglm-search-image | 搜索配图（备用） |

## 文件说明

| 文件 | 说明 |
|------|------|
| SKILL.md | 主文档，完整工作流程 |
| config_template.json | 配置文件模板 |
| article_template.md | 文章结构参考 |
| html_styles.md | HTML 排版规范 |
| api_reference.md | 微信 API 参考 |

## 注意事项

1. 配置文件包含敏感信息，不要提交到公开仓库
2. 确保 IP 已添加到白名单
3. access_token 有效期 2 小时
4. 图片大小不超过 10MB
5. 文章需符合公众号内容规范，避免敏感词

## 版本

- 1.1.0 - 更新为使用 AutoGLM 系列工具 + blog-writer
- 1.0.0 - 初始版本
