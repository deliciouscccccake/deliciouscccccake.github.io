# 华东师范大学计算机科学技术系项目报告

---

**课程名称：** 数据结构

**姓名：** 唐浩东

**年级：** 24级

**指导教师：** 肖春芸

**学号：** 10234511421

**日期：** 2025.06.19

---
## 一、项目名称
项目导航系统

---

## 二、主要功能

本项目实现了一个校园导航系统，主要功能如下：

### 1. 节点和边的基本操作：

**插入节点：** 支持添加新的地点信息。

**删除节点：** 支持删除指定的地点。

**查找节点：** 支持根据地名查找单个地点的信息，根据类型批量查找地点，以及列举所有地点的信息。

**更新节点：** 支持更新地点的参观时间等信息。

**插入边：** 支持添加新的道路信息。

**删除边：** 支持删除指定的道路。

**查找边：** 支持根据两边地点查找边的信息，根据起点批量查找边的信息，以及列举所有边的信息。

**更新边：** 支持更新道路的权重（长度）。

### 2. 拓展功能：

**连通判断：** 判断图是否连通，并在不连通时给出使图连通的加边方案

**欧拉通路检测：** 检测图中是否存在欧拉通路。

**最短路径计算：** 实现了Dijkstra算法，计算两个地点之间的最短路径总长以及最短路径。

**最小生成树：** 通过Prim算法构造图中的最小生成树。

### 3. 数据加载与存储：

从文件中读取节点和边信息，并将其加载到图中。

将图中的节点和边信息存储到文件中，便于下次使用。
### 4. 用户交互界面：

提供命令行界面，用户可以通过菜单选择不同的操作，如查看地点信息、添加/删除地点和道路、计算最短路径等。

---

## 三、 数据测试情况

### 数据覆盖情况：

**数据存储：**

数据统一存储在Datas文件夹中

节点数据存储在nodes.csv文件中，包括地点名称、类别和参观时间

边数据存储在edges.csv文件中，包含两点之间边的权重。

**样例数据：**

节点数据格式为地点**名称，类型，游览时间**，边数据包括**道路的起点、终点和长度**。

**数据规模：**

测试数据包括1000000个地点和1500000条道路，覆盖了校园中主要的建筑和道路（迫真）。测试数据的规模适中，能够充分验证系统的功能和性能。

---

## 四、 其他部分
### 1. 性能：
**路径计算性能：**

Dijkstra算法的时间复杂度为O(E + V log V)，其中E是边的数量，V是顶点的数量。在测试数据规模下，路径计算的性能表现良好，能够在较短时间内返回结果。

**内存使用：**
系统在运行过程中使用动态数据结构（如vector、list）存储图的节点和边，内存使用量随数据规模增大而增加。在测试数据规模下，内存使用量在可接受范围内。

### 2. 遇到的主要问题：

图的连通性检测：

**删除顶点的性能问题：**

删除顶点时，若要删除所有有关这个顶点的边，则需要遍历整个图，性能较差。因此增加了一个顶点集合用用于记录被删除的点，并在程序运行过程中考虑这个集合。在顶点删除较少的情况下，这种方法不会给程序造成太大负担，只是当删除的顶点与现存的顶点规模相近时，各种算法会增加一个lgN的复杂度。

**用户交互界面：**

为了提供良好的用户体验，设计了简单易用的命令行界面，用户可以通过菜单选择不同的操作。实现过程中，确保每个操作的输入输出清晰明了，同时处理了用户可能的错误输入。

---

## 五、代码示例

### 1. Dijkstra算法实现

```C++
const std::list<string> Graph::GetShortestPath(const std::vector<Graph::VertexID> &pre, const string &vNam) const
{
    auto t = vS2ID.find(vNam);
    if(t == vS2ID.end() || deled.find(t->second) != deled.end())
        throw GraphException("Cannot find VertexID or be deleted");
    std::list<string> l;
    auto p = t->second;
    while(p != pre[p])
    {
        l.push_front(vNode[p].name);
        p = pre[p];
    }

    return l;
}
```

### 2. Prim算法实现
```C++
std::pair<std::vector<Graph::EdgeBiNode>, Graph::EdgeW> Graph::MinimunSpanningTree()
{
    EdgeW res = 0;
    VertexID s = 1;
    while(s <= n && deled.find(s) != deled.end())s++;
    std::priority_queue<std::pair<EdgeW, VertexID>, std::vector<std::pair<EdgeW, VertexID>>, DijkstraComp> q;
    std::vector<EdgeW> dis(n + 1, INF);
    std::vector<VertexID> pre(n + 1, 0);
    std::vector<EdgeBiNode> tre;
    dis[s] = 0;
    bool *vis = new bool[n + 1];
    for(int i = 1;i <= n;++i)vis[i] = false;
    q.push({0, s});
    while(!q.empty())
    {
        auto p = q.top();
        q.pop();
        if(vis[p.second])continue;
        vis[p.second] = true;
        res += p.first;
        tre.push_back(EdgeBiNode(vNode[pre[p.second]].name, vNode[p.second].name, p.first));
        for(auto &i:l[p.second])
        {
            if(!vis[i.to] && deled.find(i.to) == deled.end() && i.w < dis[i.to])
            {
                dis[i.to] = i.w;
                pre[i.to] = p.second;
                q.push({i.w, i.to});
            }
        }
    }
    delete[] vis;
    tre.erase(tre.begin());
    return {tre, res};
}
```

---

## 六、总结

通过本次实验，实现了一个基本的校园导航系统，能够进行节点和边的基本操作，计算最短路径，检测欧拉通路。在项目开发过程中，解决了图的连通性检测、数据加载与存储、用户交互界面设计等问题，提升了C++编程的综合能力。
