# ����
## ��������
### ��������
���Ի��з�<br />�˴�Ӧ���ѻ���
```C++
Graph(const V* vertexs, size_t n) {
	_vertexs.reserve(n);
	for (size_t i = 0; i < n; ++i)
	{
		_vertexs.push_back(vertexs[i]);
		_vIndexMap[vertexs[i]] = i;
	}

	// MAX_W ��Ϊ�����ڱߵı�ʶֵ
	_matrix.resize(n);
	for (auto& e : _matrix)
	{
		e.resize(n, MAX_W);
	}
}
```
## ͼƬ����
ͼƬ���ԣ�<br />
![test01.png](res/test.png)<br />
![test01](https://img1.baidu.com/it/u=2370595099,2069636640&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=800) <br />