# Hermes 诊断报告 | 2026-04-17 (最终版)

## 🔍 问题诊断

### 发现的问题

| 问题 | 严重程度 | 状态 |
|------|----------|------|
| ❌ venv 环境损坏（pip 不存在） | 🔴 严重 | ✅ 已修复 |
| ❌ Python 依赖未安装 | 🔴 严重 | ✅ 已修复 |
| ❌ **provider 配置错误（nvidia 不存在）** | 🔴 严重 | ✅ 已修复 |
| ⚠️ 配置文件格式问题 | 🟡 中等 | ✅ 已修复 |
| ⚠️ 智能路由未启用 | 🟡 中等 | ✅ 已启用 |
| ⚠️ 无备用模型配置 | 🟡 中等 | ✅ 已配置 |

---

## ✅ 已完成的升级

### 1. 环境修复
```bash
cd ~/.hermes/hermes-agent
rm -rf venv
uv venv --python 3.11
source .venv/bin/activate
uv pip install -e ".[all]"
```

### 2. 配置优化（最终版）

```yaml
model:
  default: z-ai/glm5
  provider: custom  # ⚠️ 必须用 custom，不能用 nvidia
  base_url: https://integrate.api.nvidia.com/v1
  api_key: ${NVIDIA_API_KEY}

fallback_model:
  provider: zai
  base_url: https://api.z.ai/v1
  api_key: ${ZAI_API_KEY:-${NVIDIA_API_KEY}}
  model: glm-4

smart_model_routing:
  enabled: true
  cheap_model:
    provider: custom
    base_url: https://integrate.api.nvidia.com/v1
    api_key: ${NVIDIA_API_KEY}
    model: z-ai/glm4-flash
```

### 3. 关键修复

| 问题 | 解决方案 |
|------|----------|
| `provider: nvidia` 报错 | 改为 `provider: custom` |
| Hermes 不识别 nvidia provider | 使用 custom + base_url 配置 |

---

## ⚠️ 重要发现：Hermes 支持的 Providers

| Provider | API Key 环境变量 | 说明 |
|----------|------------------|------|
| `openrouter` | `OPENROUTER_API_KEY` | 聚合器，支持多种模型 |
| `nous` | OAuth (hermes auth) | Nous Portal |
| `openai-codex` | OAuth (hermes auth) | OpenAI Codex |
| `zai` | `ZAI_API_KEY` | Z.AI / GLM |
| `kimi-coding` | `KIMI_API_KEY` | Kimi / Moonshot |
| `minimax` | `MINIMAX_API_KEY` | MiniMax |
| **`custom`** | 自定义 | **OpenAI 兼容端点（如 NVIDIA）** |

**NVIDIA API 必须用 `custom` provider！**

---

## 📊 当前状态

```
模型: z-ai/glm5
Provider: custom (NVIDIA API)
Fallback: glm-4 (ZAI API)
智能路由: ✅ 已启用
廉价模型: z-ai/glm4-flash
```

---

## 🔧 快速命令

```bash
# 激活环境
source ~/.hermes/hermes-agent/.venv/bin/activate

# 测试 CLI
python cli.py -q "hello"

# 检查配置
cat ~/.hermes/config.yaml | head -20

# 验证模块
python -c "import run_agent; print('OK')"
```

---

*诊断完成时间: 2026-04-17 04:30 (GMT+8)*  
*关键修复: provider 必须用 `custom` 而非 `nvidia`*
