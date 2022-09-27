```c++
#include<iostream>
#include<Windows.h>
using namespace std;

HANDLE hSemaphore = CreateSemaphore(
    NULL,//指定安全属性，一般传入NULL
    1,//指定信号量对象的初始值。
    100,//信号量的最大值
    NULL//给出信号量的名字。
    );//当前1资源，运行100个同时访问。

DWORD WINAPI ThreadProc01(LPVOID lpParameter) {
 long count;
 WaitForSingleObject(hSemaphore, INFINITE);//hSemaphore空闲状态时，申请该信号量
 Sleep(1000);//子进程睡眠1秒
 cout << "第一个执行的进程为A进程" << endl;
 ReleaseSemaphore(
     hSemaphore, //信号量句柄
     1,//当前资源数加一
     &count);//返回当前资源计数的初始值
 return 0;
}

DWORD WINAPI ThreadProc02(LPVOID lpParameter) {
 long count;
 WaitForSingleObject(hSemaphore, INFINITE);
 Sleep(2000);
 cout << "随后执行的进程为B进程" << endl;
 ReleaseSemaphore(hSemaphore, 1, &count);
 return 0;
}

DWORD WINAPI ThreadProc03(LPVOID lpParameter) {
 long count;
 WaitForSingleObject(hSemaphore, INFINITE);
 Sleep(3000);
 cout << "最后执行的进程为C进程" << endl;
 ReleaseSemaphore(hSemaphore, 1, &count);
 return 0;
}

int main() {
 HANDLE handle1 = CreateThread(
    NULL,//为NULL表示句柄不能被子进程继承。
    0,//设置初始栈大小，以字节为单位，如果为0，那么默认将使用调用该函数的线程
    &ThreadProc01,//指向线程函数的指针。
    (void*)80, //向线程传递参数，是一个指向结构的指针，不需要传递参数时，为NULL
    0, //控制线程创建的标志，0表示创建后立即激活。
    NULL//保存新线程的ID，是指向线程ID的指针，如果为空，线程ID不返回。
    );//函数成功，返回线程句柄，否则返回NULL。
 HANDLE handle2 = CreateThread(NULL, 0, &ThreadProc02, (void*)80, 0, NULL);
 HANDLE handle3 = CreateThread(NULL, 0, &ThreadProc03, (void*)80, 0, NULL);

 CloseHandle(handle1);//信号量的清理与销毁。
 handle1 = NULL;
 CloseHandle(handle2);
 handle2 = NULL;
 CloseHandle(handle3);
 handle3 = NULL;

 system("pause");

 return 0;
}
```

