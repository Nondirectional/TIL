 # uvloop 平台兼容性决策记录

---

## 决策背景
uvloop 是高性能事件循环库，但官方明确不支持 Windows 平台。直接依赖 uvloop 会导致 Windows 用户无法正常安装或运行，需调整为仅在 Linux 下可选安装，保证跨平台兼容性。

## 可选方案
- **方案一：仅在 Linux 下将 uvloop 作为可选依赖，Windows 用户不安装**
  - 优点：保证跨平台兼容性，Linux 下可获得性能提升
  - 缺点：Windows 用户无 uvloop 性能优化
- **方案二：强制所有平台安装 uvloop，Windows 用户手动规避**
  - 优点：配置简单
  - 缺点：Windows 用户体验差，易出错

## 最终决策
选择方案一。将 uvloop 仅作为 Linux 平台的可选依赖，避免 Windows 用户因 uvloop 安装失败导致问题。

## 实施计划
1. 修改 pyproject.toml，在 `[project.optional-dependencies]` 下新增：
   ```toml
   [project.optional-dependencies]
   linux-performance = ["uvloop>=0.21.0"]
   windows-performance = []
   ```
2. 文档中明确说明 uvloop 仅推荐 Linux 用户安装，Windows 用户无需关注。
3. 在 Linux 下测试 uvloop 安装，Windows 下验证无 uvloop 安装报错。

## 风险点与应对
- **风险 1**：Linux 用户未主动安装 uvloop
  - 应对：在文档中明确推荐
- **风险 2**：Windows 用户误装 uvloop
  - 应对：在文档中明确说明 uvloop 不支持 Windows

---

> 本文档用于记录 uvloop 平台兼容性相关决策，便于团队后续查阅与协作。