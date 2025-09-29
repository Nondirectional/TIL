# Decision Log

This file records architectural and implementation decisions using a list format.
2025-07-17 14:50:42 - Log of updates made.

*

## Decision

*

## Rationale 

*

## Implementation Details

*   

---
### Decision Record
[2025-07-17 14:50:58] - uvloop 在 Windows 不支持，需将 uvloop 作为 Linux 平台可选依赖，更新 pyproject.toml 的 [project.optional-dependencies]，并为 Windows 用户提供性能优化的替代方案。

**Decision Background:**
uvloop 是高性能事件循环库，但官方明确不支持 Windows 平台。直接依赖 uvloop 会导致 Windows 用户无法正常安装或运行，需调整为仅在 Linux 下可选安装。

**Available Options:**
- Option 1: 仅在 Linux 下将 uvloop 作为可选依赖，Windows 用户不安装
  - Pros: 保证跨平台兼容性，Linux 下可获得性能提升
  - Cons: Windows 用户无 uvloop 性能优化
- Option 2: 强制所有平台安装 uvloop，Windows 用户手动规避
  - Pros: 配置简单
  - Cons: Windows 用户体验差，易出错

**Final Decision:**
选择 Option 1。将 uvloop 仅作为 Linux 平台的可选依赖，避免 Windows 用户因 uvloop 安装失败导致问题。

**Implementation Plan:**
- Step 1: 修改 pyproject.toml，在 [project.optional-dependencies] 下新增 linux-performance = ["uvloop>=0.21.0"]
- Step 2: 新增 windows-performance = []，为 Windows 用户后续性能优化预留
- Validation Method: 在 Linux 下测试 uvloop 安装，Windows 下验证无 uvloop 安装报错

**Risks and Mitigation:**
- Risk 1: Linux 用户未主动安装 uvloop → Mitigation: 文档中明确推荐
- Risk 2: Windows 用户误装 uvloop → Mitigation: 文档中明确说明 uvloop 不支持 Windows 