# VSʹ��CMake����QT��Ŀ����������

[ͷ�ļ����ô���](#ͷ�ļ����ô���)

--- 

## ͷ�ļ����ô���

![QT��UI��ͷ�ļ����ô���ʾ��.png](../res/0-title/QT��UI��ͷ�ļ����ô���ʾ��.png)

��Ϊ�� QT Designer �б����ϰ���Ե������� MainInterface.ui

��ʵ���ϸ��ļ��������� XML ��ʽ��Ԫ���ݣ�����ֱ�ӿ�ִ�еı�����Դ��룬ֱ������ MainInterface.ui ��ͷ�ļ��ᷢ������

### �������

�� CMakeLists.txt �е��� `set()` ����� CMake ���ñ��� `CMAKE_AUTOUIC` ��ֵ��Ϊ `ON`������ UIC �Ĺ���ʱ�Զ������̣��� `set(CMAKE_AUTOUIC ON)`��

�����ͻ��Զ����� ui_MainInterface.h, ͨ���� `out\build\x64-Debug\Demo_autogen\include` Ŀ¼�С� 

����������ֱ�ӸĶ����� ui_MainInterface.h �ļ��� 

![QT��UIͷ�ļ�����ȷ����ʾ��.png](../res/0-title/QT��UIͷ�ļ�����ȷ����ʾ��.png)

ע�⣺ui_MainInterface.h �����ɿ�����Ҫһ�������������Ȼ�������Եȴ����ɡ�

