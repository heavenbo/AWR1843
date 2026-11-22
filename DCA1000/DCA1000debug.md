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

**# 2. mmWave Studio 无法加载 RadarLinkDLL.dll

## 报错信息

mmWave Studio 02.01.01.00 启动时，在 `ar1.selectRadarMode(0)` 处中止：

```text
[RadarAPI]: ar1.selectRadarMode(0)

***Script FAILED!***
Exception message is:
无法在 DLL“RadarLinkDLL.dll”中找到名为“RadarLinkImpl_setSystemType”的入口点。
```

将 `RunTime\RadarLinkDLL.dll` 临时改名为 `RadarLinkDLL.dll.bak`，并将 `Clients\AR1xController` 加入当前 CMD 的 `PATH` 后，报错变为：

```text
无法加载 DLL“RadarLinkDLL.dll”: 找不到指定的模块。
(异常来自 HRESULT:0x8007007E)。
```

## 原因

检查安装目录中两份同名 DLL 的导出表和导入表后确认：

- `Clients\AR1xController\RadarLinkDLL.dll` 提供 `RadarLinkImpl_setSystemType`，并依赖 `VCRUNTIME140.dll`。
- `RunTime\RadarLinkDLL.dll` 缺少该入口点。
- 故障发生时，在 `C:\Windows\SysWOW64` 和上述两个 DLL 所在目录中都未找到 `VCRUNTIME140.dll`。缺少 **32 位 Visual C++ 运行库**可以解释临时测试中的 `0x8007007E`。安装运行库后，临时启动和普通启动均成功。

## 解决方法

1. 完全关闭 mmWave Studio。
2. 安装或修复 **Microsoft Visual C++ v14 Redistributable（x86）**。即使 Windows 是 64 位，这个 32 位程序也需要 **x86** 版本。[微软官方下载](https://aka.ms/vc14/vc_redist.x86.exe)
3. 如果排查时将 `RunTime\RadarLinkDLL.dll` 改成了 `RadarLinkDLL.dll.bak`，将其恢复为原文件名。
4. 关闭曾临时修改 `PATH` 的 CMD 窗口，再双击 `RunTime\mmWaveStudio.exe` 正常启动。

## 验证

恢复 DLL 原名并关闭临时 CMD 窗口后，双击 `RunTime\mmWaveStudio.exe`。本机已验证启动通过 `ar1.selectRadarMode(0)`，未再出现入口点缺失或 `0x8007007E`。**
