# 学习记录 8-4

### 树形结构转平铺

- 循环结合递归实现树形结构平铺，该方法接受两个参数，data 为要遍历的树形结构，list 为返回的数据。遍历该树形结构，遍历当前节点并将该节点放入 list 中，然后判断该节点是否存在子节点，若存在则递归进行下一轮遍历，直到该节点不存在子节点时终止递归。
- reduce 结合递归实现树形结构平铺，接受一个参数 data 为树形数据，利用 reduce 的特性，初始化 reduce 的返回值是一个空数组，利用该方法的 previewValue(result)和 currentValue(node),将每轮遍历的 currentValue 保存至 previewValue 中，若该节点存在子节点，则利用递归的方式将数据存到 previewValue，直到递归结束，返回一个包含所有节点的一维数组。

```
//通过循环递归
const treeToArr = (data, list) => {
  data.forEach((item) => {
    list.push(item);
    item.children?.length > 0 && treeToArr(item.children, list);
  });
  return list;
};

const list = [];
treeToArr(data, list);

//通过reduce()方法递归
const flattenTree = (tree) => {
  return tree.reduce((result, node) => {
    result.push(node);
    node.children?.length>0 && result.push(...flattenTree(node.children));
    return result;
  }, []);
};

const list = flattenTree(data);
```

### 平铺数据转树形结构

- 递归实现树形结构，通过匹配数组的每一项的 parent id 来实现树形结构的转换。在初始化时，定义该树形结构的根节点为 null，然后通过匹配 pid 是否相等，若相等则生成一个节点对象 node，该节点内部的 children 是通过递归的方式向下传递上一级的 id，在数组下一轮遍历的时候将 pid 与上一级的 id 进行匹配，相等则为该节点的子节点。

```
const arrToTree = (arr, pid = null) => {
  const res = [];
  arr.forEach((item) => {
    if (item.pid === pid) {
      const node = {
        id: item.id,
        children: arrToTree(arr,item.id),
      };
      res.push(node);
    }
  });
  return res;
};
const tree = arrToTree(arr, null);
```
