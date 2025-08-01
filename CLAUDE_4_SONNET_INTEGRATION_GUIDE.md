# Claude 4 Sonnet 集成指南

## 🎉 集成状态

✅ **Claude 4 Sonnet 已成功集成到系统中！**

## 📋 支持的模型

### Claude 4 Models (最新一代)

| 模型名称 | API 标识符 | 别名 | 描述 |
|---------|-----------|------|------|
| Claude Opus 4 | `claude-opus-4-20250514` | `claude-opus-4-0` | 最强能力模型 |
| Claude Sonnet 4 | `claude-sonnet-4-20250514` | `claude-sonnet-4-0` | 高性能平衡模型 |

### 模型特性对比

| 特性 | Claude 4 Sonnet | Claude 4 Opus | Claude 3.5 Sonnet |
|------|----------------|---------------|-------------------|
| **性能等级** | 高性能平衡 | 最强能力 | 智能模型 |
| **上下文窗口** | 200K tokens | 200K tokens | 200K tokens |
| **最大输出** | 64,000 tokens | 32,000 tokens | 8,192 tokens |
| **扩展思考** | ✅ 支持 | ✅ 支持 | ❌ 不支持 |
| **视觉输入** | ✅ 支持 | ✅ 支持 | ✅ 支持 |
| **多语言** | ✅ 支持 | ✅ 支持 | ✅ 支持 |
| **训练截止** | 2025年3月 | 2025年3月 | 2024年4月 |

## 💰 定价信息

根据 Anthropic 官方定价（每百万 tokens）：

| 模型 | 输入价格 | 输出价格 | 缓存价格 |
|------|----------|----------|----------|
| Claude 4 Sonnet | $3.00 | $15.00 | $0.30 |
| Claude 4 Opus | $15.00 | $75.00 | $1.50 |

## 🚀 使用方法

### 1. 通过常量使用

```typescript
import { AnthropicClient } from './core/anthropicClient.js';

// 使用具体版本（推荐生产环境）
const model = AnthropicClient.CLAUDE_MODELS.SONNET_4;
// 值: 'claude-sonnet-4-20250514'

// 使用别名（推荐开发测试）
const modelAlias = AnthropicClient.CLAUDE_MODELS.SONNET_4_LATEST;
// 值: 'claude-sonnet-4-0'
```

### 2. 直接使用模型名称

```typescript
// 生产环境使用具体版本
const response = await claudeClient.sendMessage(
  "你好，请介绍一下你的能力", 
  'claude-sonnet-4-20250514',
  { maxTokens: 4096 }
);

// 开发环境使用别名
const response = await claudeClient.sendMessage(
  "测试 Claude 4 Sonnet", 
  'claude-sonnet-4-0',
  { maxTokens: 4096 }
);
```

### 3. 通过 CLI 命令使用

```bash
# 设置模型为 Claude 4 Sonnet
/model set claude-sonnet-4-20250514

# 或使用别名
/model set claude-sonnet-4-0
```

## ⭐ Claude 4 Sonnet 的优势

### 🧠 **卓越的推理能力**
- 在复杂推理任务中表现出色
- 支持扩展思考（Extended Thinking）功能
- 能够进行多步骤逻辑分析

### 💻 **编程和开发**
- 最大输出 64K tokens，适合生成长代码
- 支持完整的软件开发生命周期
- 优秀的代码调试和优化能力

### 👁️ **多模态处理**
- 支持图像分析和处理
- 能够理解图表、图形和复杂视觉内容
- 适合数据分析和视觉任务

### ⚡ **性能优化**
- 比 Claude 3.5 更快的响应速度
- 更准确的任务执行
- 更低的幻觉率（hallucination）

## 🛠️ 迁移建议

### 从 Claude 3.5 Sonnet 迁移

```typescript
// 旧版本
const oldModel = AnthropicClient.CLAUDE_MODELS.SONNET_3_5;

// 新版本 - 只需更改模型名称
const newModel = AnthropicClient.CLAUDE_MODELS.SONNET_4;

// API 调用方式完全兼容
const response = await claudeClient.sendMessage(message, newModel, options);
```

### 迁移检查清单

- [ ] 更新模型名称常量
- [ ] 测试现有功能兼容性
- [ ] 验证工具调用正常工作
- [ ] 检查输出格式是否符合预期
- [ ] 监控 API 使用成本变化

## 🔧 配置示例

### anthropicClient 配置

```typescript
const config = {
  model: AnthropicClient.CLAUDE_MODELS.SONNET_4,
  maxTokens: 64000,  // Claude 4 Sonnet 支持更大输出
  temperature: 0.1,
  systemPrompt: "你是一个专业的AI助手..."
};
```

### 扩展思考功能

```typescript
// 启用扩展思考功能（Claude 4 专有）
const response = await claudeClient.sendMessage(
  "请详细分析这个复杂问题", 
  AnthropicClient.CLAUDE_MODELS.SONNET_4,
  { 
    maxTokens: 32000,
    enableExtendedThinking: true  // 启用扩展思考
  }
);
```

## ⚠️ 注意事项

1. **成本管理**: Claude 4 Sonnet 比 Claude 3.5 成本相同，但比 Claude 3 Haiku 成本更高
2. **版本选择**: 生产环境建议使用具体版本号，避免自动更新带来的不确定性
3. **输出限制**: 虽然支持 64K tokens 输出，但请根据实际需求合理设置
4. **API 兼容性**: 现有的 API 调用方式完全兼容，无需修改代码结构

## 🧪 测试验证

系统已包含完整的测试脚本，可以验证 Claude 4 Sonnet 的集成状态：

```bash
node test-claude4-integration.js
```

## 📞 技术支持

如果在使用 Claude 4 Sonnet 过程中遇到问题：

1. 检查 API 密钥是否有 Claude 4 访问权限
2. 确认模型名称拼写正确
3. 验证网络连接和代理设置
4. 查看错误日志获取详细信息

---

**🎯 开始使用 Claude 4 Sonnet，体验最新一代 AI 的强大能力！**