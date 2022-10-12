---
layout:     post
title:      "Search in Python"
date:       2022-10-12 
author:     "Pengc"
catalog: false
published: true
header-style: text
tags:
  - python
---

> 来源于一个死活跑不出来的小作业

想象一个python代码跑了一个小时也跑不出来的抓狂。来自于一个简单的BFS搜索：

```python
def search_without_info(problem):
    """
    YOUR CODE HERE
    """
    print("无信息搜索")
    # 思路，广度优先搜索，维护一个队列和一个列表
    qu = Queue()
    close = []

    # 维护一些初始化的信息
    parking = problem
    node_start = parking.init_state
    node_end = parking.goal_state
    qu.push(node_start)

    # 开始广度优先搜索
    while not qu.empty():
        node = qu.pop()
        state = node.state
        close.append(state)
        actions = parking.actions(state)
        for action in actions:
            target, empty_pos = action[0], action[1]
            new_node = node.child_node(parking, empty_pos, target)
            if new_node == node_end:
                return new_node
            if new_node.state not in close and qu.find(new_node) is None: # ***
                # print(f'depth:{new_node.depth}')
                qu.push(new_node)

    return 0
```

打了***的那一步使得整个程序跑不出来。

和高老师商量了一下发现是list和queue的这种查找都是O(n)的，于是果断的换用set维护了：

```python
def search_without_info_pro(problem):
    """
    相比于非pro版本，此版本的查找都是hash的
    """
    print("无信息搜索-hash")
    # 思路，广度优先搜索
    qu = Queue()
    close = Set()
    open = Set()
    # 维护一些初始化的信息
    parking = problem
    node_start = parking.init_state
    node_end = parking.goal_state
    qu.push(node_start)
    open.add(np.array(node_start.state).tobytes())
    # 开始广度优先搜索
    while not qu.empty():
        node = qu.pop()
        state = node.state
        str_state = np.array(node.state).tobytes()  # tobytes enables hash
        open.remove(str_state)
        close.add(str_state)
        actions = parking.actions(state)
        for action in actions:
            target, empty_pos = action[0], action[1]
            new_node = node.child_node(parking, empty_pos, target)
            if new_node == node_end:
                return new_node
            if not close.find(np.array(new_node.state).tobytes()) and not open.find(np.array(new_node.state).tobytes()):
                qu.push(new_node)
                open.add(np.array(new_node.state).tobytes())
    return 0
```

结果只需要10s：

```
无信息搜索-hash
time cost:10.71301007270813
depth:15
```

所以说，python中查找需要慎重，用set和dict这种hash的办法应该是个好的选择。
