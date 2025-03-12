# 测试
## 二级标题
### 三级标题
测试换行符<br />此处应该已换行
```C++
Graph(const V* vertexs, size_t n) {
	_vertexs.reserve(n);
	for (size_t i = 0; i < n; ++i)
	{
		_vertexs.push_back(vertexs[i]);
		_vIndexMap[vertexs[i]] = i;
	}

	// MAX_W 作为不存在边的标识值
	_matrix.resize(n);
	for (auto& e : _matrix)
	{
		e.resize(n, MAX_W);
	}
}
```
## 图片测试
图片测试：<br />
![test01.png](res/test.png)<br />
![test01](https://img1.baidu.com/it/u=2370595099,2069636640&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=800) <br />