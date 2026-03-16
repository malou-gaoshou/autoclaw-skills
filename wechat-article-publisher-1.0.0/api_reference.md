# API 接口参考

微信公众号 API 完整参考文档。

---

## 1. 获取 Access Token

### 接口
```
GET https://api.weixin.qq.com/cgi-bin/token
```

### 参数
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| grant_type | string | 是 | 固定值：client_credential |
| appid | string | 是 | 公众号唯一标识 |
| secret | string | 是 | 公众号appsecret |

### 示例
```bash
curl "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={appid}&secret={secret}"
```

### 返回
```json
{
  "access_token": "ACCESS_TOKEN",
  "expires_in": 7200
}
```

### 错误码
| 错误码 | 说明 |
|--------|------|
| -1 | 系统繁忙 |
| 40001 | AppSecret错误或不属于该公众号 |
| 40002 | 请确保grant_type字段值为client_credential |
| 40164 | 调用接口的IP地址不在白名单中 |

---

## 2. 上传图文消息内的图片

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/media/uploadimg
```

### 参数
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| access_token | string | 是 | 访问令牌 |
| media | file | 是 | 图片文件 |

### 示例
```bash
curl -X POST "https://api.weixin.qq.com/cgi-bin/media/uploadimg?access_token={token}" \
  -F "media=@image.png"
```

### 返回
```json
{
  "url": "http://mmbiz.qpic.cn/..."
}
```

---

## 3. 上传永久素材（图片）

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/material/add_material
```

### 参数
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| access_token | string | 是 | 访问令牌 |
| type | string | 是 | 固定值：image |
| media | file | 是 | 图片文件 |

### 示例
```bash
curl -X POST "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token={token}&type=image" \
  -F "media=@image.png"
```

### 返回
```json
{
  "media_id": "MEDIA_ID",
  "url": "http://mmbiz.qpic.cn/..."
}
```

### 限制
- 图片大小：不超过 10MB
- 支持格式：bmp/png/jpeg/jpg/gif

---

## 4. 创建草稿

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/draft/add
```

### 参数
```json
{
  "articles": [
    {
      "title": "标题",
      "author": "作者",
      "digest": "摘要",
      "content": "HTML内容",
      "content_source_url": "原文链接（可选）",
      "thumb_media_id": "封面图media_id",
      "need_open_comment": 0,
      "only_fans_can_comment": 0
    }
  ]
}
```

### 示例
```bash
curl -X POST "https://api.weixin.qq.com/cgi-bin/draft/add?access_token={token}" \
  -H "Content-Type: application/json" \
  -d '{
    "articles": [{
      "title": "测试文章",
      "author": "",
      "digest": "这是摘要",
      "content": "<p>这是内容</p>",
      "thumb_media_id": "MEDIA_ID",
      "need_open_comment": 0,
      "only_fans_can_comment": 0
    }]
  }'
```

### 返回
```json
{
  "media_id": "DRAFT_MEDIA_ID"
}
```

---

## 5. 修改草稿

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/draft/update
```

### 参数
```json
{
  "media_id": "草稿media_id",
  "index": 0,
  "articles": {
    "title": "新标题",
    ...
  }
}
```

---

## 6. 删除草稿

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/draft/delete
```

### 参数
```json
{
  "media_id": "草稿media_id"
}
```

---

## 7. 发布接口

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/freepublish/submit
```

### 参数
```json
{
  "media_id": "草稿media_id"
}
```

### 返回
```json
{
  "publish_id": "发布任务ID",
  "msg_data_id": "消息数据ID"
}
```

---

## 8. 获取发布状态

### 接口
```
POST https://api.weixin.qq.com/cgi-bin/freepublish/get
```

### 参数
```json
{
  "publish_id": "发布任务ID"
}
```

---

## 通用错误码

| 错误码 | 说明 |
|--------|------|
| -1 | 系统繁忙 |
| 40001 | 不合法的secret参数 |
| 40002 | 不合法的grant_type参数 |
| 40003 | 不合法的openid |
| 40004 | 不合法的media_id |
| 40007 | 不合法的media_id |
| 40009 | 不合法的图片文件大小 |
| 41001 | 缺少access_token参数 |
| 42001 | access_token超时 |
| 45001 | 图片大小超过10MB |
| 45002 | 文章内容超过20000字符 |
| 45003 | 标题长度超过64字符 |
| 45004 | 摘要长度超过120字符 |
| 45008 | 图文消息超过限制 |
| 47001 | 数据格式错误 |
| 48001 | api功能未授权 |
| 50002 | 用户受限 |
| 61004 | access_token过期 |

---

## Token 管理

### 缓存策略
```python
import time
import json

class WeChatTokenManager:
    def __init__(self, appid, secret):
        self.appid = appid
        self.secret = secret
        self.token = None
        self.expires_at = 0

    def get_token(self):
        if self.token and time.time() < self.expires_at:
            return self.token

        # 获取新token
        url = f"https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={self.appid}&secret={self.secret}"
        response = requests.get(url)
        data = response.json()

        if "access_token" in data:
            self.token = data["access_token"]
            self.expires_at = time.time() + data["expires_in"] - 300  # 提前5分钟过期
            return self.token
        else:
            raise Exception(f"获取token失败: {data}")
```

### 并发处理
- 建议使用分布式锁
- 避免多个进程同时刷新 token
- 使用中央缓存（Redis）

---

## 速率限制

| 接口 | 限制 |
|------|------|
| 获取 access_token | 每日 200 次 |
| 上传图片 | 每分钟 100 次 |
| 创建草稿 | 每分钟 100 次 |
| 发布文章 | 每日 100 次 |

---

## 调试技巧

### 1. 测试 Token
```bash
curl "https://api.weixin.qq.com/cgi-bin/get_api_domain_ip?access_token={token}"
```

### 2. 查看错误详情
```python
import json

response = requests.post(url, json=data)
result = response.json()

if "errcode" in result and result["errcode"] != 0:
    print(f"错误码: {result['errcode']}")
    print(f"错误信息: {result['errmsg']}")
```

### 3. 日志记录
```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.debug(f"请求URL: {url}")
logger.debug(f"请求参数: {json.dumps(data, ensure_ascii=False)}")
logger.debug(f"响应: {response.text}")
```
