# Linux系统管理：第8章：防火墙配置与管理 🔥

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_1.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_3.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_5.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_7.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_9.png)

在本节课中，我们将学习Linux系统中防火墙的配置与管理。防火墙是保护系统安全的重要工具，它能够控制网络流量的进出，防止未经授权的访问。我们将介绍四种配置防火墙的方法，并讲解相关的核心概念，帮助初学者理解和掌握防火墙的基本操作。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_11.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_13.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_15.png)

---

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_17.png)

## 网络配置前置条件 🌐

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_19.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_21.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_23.png)

在配置防火墙之前，我们需要确保虚拟机与物理机之间的网络能够正常通信。本节将介绍如何配置网卡，以便后续的防火墙实验能够顺利进行。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_25.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_27.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_29.png)

### 虚拟机网络模式选择

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_31.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_33.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_35.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_37.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_39.png)

虚拟机网络模式主要有三种：
1. **仅主机模式**：虚拟机之间及虚拟机与物理机之间可以通信，但不能访问外网。
2. **NAT模式**：虚拟机可以通过物理机的网络访问外网。
3. **桥接模式**：虚拟机直接使用物理机的网络方式访问外网。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_41.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_43.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_45.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_47.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_49.png)

为了实验需要，我们选择**仅主机模式**，确保虚拟机与物理机能够互通。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_51.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_53.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_55.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_57.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_59.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_61.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_63.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_65.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_67.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_69.png)

### 配置物理机网卡

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_71.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_73.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_75.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_77.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_79.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_81.png)

1. 打开物理机的网络设置，找到虚拟网卡（如VMnet1）。
2. 配置IP地址为`192.168.10.1`，子网掩码为`24`（或`255.255.255.0`）。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_83.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_85.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_87.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_89.png)

### 配置虚拟机网卡

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_91.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_93.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_95.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_97.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_99.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_101.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_103.png)

虚拟机网卡的配置有四种方法，我们将从难到易依次介绍。您只需选择其中一种方法即可。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_105.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_107.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_109.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_111.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_113.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_115.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_117.png)

#### 方法一：修改配置文件
1. 找到网卡配置文件路径：`/etc/sysconfig/network-scripts/`。
2. 编辑对应的网卡配置文件（如`ifcfg-ens160`）。
3. 修改IP地址为`192.168.10.20`，子网掩码为`24`。
4. 重启网卡服务使配置生效：
   ```bash
   nmcli connection reload
   nmcli connection up ens160
   ```

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_119.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_121.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_123.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_125.png)

#### 方法二：使用命令行工具
1. 运行命令`nmtui`，进入类图形化配置界面。
2. 选择“编辑连接”，修改IP地址为`192.168.10.30`，子网掩码为`24`。
3. 启用网卡连接。

#### 方法三：使用图形化界面工具
1. 运行命令`nm-connection-editor`，打开图形化配置界面。
2. 双击网卡，修改IP地址为`192.168.10.40`，子网掩码为`24`。
3. 保存并启用网卡。

#### 方法四：通过系统图形界面
1. 在虚拟机图形界面中，点击网络图标，进入网络设置。
2. 修改IP地址为`192.168.10.50`，子网掩码为`24`。
3. 启用网卡连接。

完成以上步骤后，物理机与虚拟机之间应能正常通信。

---

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_127.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_129.png)

## 防火墙基础理论 🛡️

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_131.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_133.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_135.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_137.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_139.png)

防火墙的作用是保护内网安全，控制流量的进出。本节将介绍防火墙的核心概念，包括规则链、匹配规则和默认策略。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_141.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_143.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_145.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_147.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_149.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_151.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_153.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_155.png)

### 规则链
防火墙规则链分为五种：
1. **INPUT链**：处理流入的流量。
2. **OUTPUT链**：处理流出的流量。
3. **FORWARD链**：处理转发的流量。
4. **PREROUTING链**：路由前处理。
5. **POSTROUTING链**：路由后处理。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_157.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_159.png)

### 匹配规则
防火墙规则从上到下依次匹配，匹配到即停止。因此，应将更精确的规则放在前面。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_161.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_163.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_165.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_167.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_169.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_171.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_173.png)

### 默认策略
默认策略分为两种：
1. **允许所有，拒绝特定**：默认允许所有流量，仅拒绝特定流量。
2. **拒绝所有，允许特定**：默认拒绝所有流量，仅允许特定流量。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_175.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_177.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_179.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_181.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_183.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_185.png)

后者更安全，但配置更复杂。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_187.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_189.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_191.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_193.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_195.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_197.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_199.png)

---

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_201.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_203.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_205.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_207.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_209.png)

