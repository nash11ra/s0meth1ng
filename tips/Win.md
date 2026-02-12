# Windows相关
## 更新时间 2026.02.12

### 1. 解决 Win10 远程桌面连接的错误提示

背景：Windows 10通过系统自带的`mstsc远程桌面`连接到另外一台Windows 10时候总是弹出`无法定位程序输入点_CxxFrameHandler4 于动态链接库`，以及缺失`FXSAPI.DLL`，`FXSTIFF.DLL`，`FXSUI.DLL`错误弹窗，每次远程连接都有

解决方法：也许和打印机相关的驱动有关，我尝试关闭连接时的`本地资源`选项卡中的`打印机`复选框之后，似乎就不怎么弹窗了

### 2. 使用Dism修复系统与错误

背景：在某次 Windows 10 升级 Windows 11 的过程中，回退至Windows 10时，有些软件dll缺失错误，需要进行修复，搜索一番使用命令行修复

修复命令的来源：[知乎](https://zhuanlan.zhihu.com/p/616060244)

回答摘录如下：

#### 首先以管理员身份运行命令提示符，并执行以下步骤：

#### 步骤1：扫描全部系统文件并和官方系统文件对比指令：
```
DISM /Online /Cleanup-image /Scanhealth
```
#### 步骤2：如果步骤1提示存在问题，则需要跳过步骤2，直接执行步骤3；否则，继续执行指令：
```
sfc /scannow
```
此时可以直接结束，不再执行后续步骤3，4，5。
#### 步骤3：执行检测系统映像指令：
```
DISM /Online /Cleanup-image /Checkhealth
```
#### 步骤4：执行修复系统映像指令，即把系统文件还原成系统的官方源文件：
```
DISM /Online /Cleanup-image /Restorehealth
```
#### 步骤5：修复完成后，再次检查系统，执行步骤2的指令并结束。
```
sfc /scannow
```



#### 在修复过程中，执行了上述步骤4，可能出现如下错误代码：0x800f081f



解决方案：

#### 1.下载MediaCreationTool工具，创建跟当前系统相同版本的镜像文件（比如我的电脑是Windows 10 20H2版本，应下载版本为：MediaCreationTool20H2.exe）。

#### 2. 双击MediaCreationTool20H2.exe文件进行安装，接受协议——>选择为另一台电脑创建安装介质(U盘、DVD或ISO文件)——>选择要使用的介质（ISO文件）——>将ISO文件放在C盘（或者其他盘符）目录下。

#### 3. 双击进入刚刚下载的.iso文件，复制子目录sources中的install.esd文件路径，执行下列指令后生成install.wim文件到C盘（或其他盘符，自行指定）根目录下：
```
DISM /Export-Image /SourceImageFile:F:\sources\install.esd /SourceIndex:1 /DestinationImageFile:C:\install.wim /Compress:Max /CheckIntegrity
```
#### 4. 在C盘（或其他盘符）根目录下创建一个文件夹，命名为REP（或其他名称）
```
ATTRIB C:\install.wim -R
```
#### 5.然后执行指令：
```
DISM.exe /Mount-Image /ImageFile:C:\install.wim /Index:1 /MountDir:C:\REP
```
#### 6. 经步骤4载入install.wim文件后，继续执行指令：
```
DISM /Online /Cleanup-image /RestoreHealth /Source:C:\REP\windows /Limitaccess
```
#### 7. 此时再次执行文章开头的步骤4，步骤5指令，等待系统还原成功【说明Dism修复完成】。
#### 8. 安装映像操作成功完成后，可以卸载之前已经安装的镜像文件，节省磁盘空间。
```
DISM /Unmount-Image /MountDir:C:\REP /Discard
```
#### 9. 卸载操作成功完成后，就可以删除C盘根目录下的install.wim文件和REP文件夹
#### 10. 当然有可能上述操作仍然不奏效
  我就遇到了这种情况，一般Windows更新会有10天的反悔期，在升级时会产生一个WindowsOld.wim的用于回退的压缩镜像，可以利用这个镜像，利用第4步到第6步重新操作一遍，就不会报0x800f081f的错误了

  但是采用`sfc /scannow`,仍然会报无法修复的错误

  在微软官方[帮助文档](https://support.microsoft.com/zh-cn/topic/%E4%BD%BF%E7%94%A8%E7%B3%BB%E7%BB%9F%E6%96%87%E4%BB%B6%E6%A3%80%E6%9F%A5%E5%99%A8%E5%B7%A5%E5%85%B7%E4%BF%AE%E5%A4%8D%E4%B8%A2%E5%A4%B1%E6%88%96%E6%8D%9F%E5%9D%8F%E7%9A%84%E7%B3%BB%E7%BB%9F%E6%96%87%E4%BB%B6-79aa86cb-ca52-166a-92a3-966e85d4094e#:~:text=sfc%20%2Fscannow%20%E5%91%BD%E4%BB%A4%E5%B0%86%E6%89%AB%E6%8F%8F%E6%89%80%E6%9C%89%E5%8F%97%E4%BF%9D%E6%8A%A4%E7%9A%84%E7%B3%BB%E7%BB%9F%E6%96%87%E4%BB%B6%EF%BC%8C%E5%B9%B6%E5%B0%86%E6%8D%9F%E5%9D%8F%E7%9A%84%E6%96%87%E4%BB%B6%E6%9B%BF%E6%8D%A2%E4%B8%BA%E7%BC%93%E5%AD%98%E7%9A%84%E5%89%AF%E6%9C%AC%E3%80%82%20%E6%AD%A4%E8%BF%87%E7%A8%8B%E5%AE%8C%E6%88%90%E5%90%8E%EF%BC%8C%E5%B0%86%E6%98%BE%E7%A4%BA%E6%89%AB%E6%8F%8F%E7%BB%93%E6%9E%9C%E3%80%82%20%E4%BD%A0%E5%8F%AF%E8%83%BD%E4%BC%9A%E6%94%B6%E5%88%B0%E4%BB%A5%E4%B8%8B%E6%B6%88%E6%81%AF%E4%B9%8B%E4%B8%80%EF%BC%9A%20Windows,%E8%B5%84%E6%BA%90%E4%BF%9D%E6%8A%A4%E6%89%BE%E4%B8%8D%E5%88%B0%E4%BB%BB%E4%BD%95%E5%AE%8C%E6%95%B4%E6%80%A7%E5%86%B2%E7%AA%81%E3%80%82%20%E6%B2%A1%E6%9C%89%E4%BB%BB%E4%BD%95%E4%B8%A2%E5%A4%B1%E6%88%96%E6%8D%9F%E5%9D%8F%E7%9A%84%E7%B3%BB%E7%BB%9F%E6%96%87%E4%BB%B6%E3%80%82%20Windows%20%E8%B5%84%E6%BA%90%E4%BF%9D%E6%8A%A4%E6%97%A0%E6%B3%95%E6%89%A7%E8%A1%8C%E8%AF%B7%E6%B1%82%E7%9A%84%E6%93%8D%E4%BD%9C%E3%80%82%20%E8%8B%A5%E8%A6%81%E8%A7%A3%E5%86%B3%E6%AD%A4%E9%97%AE%E9%A2%98%EF%BC%8C%E8%AF%B7%E5%9C%A8%20%E5%AE%89%E5%85%A8%E6%A8%A1%E5%BC%8F%E4%B8%8B%20%E6%89%A7%E8%A1%8C%E7%B3%BB%E7%BB%9F%E6%96%87%E4%BB%B6%E6%A3%80%E6%9F%A5%E5%99%A8%E6%89%AB%E6%8F%8F%E3%80%82)最底部的更多信息中，可以发现一条命令`findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log >"%userprofile%\Desktop\sfcdetails.txt"`，用于查找那些无法修复的文件名，我这里查出来大多是微软商店里某些app的dll文件hash不对，但也没有什么大的报错，索性也就不管了。

#### 修复好之后，我也遇到了频繁通知 “A字“出现了问题，无法安装功能”

这是由于输入法发生故障引起的，可参看微软支持社区回答底下的[张以民的回答](https://answers.microsoft.com/zh-hans/windows/forum/all/%E9%A2%91%E7%B9%81%E9%80%9A%E7%9F%A5a%E5%AD%97/804fac73-6909-4fc9-9239-5f445302ba67)

答案摘录如下：

该问题可能是2-3月的更新（猜测是KB5053606或KB5053643）不知何故卸载了可选功能“简体中文补充字体”，而1909以后的Win10又不支持在线添加FOD（按需功能）导致的。

这个BUG会导致 等线（Deng.ttf/Dengb.ttf/Dengl.ttf）、仿宋（simfang.ttf）、楷体（simkai.ttf）、黑体（simhei.ttf）4个中文字体、共6个ttf文件缺失。

#### 解决办法一：

下载适用于Win10 22H2 x64的FOD光盘（4.87GB），[地址](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso) 。双击挂载，假设挂载的盘符为F；

以管理员方式运行CMD，执行如下命令（注意：/Source:后面接的是FOD光盘盘符）：
```
dism /Online /add-capability /CapabilityName:Language.Fonts.Hans~~~und-HANS~0.0.1.0 /Source:F:
```
如果提示“错误: 1168 找不到元素”，请使用解决办法二的命令。

#### 解决办法二：

下载提取的简体中文补充字体安装包（42MB），[123盘地址](https://www.123865.com/s/m7HUVv-gkSf3) ，假设下载保存为：E:\Downloads\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab；

以管理员方式运行CMD，执行如下命令（注意：/PackagePath:后面接的是CAB安装包的实际路径）：
```
dism /Online /Add-Package /PackagePath:"E:\Downloads\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package_31bf3856ad364e35_amd64__.cab"
```
完成后重启计算机。设置/系统/可选功能，下拉列表到最后，检查“简体中文补充字体”是否存在。

注：在官方补丁出来之前建议暂停Windows更新，因为截至目前有些后续补丁依旧会删除这个FOD。

### 3. Listary

在当前目录用Everything搜索的命令：`关键字`可设置为`ev`，`路径`为Everything的路径:`D:\Everything\Everything.exe`，`参数`为`-p "{current_folder}" -s {query}`
