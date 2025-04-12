# ʹ��Docker����������

[Docker_login_search��ʱ�����ܾ����ӵ�����](#Docker_login_search��ʱ�����ܾ����ӵ�����)

--- 

## Docker_login_search��ʱ�����ܾ����ӵ�����

### Docker������ȡpull

���ڹ�������������ƣ�docker �޷� pull��

�������취�ܼ򵥣�ֱ��ʹ�þ��񼴿ɣ��������£�

�� /etc/docker/ Ŀ¼�´� daemon.json ��û�оʹ������ļ���

��������Ӿ���Դ��

```
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "20m",
        "max-file": "3"
    },
    "registry-mirrors": [
      "https://docker.1ms.run",
      "https://docker.xuanyuan.me",
      "https://docker.wanpeng.life",
      "https://docker.imgdb.de",
      "https://dockerproxy.cn",
      "https://dockerproxy.com",
      "https://dockerpull.cn",
      "https://docker.1panel.live"
    ]
}

```

������ docker �������� pull��

### �޷�login_search

���˾���Դ����Ȼ�����������д󲿷ֲ���������Ȼ�в��������޷�ʹ�á�

�������£�����ͼƬ�е����ⶼ��������ʾ�����ģ���ԭ�����������룬��������˼�޶�����

![docker����������ʾ��.png](../res/0-title/docker����������ʾ��.png)

��search��loginʱ���ᱨ��connect: connection refused��

��ʹʹ�ô���Ҳû��Ч����

### ԭ��

Docker ���������úʹ���������������һ�£����� Docker search ����ʱ�޷����Լ����õķ�ǽ����

��Ȼ����Ҫʹ�ô����� daemon.json �ļ��Ͳ���Ҫ�ˣ�Ҳ������������⣬������԰� daemon.json ������Ϊ�������ƣ��������Ҫ������������

### ������

Ϊ Docker �ػ��������ô���ʹ�� `find / -name "docker.service"` �����ҵ����Ǽ������õ��ļ�����Ŀ¼���磺/usr/lib/systemd/system/docker.service����

- �� docker.service �ļ����ҵ� Service������������������ã�

    ```
    Environment="http_proxy=http://127.0.0.1:7890"
    Environment="https_proxy=http://127.0.0.1:7890"
    ```

    ![docker.service������ʾ.png](../res/0-title/docker.service������ʾ.png)

    ���ú�֮��Ҫ���¼��������ļ�, ʹ������ `systemctl daemon-reload` ���ɡ�

- ע��

    ����Ķ˿ں�7890��������������clash��������һ����7890û�д�

    ������ʹ������ip�磺192.xxx.xxx.xxx����Ȼ127.0.0.1�󲿷����Ҳ�������

����������ʹ��127.0.0.1������ʱʹ������ip���ᵼ�� Docker �ػ����������ڶ������������ռ䣬����������ֱ��ʹ�õ�����ip�����޷�ֱ�ӷ����������� 127.0.0.1��

- ������Ҫ�Դ����߽������ã���clashΪ������
    
    ![clash������ʾ.png](../res/0-title/clash������ʾ.png)

    ������������ӣ��� Clash �����п��� Allow LAN

֮��Ϳ�������ʹ��docker�ˡ�

![dockerʹ�óɹ���ʾ1.png](../res/0-title/dockerʹ�óɹ���ʾ1.png)

![dockerʹ�óɹ���ʾ2.png](../res/0-title/dockerʹ�óɹ���ʾ2.png)