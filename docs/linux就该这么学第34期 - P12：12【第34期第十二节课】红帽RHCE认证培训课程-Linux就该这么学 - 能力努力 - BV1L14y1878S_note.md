# Linux就该这么学：第34期：第12节课：防火墙配置与管理 🔥

![](img/d4a08906e8829452e5f1aa952ec86ac3_1.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_3.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_5.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_7.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_9.png)

在本节课中，我们将学习Linux系统中防火墙的配置与管理。防火墙是保护系统安全的重要工具，它能够控制网络流量的进出，防止未经授权的访问。我们将介绍四种配置防火墙的方法，并讲解相关的核心概念，帮助初学者理解和掌握防火墙的基本操作。

![](img/d4a08906e8829452e5f1aa952ec86ac3_11.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_13.png)

---

![](img/d4a08906e8829452e5f1aa952ec86ac3_15.png)

## 网络配置基础 🌐

在配置防火墙之前，我们需要确保虚拟机与物理机之间的网络能够正常通信。本节将介绍如何配置网卡，以便后续的防火墙实验能够顺利进行。

![](img/d4a08906e8829452e5f1aa952ec86ac3_17.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_19.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_21.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_23.png)

### 网卡配置方法

![](img/d4a08906e8829452e5f1aa952ec86ac3_25.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_27.png)

以下是四种配置网卡的方法，您可以根据自己的喜好选择其中一种进行配置。

![](img/d4a08906e8829452e5f1aa952ec86ac3_29.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_31.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_33.png)

#### 方法一：通过配置文件修改网卡参数

![](img/d4a08906e8829452e5f1aa952ec86ac3_35.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_37.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_39.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_41.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_43.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_45.png)

1. 找到网卡配置文件路径：`/etc/sysconfig/network-scripts/`。
2. 编辑对应的网卡配置文件（例如 `ifcfg-ens160`）。
3. 修改 `IPADDR` 参数为所需的IP地址（例如 `192.168.10.20`）。
4. 保存并退出文件。
5. 重启网卡服务使配置生效：
   ```bash
   nmcli connection reload
   nmcli connection up ens160
   ```

![](img/d4a08906e8829452e5f1aa952ec86ac3_47.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_49.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_51.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_53.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_55.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_57.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_59.png)

#### 方法二：使用命令行工具 `nmtui`

![](img/d4a08906e8829452e5f1aa952ec86ac3_61.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_63.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_65.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_67.png)

1. 运行命令 `nmtui` 打开网络配置界面。
2. 选择“编辑连接”选项，进入网卡编辑界面。
3. 修改IP地址为所需的值（例如 `192.168.10.30`）。
4. 保存并退出界面。
5. 启用网卡：
   ```bash
   nmcli connection up ens160
   ```

![](img/d4a08906e8829452e5f1aa952ec86ac3_69.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_71.png)

#### 方法三：使用图形化工具 `nm-connection-editor`

![](img/d4a08906e8829452e5f1aa952ec86ac3_73.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_75.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_77.png)

1. 运行命令 `nm-connection-editor` 打开图形化配置界面。
2. 双击要配置的网卡，进入编辑界面。
3. 在“IPv4设置”中修改IP地址（例如 `192.168.10.40`）。
4. 保存并关闭界面。
5. 启用网卡：
   ```bash
   nmcli connection up ens160
   ```

![](img/d4a08906e8829452e5f1aa952ec86ac3_79.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_81.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_83.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_85.png)

#### 方法四：通过图形界面小工具配置

![](img/d4a08906e8829452e5f1aa952ec86ac3_87.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_89.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_91.png)

1. 点击系统右上角的网络图标，选择“有线设置”。
2. 进入网络设置界面，点击齿轮图标编辑网卡。
3. 在“IPv4”选项卡中修改IP地址（例如 `192.168.10.50`）。
4. 点击“应用”保存配置。
5. 关闭并重新启用网卡。

---

## 防火墙基础理论 🛡️

防火墙是保护系统安全的重要工具，它通过控制网络流量的进出，防止未经授权的访问。本节将介绍防火墙的基本概念和核心理论。

### 防火墙的作用

防火墙类似于防盗门，主要用于保护内部网络的安全。它可以控制流量的方向，包括输入流量（Input）、输出流量（Output）和转发流量（Forward）。

![](img/d4a08906e8829452e5f1aa952ec86ac3_93.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_95.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_97.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_99.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_101.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_103.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_105.png)

### 流量控制方法

![](img/d4a08906e8829452e5f1aa952ec86ac3_107.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_109.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_111.png)

当防火墙匹配到流量时，有以下四种处理方法：

![](img/d4a08906e8829452e5f1aa952ec86ac3_113.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_115.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_117.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_119.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_121.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_122.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_124.png)

1. **允许（ACCEPT）**：放行流量，允许其通过防火墙。
2. **拒绝（REJECT）**：拒绝流量，并向发送方返回拒绝信息。
3. **丢弃（DROP）**：丢弃流量，不向发送方返回任何信息。
4. **记录（LOG）**：记录流量信息，保存到系统日志中。

![](img/d4a08906e8829452e5f1aa952ec86ac3_126.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_128.png)

### 规则链与匹配原则

![](img/d4a08906e8829452e5f1aa952ec86ac3_130.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_132.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_134.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_136.png)

防火墙规则按照规则链（Chain）组织，常见的规则链包括：
- **INPUT**：处理输入流量。
- **OUTPUT**：处理输出流量。
- **FORWARD**：处理转发流量。

![](img/d4a08906e8829452e5f1aa952ec86ac3_138.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_140.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_142.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_144.png)

防火墙规则的匹配原则：
1. 从上到下依次匹配。
2. 匹配到即停止，后续规则不再生效。

![](img/d4a08906e8829452e5f1aa952ec86ac3_146.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_148.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_150.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_152.png)

---

![](img/d4a08906e8829452e5f1aa952ec86ac3_154.png)

## 防火墙配置方法 🔧

![](img/d4a08906e8829452e5f1aa952ec86ac3_156.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_158.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_160.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_162.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_164.png)

本节将介绍四种配置防火墙的方法，包括 `iptables`、`firewall-cmd`、`firewall-config` 和 `cockpit`。您可以根据需求选择其中一种进行配置。

![](img/d4a08906e8829452e5f1aa952ec86ac3_166.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_168.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_170.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_172.png)

### 方法一：使用 `iptables`

![](img/d4a08906e8829452e5f1aa952ec86ac3_174.png)

`iptables` 是Linux系统中传统的防火墙配置工具，适用于老版本系统（如Red Hat 5/6）。

![](img/d4a08906e8829452e5f1aa952ec86ac3_176.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_178.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_180.png)

#### 常用命令

![](img/d4a08906e8829452e5f1aa952ec86ac3_182.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_184.png)

1. 查看现有规则：
   ```bash
   iptables -L
   ```
2. 清空所有规则：
   ```bash
   iptables -F
   ```
3. 设置默认策略：
   ```bash
   iptables -P INPUT DROP   # 默认拒绝所有输入流量
   iptables -P INPUT ACCEPT # 默认允许所有输入流量
   ```
4. 添加规则：
   ```bash
   iptables -A INPUT -s 192.168.10.0/24 -j ACCEPT   # 允许特定网段访问
   iptables -I INPUT -s 192.168.10.1 -j REJECT      # 拒绝特定IP访问
   ```
5. 删除规则：
   ```bash
   iptables -D INPUT 1   # 删除第一条规则
   ```
6. 保存规则：
   ```bash
   service iptables save
   ```

![](img/d4a08906e8829452e5f1aa952ec86ac3_186.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_188.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_190.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_192.png)

### 方法二：使用 `firewall-cmd`

![](img/d4a08906e8829452e5f1aa952ec86ac3_194.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_196.png)

`firewall-cmd` 是Red Hat 7/8系统中推荐的防火墙配置工具，支持区域（Zone）管理。

![](img/d4a08906e8829452e5f1aa952ec86ac3_198.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_200.png)

#### 常用命令

![](img/d4a08906e8829452e5f1aa952ec86ac3_202.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_204.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_206.png)

1. 查看默认区域：
   ```bash
   firewall-cmd --get-default-zone
   ```
