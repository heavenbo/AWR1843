# 1. mmWave Studio 无法加载 MATLAB Runtime DLL

## 报错信息

```text
[RadarAPI]: Error: Error occured in Matlab Initialization
[RadarAPI]: Matlab Engine couldn't be Started..

***Script FAILED!***
Exception message is:
无法加载 DLL“mclmcrrt8_5_1.dll”: 找不到指定的模块。
(异常来自 HRESULT:0x8007007E)。
```

## 原因

本机已安装 **MATLAB Runtime 8.5.1（32 位）**，DLL 文件也存在。Windows 系统 `Path` 中，MATLAB Runtime 和 Ghostscript 的目录之间少了一个分号，导致两个目录粘成一条无效路径：

```text
C:\Program Files (x86)\MATLAB\MATLAB Runtime\v851\runtime\win32C:\Program Files\gs\gs10.07.0\bin
```

## 解决方法

1. 按 **Win + R**，输入 `sysdm.cpl`，回车。
2. 打开 **高级 → 环境变量 → 系统变量 → Path → 编辑**。
3. 找到上述粘连路径，拆成两条独立记录：

   ```text
   C:\Program Files (x86)\MATLAB\MATLAB Runtime\v851\runtime\win32
   C:\Program Files\gs\gs10.07.0\bin
   ```

4. 保存后完全退出并重新启动 mmWave Studio。

## 验证

新开一个命令提示符（CMD），运行：

```cmd
where mclmcrrt8_5_1.dll
```

确认返回 MATLAB Runtime `v851\runtime\win32` 下的 DLL 路径，并检查 mmWave Studio 的 MATLAB 后处理引擎能否正常启动。
