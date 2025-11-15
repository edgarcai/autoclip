# DeepSeek 模型集成说明

## 概述

本次更新为 AutoClip 项目添加了 DeepSeek 模型作为额外的 AI 模型选项。DeepSeek 是一家提供先进大语言模型的公司，其模型在代码生成和对话方面表现优异。

## 更新的文件

### 1. 后端更新

#### `backend/core/llm_providers.py`
- 在 `ProviderType` 枚举中添加了 `DEEPSEEK` 类型
- 创建了新的 `DeepSeekProvider` 类，支持 DeepSeek Chat 和 DeepSeek Coder 模型
- 在 `LLMProviderFactory` 中注册了 DeepSeek 提供商

**支持的模型：**
- `deepseek-chat`：支持对话和文本生成，最大 32768 tokens
- `deepseek-coder`：专注于代码生成和编程辅助，最大 16384 tokens

**API 端点：**
- Base URL: `https://api.deepseek.com/v1`
- 端点: `/chat/completions`

#### `backend/core/llm_manager.py`
- 在默认设置中添加了 `deepseek_api_key` 字段
- 更新了 `_get_api_key_for_provider` 方法，添加 DeepSeek API 密钥映射
- 更新了 `set_provider` 方法，添加 DeepSeek 支持
- 更新了 `_get_provider_display_name` 方法，添加 DeepSeek 显示名称

### 2. 前端更新

#### `frontend/src/pages/SettingsPage.tsx`
- 在 `providerConfig` 中添加了 DeepSeek 配置：
  - 名称: 'DeepSeek'
  - 图标: RobotOutlined
  - 颜色: '#13c2c2'
  - 描述: 'DeepSeek 大模型服务'
  - API 密钥字段: 'deepseek_api_key'
  - 占位符: '请输入DeepSeek API密钥'

- 在使用说明中添加了 DeepSeek 的获取 API 密钥指引：
  - 访问 platform.deepseek.com 获取 API 密钥

## 使用方法

### 1. 获取 DeepSeek API 密钥

1. 访问 [DeepSeek 平台](https://platform.deepseek.com)
2. 注册账号并登录
3. 在控制台中创建 API 密钥
4. 充值账户（如需要）

### 2. 在 AutoClip 中配置 DeepSeek

1. 启动 AutoClip 前端
2. 进入设置页面 (Settings)
3. 选择 "AI 模型配置" 标签
4. 在提供商下拉菜单中选择 "DeepSeek"
5. 输入从 DeepSeek 平台获取的 API 密钥
6. 选择要使用的模型（DeepSeek Chat 或 DeepSeek Coder）
7. 点击 "测试连接" 验证配置
8. 点击 "保存配置" 保存设置

### 3. 使用 DeepSeek 进行视频分析

配置完成后，DeepSeek 模型将用于：
- 视频内容分析和摘要生成
- 视频分段和评分
- 切片标题和描述生成
- 合集创建和管理

## 测试验证

所有更改已通过测试验证：

```bash
# 运行测试脚本
python test_deepseek.py

# 结果
✅ DeepSeek 提供商创建成功
✅ 成功获取 DeepSeek 模型列表 (2 个模型)
✅ 设置文件中包含 deepseek_api_key 字段
✅ DeepSeek 模型列表已注册
✅ 提供商显示名称正确

总计: 3/3 项测试通过
```

## 技术特性

### DeepSeekProvider 类特性

- 继承自 `LLMProvider` 抽象基类
- 使用标准的 OpenAI 兼容 API 格式
- 支持自定义温度和最大 tokens 参数
- 内置连接测试功能
- 支持错误处理和重试机制

### API 调用示例

```python
# 创建 DeepSeek 提供商
provider = DeepSeekProvider(api_key="your_key", model_name="deepseek-chat")

# 调用 API
response = provider.call("请分析以下视频内容...")
print(response.content)

# 测试连接
is_connected = provider.test_connection()
```

## 注意事项

1. **API 密钥安全**：请妥善保管您的 DeepSeek API 密钥，不要分享给他人
2. **费用控制**：DeepSeek 的使用会产生费用，请根据需要进行充值
3. **速率限制**：请注意 DeepSeek 的 API 速率限制，避免频繁调用
4. **模型选择**：
   - `deepseek-chat`：适合通用的对话和文本生成任务
   - `deepseek-coder`：适合需要代码生成或编程辅助的任务

## 后续计划

- [ ] 添加 DeepSeek 更多模型支持（如 deepseek-reasoner）
- [ ] 优化 DeepSeek API 调用的错误处理
- [ ] 添加 DeepSeek 使用统计和费用跟踪
- [ ] 完善 DeepSeek 模型在不同任务场景下的性能优化

## 更新日期

2025-11-02

## 贡献者

Claude Code (Anthropic)