2. 设置默认区域：
   ```bash
   firewall-cmd --set-default-zone=public
   ```
3. 添加允许的服务：
   ```bash
   firewall-cmd --permanent --add-service=ssh
   firewall-cmd --reload
   ```
4. 添加允许的端口：
   ```bash
   firewall-cmd --permanent --add-port=80/tcp
   firewall-cmd --reload
   ```
5. 移除服务或端口：
   ```bash
   firewall-cmd --permanent --remove-service=ssh
   firewall-cmd --permanent --remove-port=80/tcp
   firewall-cmd --reload
   ```
6. 启用紧急模式（切断所有连接）：
   ```bash
   firewall-cmd --panic-on
   ```
7. 禁用紧急模式：
   ```bash
   firewall-cmd --panic-off
   ```

![](img/d4a08906e8829452e5f1aa952ec86ac3_208.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_210.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_212.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_214.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_216.png)

### 方法三：使用 `firewall-config`

![](img/d4a08906e8829452e5f1aa952ec86ac3_218.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_220.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_222.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_224.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_226.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_228.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_230.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_232.png)

`firewall-config` 是图形化界面的防火墙配置工具，适合初学者使用。通过界面操作可以直观地配置防火墙规则，与 `firewall-cmd` 实时同步。

![](img/d4a08906e8829452e5f1aa952ec86ac3_234.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_236.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_238.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_240.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_242.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_244.png)

### 方法四：使用 `cockpit`

![](img/d4a08906e8829452e5f1aa952ec86ac3_246.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_248.png)

`cockpit` 是一个基于Web的系统管理工具，可以通过图形界面配置防火墙及其他系统参数。它是一个“兜底”工具，适合在命令行配置困难时使用。

![](img/d4a08906e8829452e5f1aa952ec86ac3_250.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_252.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_254.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_256.png)

---

![](img/d4a08906e8829452e5f1aa952ec86ac3_258.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_260.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_262.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_264.png)

## 高级配置技巧 🚀

![](img/d4a08906e8829452e5f1aa952ec86ac3_266.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_268.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_270.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_272.png)

本节将介绍防火墙的一些高级配置技巧，包括端口转发和复杂规则（Rich Rule）的使用。

![](img/d4a08906e8829452e5f1aa952ec86ac3_274.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_276.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_278.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_280.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_282.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_284.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_286.png)

### 端口转发

![](img/d4a08906e8829452e5f1aa952ec86ac3_288.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_290.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_292.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_294.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_296.png)

端口转发可以将外部访问的流量转发到内部指定的端口，例如将外部访问的888端口转发到22端口（SSH服务）：
```bash
firewall-cmd --permanent --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10
firewall-cmd --reload
```

### 复杂规则（Rich Rule）

![](img/d4a08906e8829452e5f1aa952ec86ac3_298.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_300.png)

复杂规则允许更精细的流量控制，例如仅允许特定IP访问SSH服务：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.10.1" service name="ssh" accept'
firewall-cmd --reload
```

---

![](img/d4a08906e8829452e5f1aa952ec86ac3_302.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_304.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_306.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_308.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_310.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_312.png)

## 总结 📚

![](img/d4a08906e8829452e5f1aa952ec86ac3_314.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_316.png)

在本节课中，我们一起学习了Linux系统中防火墙的配置与管理。主要内容包括：
1. 网络配置的基础知识，确保虚拟机与物理机之间的网络互通。
2. 防火墙的基本理论，包括流量控制方法和规则链的匹配原则。
3. 四种配置防火墙的方法：`iptables`、`firewall-cmd`、`firewall-config` 和 `cockpit`。
4. 高级配置技巧，如端口转发和复杂规则的使用。

![](img/d4a08906e8829452e5f1aa952ec86ac3_318.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_320.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_322.png)

![](img/d4a08906e8829452e5f1aa952ec86ac3_324.png)

通过本节课的学习，您应该能够根据实际需求选择合适的防火墙配置方法，并掌握基本的防火墙管理技能。防火墙是系统安全的重要组成部分，希望您能在实践中不断巩固和提升相关技能。