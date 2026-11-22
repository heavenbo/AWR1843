# mmWave Studio 无法加载 MATLAB Runtime DLL

## 报错信息

启动 mmWave Studio 时，MATLAB 后处理引擎初始化失败，随后启动脚本报错：

```text
[RadarAPI]: Error: Error occured in Matlab Initialization
[RadarAPI]: Matlab Engine couldn't be Started..

***Script FAILED!***
Exception message is:
无法加载 DLL“mclmcrrt8_5_1.dll”: 找不到指定的模块。
(异常来自 HRESULT:0x8007007E)。
```

日志中还出现了「未将对象引用设置到对象的实例」。该错误发生在后处理引擎初始化失败之后，应先处理 DLL 加载问题。

## 原因

本机已经安装 **MATLAB Runtime 8.5.1（32 位）**，`mclmcrrt8_5_1.dll` 文件也存在。但 Windows 系统 `Path` 中，MATLAB Runtime 和 Ghostscript 两个目录之间缺少分号，导致 Windows 把它们当成一条无效路径：

```text
C:\Program Files (x86)\MATLAB\MATLAB Runtime\v851\runtime\win32C:\Program Files\gs\gs10.07.0\bin
```

## 解决方法

1. 按 **Win + R**，输入 `sysdm.cpl` 并回车。
2. 进入 **高级 → 环境变量**。
3. 在下半部分的 **系统变量** 中选中 **Path → 编辑**。
4. 找到上述粘连的路径，将它拆成两条独立记录：

   ```text
   C:\Program Files (x86)\MATLAB\MATLAB Runtime\v851\runtime\win32
   C:\Program Files\gs\gs10.07.0\bin
   ```

5. 保存后完全退出 mmWave Studio，再重新启动。

## 验证

新开一个命令提示符（CMD），执行：

```cmd
where mclmcrrt8_5_1.dll
```

应返回类似下面的路径：

```text
C:\Program Files (x86)\MATLAB\MATLAB Runtime\v851\runtime\win32\mclmcrrt8_5_1.dll
```

最后确认 mmWave Studio 启动日志中不再出现 MATLAB 引擎初始化失败。此前使用的 `LaunchMmWaveStudio.cmd` 已验证可以临时补上 Runtime 路径并启动 Studio；上面的步骤用于永久修正系统 `Path`。
