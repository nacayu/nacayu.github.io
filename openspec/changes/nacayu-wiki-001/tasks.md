# nacayu-wiki-001 Tasks

## Implementation Tasks

### Phase 1: Core Infrastructure (Day 1)

- [ ] **T1.1:** 创建 `wiki/` 目录结构
  - `wiki/index.qmd`
  - `wiki/public/_metadata.yml`
  - `wiki/private/_metadata.yml`

- [ ] **T1.2:** 实现 `filter-wiki.py` 过滤脚本
  - 扫描 wiki/ 目录下所有 .qmd 文件
  - 解析 frontmatter 的 visibility 字段
  - 处理 _metadata.yml 目录级默认配置
  - 输出过滤后的文件列表

- [ ] **T1.3:** 配置 .gitignore
  - 排除 .DS_Store 等系统文件
  - 确保 private 内容不上传

- [ ] **T1.4:** 编写单元测试
  - `tests/test_filter_wiki.py`
  - 测试 public/private 文件识别
  - 测试 _metadata.yml 默认值
  - 测试过滤逻辑

### Phase 2: CI/CD Integration (Day 2)

- [ ] **T2.1:** 修改 `.github/workflows/publish.yml`
  - 添加 filter-wiki.py 调用步骤
  - 确保只渲染公开内容到 docs/

- [ ] **T2.2:** 本地验证 CI 流程
  - 推送测试分支
  - 验证构建产物
  - 确认无 private 内容泄露

### Phase 3: Wiki Content (Day 3+)

- [ ] **T3.1:** 创建 Wiki 索引页
  - `wiki/index.qmd`
  - 展示所有 public wiki 页面
  - 分类展示

- [ ] **T3.2:** 创建示例 Wiki 内容
  - 至少 3 篇公开 wiki 文章
  - 至少 1 篇私人 wiki 文章（测试用）

---

**Dependencies:**
- T1.2 depends on T1.1
- T1.4 depends on T1.2
- T2.1 depends on T1.2, T1.4
- T3.1 depends on T1.1

**Total Estimated:** 2-3 days