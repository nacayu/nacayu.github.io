# nacayu-wiki-001: 嵌入式个人知识管理系统

## Proposal

### What

在 nacayu.github.io 中新增 Wiki 模块，支持：
- **公开 Wiki** - GitHub Pages 可访问的知识文章
- **私人 Wiki** - 本地可访问，推送时自动过滤
- **自动过滤 CI** - GitHub Actions 在 publish 时过滤私人内容

### Why

1. **Karpathy LLM Wiki 理念实践** - 知识积累而非每次重查找
2. **个人品牌建设** - 公开 Wiki 展示专业能力
3. **隐私保护** - 私人内容本地可见，GitHub Pages 不可见

### Scope

**In Scope:**
- `wiki/` 目录结构 (public/private/_metadata.yml)
- `filter-wiki.py` 过滤脚本
- GitHub Actions CI 修改
- Wiki 索引页 (`wiki/index.qmd`)

**Out of Scope:**
- Wiki 搜索功能
- 知识图谱可视化
- 多语言支持

### Done Means

1. ✅ `filter-wiki.py` 能正确过滤 `visibility: private` 的文件
2. ✅ GitHub Actions 构建产物中不包含任何 private 文件
3. ✅ 本地 `wiki/` 目录包含完整内容（含 private）
4. ✅ Wiki 索引页正常展示公开内容
5. ✅ 单元测试覆盖过滤逻辑

---

**Created:** 2026-05-20
**Status:** proposed
**Author:** nacayu