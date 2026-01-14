---
share: true
---

# Docker 安装配置指南
---
## 一、Docker Desktop 安装

### 前置要求

- Windows 10/11 64位系统
- 在 BIOS 中启用虚拟化（见下方说明）
- 已安装 WSL 2（推荐）

### 检查虚拟化是否已开启

打开任务管理器 → 性能 → CPU，查看右下角"虚拟化"状态：
- 显示"已启用"：无需操作
- 显示"已禁用"：需要进入 BIOS 开启

### 在 BIOS 中开启虚拟化

#### 步骤一：进入 BIOS

**方法 A：通过 Windows 设置**
1. 打开 设置 → 系统 → 恢复
2. 点击"高级启动"下的"立即重新启动"
3. 选择 疑难解答 → 高级选项 → UEFI 固件设置
4. 点击"重启"

**方法 B：开机时按快捷键**
- 重启电脑，在开机 Logo 出现时连续按对应按键：
  - **联想/ThinkPad**：F1 或 F2
  - **戴尔**：F2
  - **惠普**：F10 或 Esc
  - **华硕**：F2 或 Del
  - **宏碁**：F2 或 Del
  - **微星**：Del
  - **小米/红米**：F2

#### 步骤二：找到虚拟化选项

不同品牌 BIOS 界面不同，虚拟化选项通常在以下位置：

| 品牌 | 路径 | 选项名称 |
|------|------|----------|
| Intel CPU | Advanced → CPU Configuration | Intel Virtualization Technology / VT-x |
| AMD CPU | Advanced → CPU Configuration | SVM Mode / AMD-V |
| 联想 | Security → Virtualization | Intel VT / VT-d |
| 戴尔 | Virtualization Support | Enable Virtualization |
| 惠普 | System Configuration | Virtualization Technology |

#### 步骤三：启用并保存

1. 将虚拟化选项设置为 **Enabled**
2. 按 **F10** 保存并退出（或选择 Save & Exit）
3. 电脑会自动重启

#### 步骤四：验证

重启后打开任务管理器 → 性能 → CPU，确认"虚拟化"显示为"已启用"

### 安装步骤

#### 1. 下载 Docker Desktop

访问 Docker 官网下载页面：https://www.docker.com/products/docker-desktop/

下载 **Docker Desktop for Windows**。

#### 2. 运行安装程序

- 双击下载的安装文件
- 勾选 "Use WSL 2 instead of Hyper-V"（推荐）
- 按提示完成安装

#### 3. 重启电脑

安装完成后需要重启电脑。

#### 4. 启动 Docker Desktop

- 从开始菜单启动 Docker Desktop
- 等待 Docker 引擎初始化完成（托盘图标变为稳定状态）

#### 5. 验证安装

打开终端（PowerShell 或 CMD），运行：

```powershell
docker --version
docker run hello-world
```

如果看到版本号和 "Hello from Docker!" 消息，说明安装成功。

---



## 二、常见问题

### Docker 启动失败

如果 Docker Desktop 启动时报错，检查：

1. WSL 2 是否正常安装，如果没有正确安装请参考下文
2. 虚拟化是否在 BIOS 中启用，参考上文
3. Hyper-V 或 WSL 功能是否开启

### 镜像拉取慢

如果拉取镜像很慢，可以配置国内镜像源：

1. 打开 Docker Desktop → Settings → Docker Engine
2. 添加镜像加速地址：

```json
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub-mirror.c.163.com"
  ]
}
```

3. 点击 "Apply & Restart"

---

## 三、验证安装

```powershell
# 验证 WSL
wsl --version
wsl -l -v

# 验证 Docker
docker --version
docker run hello-world
```

如果都正常输出，说明环境配置成功！

---
## 四、WSL 安装损坏修复

当遇到以下错误时：

```
WSL 安装似乎已损坏 (错误代码： Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG)
已禁止(403)。错误代码: Wsl/CallMsi/Install/0x80190193
```

可以尝试以下方法修复。

### 方法一：手动下载安装 WSL

1. 以管理员身份打开 PowerShell，先卸载损坏的 WSL：

```powershell
wsl --unregister Ubuntu
wsl --shutdown
```

2. 从微软官方手动下载 WSL 更新包：
   - 访问：https://aka.ms/wsl2kernel
   - 下载并运行 `wsl_update_x64.msi`

3. 重新安装 WSL：

```powershell
wsl --install --no-distribution
```

### 方法二：启用 Windows 功能后重装

1. 以管理员身份打开 PowerShell，启用必要功能：

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

2. **重启电脑**

3. 重启后再运行：

```powershell
wsl --set-default-version 2
wsl --install -d Ubuntu
```

### 方法三：解决网络/代理问题（403 错误）

403 错误通常是网络限制导致，尝试：

- 关闭 VPN 或代理软件
- 切换网络（如使用手机热点）
- 从 Microsoft Store 直接搜索安装 "Ubuntu" 或其他 Linux 发行版


## 五、常用 Docker 命令

```powershell
# 查看运行中的容器
docker ps

# 查看所有容器
docker ps -a

# 查看本地镜像
docker images

# 拉取镜像
docker pull <镜像名>

# 运行容器
docker run -d -p 8080:80 <镜像名>

# 停止容器
docker stop <容器ID>

# 删除容器
docker rm <容器ID>

# 删除镜像
docker rmi <镜像ID>
```
