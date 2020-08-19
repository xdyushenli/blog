# 拓扑图引擎技术调研
## antv/G6
- [x] 能否可视化地生成视图？
    动态添加元素：addItem
    拖拽视图：config -> modes -> drap-node
    添加连线：通过 setMode 切换到 addEdge 状态。
- [x] 能否方便地进行数据导出？
    graph.save()
- [x] 有没有现成的状态管理方案？
    状态统一定义，通过 setItemState、updateItemState、clearItemState 进行状态更新。
- [x] 是否长期维护？
- 在 React 中应用的难度：低
- 其他
    - 开发者：蚂蚁金服
    - 社区活跃度：高
    - 许可：MIT

## visjs/vis-network
- [x] 能否可视化地生成视图？
    可视化编辑相关方法：enableEditMode() 等
- [x] 能否方便地进行数据导出？
    getPositions() + getConnectedNodes()
- [ ] 有没有现成的状态管理方案？
- [x] 是否长期维护？
- 在 React 中应用的难度：中等
- 其他
    - 开发者：开源项目
    - 社区活跃度：较高
    - 许可：Apache & MIT
    - 物理引擎支持较好
    - 中文文档地址：[https://ame.cool/core/frontend-tools/](https://ame.cool/core/frontend-tools/)

## JointJS
- [ ] 能否可视化地生成视图？
- [ ] 能否方便地进行数据导出？
- [ ] 有没有现成的状态管理方案？
- [ ] 是否长期维护？
- 在 React 中应用的难度：低
- 其他
    - 社区活跃度：较高
    - 许可：Mozilla 2.0
    - 有付费增强版：Rappid

## jsPlumb
- [ ] 能否可视化地生成视图？
- [ ] 能否方便地进行数据导出？
- [ ] 有没有现成的状态管理方案？
- [ ] 是否长期维护？
- 在 React 中应用的难度：
- 其他
    - 开发者：
    - 社区活跃度：
    - 许可：