## 防火墙配置方法 🔧

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_211.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_213.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_215.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_217.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_219.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_221.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_223.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_225.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_227.png)

我们将介绍四种配置防火墙的方法，您可以选择其中一种进行配置。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_229.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_231.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_233.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_235.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_237.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_239.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_241.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_243.png)

### 方法一：使用iptables
`iptables`是Linux内核级别的防火墙工具，适用于老版本系统。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_245.png)

#### 常用命令
- 查看规则：`iptables -L`
- 清空规则：`iptables -F`
- 设置默认策略：`iptables -P INPUT DROP`
- 添加规则：`iptables -A INPUT -s 192.168.10.0/24 -j ACCEPT`
- 删除规则：`iptables -D INPUT 1`

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_247.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_249.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_251.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_253.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_255.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_257.png)

#### 示例：拒绝特定IP访问
```bash
iptables -A INPUT -s 192.168.10.1 -j REJECT
```

### 方法二：使用firewall-cmd
`firewall-cmd`是Red Hat 7和8中常用的防火墙配置工具。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_259.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_261.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_263.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_265.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_267.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_269.png)

#### 核心概念
- **区域**：防火墙规则的模板，如`public`、`trust`、`drop`。
- **运行时模式**：配置立即生效，重启后失效。
- **永久模式**：配置重启后生效，需使用`--permanent`参数。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_271.png)

#### 常用命令
- 查看默认区域：`firewall-cmd --get-default-zone`
- 设置默认区域：`firewall-cmd --set-default-zone=trust`
- 放行服务：`firewall-cmd --permanent --add-service=ssh`
- 放行端口：`firewall-cmd --permanent --add-port=80/tcp`
- 重新加载配置：`firewall-cmd --reload`

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_273.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_275.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_277.png)

#### 示例：放行SSH服务
```bash
firewall-cmd --permanent --add-service=ssh
firewall-cmd --reload
```

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_279.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_281.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_283.png)

### 方法三：使用firewall-config
`firewall-config`是图形化防火墙配置工具，适用于Red Hat 7系统。在Red Hat 8中需额外安装。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_285.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_287.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_289.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_291.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_293.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_295.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_297.png)

#### 安装与使用
1. 配置软件仓库，安装`firewall-config`。
2. 运行`firewall-config`，通过图形界面配置防火墙规则。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_299.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_301.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_303.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_305.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_307.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_309.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_311.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_313.png)

### 方法四：使用Cockpit
`Cockpit`是一个基于Web的系统管理工具，可以配置防火墙及其他系统服务。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_315.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_317.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_319.png)

#### 安装与使用
1. 安装Cockpit：`dnf install cockpit`
2. 启动Cockpit服务：`systemctl start cockpit`
3. 通过浏览器访问`https://<服务器IP>:9090`，使用图形界面配置防火墙。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_321.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_323.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_325.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_327.png)

---

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_329.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_331.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_333.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_335.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_337.png)

## 高级防火墙配置 🚀

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_339.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_341.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_343.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_345.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_347.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_349.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_351.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_353.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_355.png)

本节将介绍防火墙的高级配置，包括端口转发和复杂规则。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_357.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_359.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_361.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_363.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_365.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_367.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_369.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_371.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_373.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_375.png)

### 端口转发
端口转发可以将流量从一个端口转发到另一个端口，隐藏实际服务端口。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_377.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_379.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_381.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_383.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_385.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_387.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_389.png)

#### 示例：将888端口转发到22端口
```bash
firewall-cmd --permanent --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10
firewall-cmd --reload
```

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_391.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_393.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_395.png)

### 复杂规则
复杂规则允许更精确的流量控制，如基于源IP和协议的组合规则。

#### 示例：允许特定IP访问SSH服务
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.10.1" service name="ssh" accept'
firewall-cmd --reload
```

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_397.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_399.png)

---

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_401.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_403.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_405.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_407.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_409.png)

## 总结 📚

本节课中，我们一起学习了Linux系统中防火墙的配置与管理。主要内容包括：
1. 网络配置的前置条件，确保虚拟机与物理机互通。
2. 防火墙的基础理论，如规则链、匹配规则和默认策略。
3. 四种防火墙配置方法：`iptables`、`firewall-cmd`、`firewall-config`和`Cockpit`。
4. 高级防火墙配置，如端口转发和复杂规则。

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_411.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_413.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_415.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_417.png)

![](img/77b83e07a5fc1d6a4e9b1d8c5345484d_419.png)

通过本节课的学习，您应能掌握防火墙的基本配置方法，并根据实际需求选择合适的工具进行配置。防火墙是系统安全的重要组成部分，合理配置防火墙可以有效保护系统免受未经授权的访问。