## 提交阶段 <br />

renderRoot 的最后一个函数调用 commitRoot(root)

1. 断开链接,rootFiberNode 和 wip tree 的联系 `root.finishedWork=null`
2. 判断是否是否存在这三个操作，`subTreeHasEffect = (finishedWork.flags & MutationMask) !== NoFlag`,这里的 MutationMask 拥有三个操作的并集。这里是 true,然后判断`rootHasEffect=(finishedWork.flags & MutationMask) !== NoFlags`,这里为 0，当这二者有一个是正确的时候就会进入 commitMutationEffects(finishedWork)

- tip:这里的三个操作分别是 Placement:插入,Update：更新,childDeletion：删除,NoFlags===0b0

3. commitMutationEffects(finishedWork),显示往下走不断获得 child = finished.child,只有子元素存在并且子元素的 subTreeFlags 为真的时候才会继续往下递归。要不然就是往上递归。
   1. 往上递归 commitMutationEffectsOnFiber(finishedWork),注意这里的 finishedWork 已经变成了最小的变化 fiber,获得当前 fiber 的 flags
   2. 如果 flags 存在插入操作就会执行 commitPlacement(fWork),进入执行 getHostParent(fiber)获得 fiber 的 return,如果这个 parent 存在那么就会依据 parent.tag 返回对应的 stateNode,如果是 HostRoot(就是挂载的 root 节点的子节点) 就会返回 parent.stateNode.container
   3. 找到以后,parent 存在就会执行 appendPlacementIntoContainer(FWork,hostParent)
      1. 如果 fw.tag 是 HostComponent 就会进入 appendChildToContainer(hostParent,fw.stateNode) 2.这个函数会直接执行 parent.appendChild(child),当然 parent=hostParent,child = fw.stateNode

- 总结：自此，元素插入父节点成功，页面显示对应元素

  4. 执行完 commitPlacement，finishedWork.flags 去除 placementFlags,这里的只有插入操作，所以 flags = 0，取得 nextEffect.sibling,这里的为 null,所以 nextEffect = nxtEffect.return,而不是 nextEffect = sibling

4. 原本指向的 fiberRootNode.current 由 hostRootFiber 指向了 wip tree,也就是完成了新老交替

结束了！！！！！！！！挂载流程
