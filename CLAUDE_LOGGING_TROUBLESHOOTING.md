# Claude 日志系统故障排除指南

## 🔍 当前问题分析

根据您提供的日志，我们发现了以下问题：

### 1. 日志系统部分工作 ✅
- 工具调用日志正在生成
- 文件保存到正确位置：`%USERPROFILE%\claude-debug-logs\`

### 2. 存在的问题 ❌

#### 问题A：缺少完整交互日志
**现象**：只看到 `claude-tool-*.txt` 文件，没有 `claude-interaction-*.txt` 文件
**原因**：可能是应用程序还在使用旧版本代码，需要重启

#### 问题B：中文字符乱码
**现象**：日志文件中中文显示为乱码
```
鏃堕棿: 2025/8/1 21:58:51  # 应该是：时间: 2025/8/1 21:58:51
宸ュ叿: run_shell_command    # 应该是：工具: run_shell_command
```

#### 问题C：错误信息显示为空对象
**现象**：错误信息显示为 `{}`，而不是具体错误消息
**期望**：应该显示 `"Command must be a non-empty string"`

## 🛠️ 解决方案

### 方案1：重启应用程序（推荐）

**为什么需要重启？**
- 我们修改了 `packages/core/src/core/anthropicClient.ts` 文件
- 应用程序可能还在使用内存中的旧代码
- 需要重启以加载最新的日志系统代码

**操作步骤：**
1. 完全关闭当前的 uevo 应用程序
2. 重新启动应用程序
3. 再次测试 shell 工具

### 方案2：验证代码是否正确部署

**检查构建输出：**
```bash
npm run build
```

**检查日志目录：**
```powershell
Get-ChildItem "$env:USERPROFILE\claude-debug-logs" | Sort-Object LastWriteTime -Descending
```

### 方案3：手动验证最新日志

**查看最新的工具调用日志：**
```powershell
Get-Content "$env:USERPROFILE\claude-debug-logs\claude-tool-run_shell_command-*.txt" | Select-Object -Last 50
```

## 🎯 期望的正确日志格式

### 完整交互日志示例
文件名：`claude-interaction-2025-08-01T13-58-48-933Z.txt`
```
================================================================================
时间: 2025/8/1 21:58:48
模型: claude-3-5-sonnet-20241022
================================================================================

用户消息:
现在对你的shell工具进行测试，尝试cd到packages文件夹

========================================
发送给Claude的请求:
========================================
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 4096,
  "temperature": 0.1,
  "messages": [...],
  "tools": [
    {
      "name": "run_shell_command",
      "input_schema": {
        "type": "object",
        "properties": {
          "command": {
            "type": "string",
            "description": "Exact bash command to execute"
          }
        },
        "required": ["command"]  // ← 注意这里有required字段
      }
    }
  ]
}

========================================
Claude的原始响应:
========================================
{
  "content": [
    {
      "type": "tool_use",
      "id": "toolu_123",
      "name": "run_shell_command",
      "input": {}  // ← 问题根源：Claude发送了空对象
    }
  ]
}
```

### 工具调用日志示例
文件名：`claude-tool-run_shell_command-2025-08-01T13-58-48-933Z.txt`
```
================================================================================
时间: 2025/8/1 21:58:48
工具: run_shell_command
================================================================================

Claude发送的原始工具调用:
{
  "type": "tool_use",
  "id": "toolu_01RX1ZXjkMm4VzBdj7vohC9h",
  "name": "run_shell_command",
  "input": {}  // ← 清楚显示Claude发送了空参数
}

解析后的输入参数:
{}

执行结果:
N/A

错误信息:
"Command must be a non-empty string"  // ← 应该显示具体错误消息

================================================================================
```

## 🔧 代码修改摘要

已经修改的关键代码：

### 1. 增强的日志记录器
```typescript
class ClaudeResponseLogger {
  // 新增：记录完整交互
  static logInteraction(request, response, context) { ... }
  
  // 增强：记录原始工具调用对象
  static logToolCall(toolName, toolInput, toolResult, error, originalToolUse) { ... }
}
```

### 2. 集成位置
```typescript
// 在 AnthropicClient.sendMessage() 中
ClaudeResponseLogger.logInteraction(requestParams, response, {
  userMessage: userMessage,
  model: model,
  conversationHistory: this.conversationHistory
});

// 在 AnthropicClient.executeToolCall() 中  
ClaudeResponseLogger.logToolCall(toolUse.name, toolUse.input, result, error, toolUse);
```

## 📋 验证清单

重启应用程序后，请验证：

- [ ] 生成了 `claude-interaction-*.txt` 文件
- [ ] 中文字符显示正常（不是乱码）
- [ ] 错误信息显示具体消息而不是 `{}`
- [ ] 可以看到发送给Claude的完整请求
- [ ] 可以看到Claude的原始工具调用对象

## 🚀 下一步

一旦日志系统正常工作，您将能够：

1. **清楚看到问题根源**：Claude发送空的input对象
2. **分析工具定义**：检查工具schema是否正确传递给Claude
3. **诊断提示词问题**：可能需要改进系统提示词来指导Claude正确填充参数
4. **追踪完整流程**：从用户消息到工具调用的完整过程

## 💡 临时解决方案

如果日志系统仍有问题，您可以：

1. **查看控制台输出**：寻找 `[Claude Logger]` 开头的消息
2. **检查文件权限**：确保应用程序有权限写入用户目录
3. **手动创建测试**：使用简单的文件写入测试验证基本功能

这个增强的日志系统将帮助您快速定位Claude工具调用的问题根源！