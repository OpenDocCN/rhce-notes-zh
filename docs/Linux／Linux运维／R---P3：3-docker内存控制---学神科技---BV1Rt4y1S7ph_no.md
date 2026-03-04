# Docker容器管理：P3：Docker内存控制、数据映射与IO资源配额控制 🐳

![](img/f170ed2eee2c96c3946c3e884ea55c96_1.png)

在本节课中，我们将学习Docker容器的资源配额控制，重点包括内存限制、宿主机与容器间的数据映射，以及对磁盘IO读写速度的限制。掌握这些技能对于在生产环境中高效、安全地运行容器至关重要。

![](img/f170ed2eee2c96c3946c3e884ea55c96_3.png)

---

![](img/f170ed2eee2c96c3946c3e884ea55c96_5.png)

## 容器运行后自动释放资源 🗑️

![](img/f170ed2eee2c96c3946c3e884ea55c96_7.png)

上一节我们介绍了容器的基础操作，本节中我们来看看如何让容器在运行结束后自动释放资源。这在执行一次性任务（如压力测试或单元测试）时非常有用，可以避免产生大量停止的容器占用系统资源。

![](img/f170ed2eee2c96c3946c3e884ea55c96_9.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_11.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_13.png)

Docker的 `--rm` 参数可以实现此功能。当容器退出时，它会自动被删除。

![](img/f170ed2eee2c96c3946c3e884ea55c96_15.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_17.png)

以下是使用 `--rm` 参数的示例命令：
```bash
docker run -it --rm --name mk centos sleep 5
```
此命令会创建一个名为 `mk` 的容器，执行 `sleep 5` 命令后自动退出并删除。您可以通过 `docker ps -a` 命令观察其创建和自动删除的过程。

![](img/f170ed2eee2c96c3946c3e884ea55c96_18.png)

---

![](img/f170ed2eee2c96c3946c3e884ea55c96_20.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_22.png)

## 内存资源控制 💾

![](img/f170ed2eee2c96c3946c3e884ea55c96_24.png)

了解了自动清理后，我们来看看如何控制容器对内存资源的使用。通过 `-m` 参数，可以限制容器能够使用的最大内存量。

![](img/f170ed2eee2c96c3946c3e884ea55c96_26.png)

例如，以下命令将容器内存限制为128MB：
```bash
docker run -it -m 128m centos
```
您可以通过检查容器在 `/sys/fs/cgroup/memory/memory.limit_in_bytes` 文件中的值来验证限制是否生效。

![](img/f170ed2eee2c96c3946c3e884ea55c96_28.png)

---

## 综合资源限制示例 ⚙️

![](img/f170ed2eee2c96c3946c3e884ea55c96_30.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_32.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_34.png)

我们可以将CPU和内存限制结合起来，创建一个资源受限的容器。

![](img/f170ed2eee2c96c3946c3e884ea55c96_36.png)

以下命令创建一个容器，限制其只能在CPU核心0和1上运行，并且最大内存使用量为128MB：
```bash
docker run -it --cpuset-cpus="0,1" -m 128m centos
```
通过这种方式，可以精细地控制容器对宿主机资源的占用。

---

![](img/f170ed2eee2c96c3946c3e884ea55c96_38.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_40.png)

## 数据卷映射 📂

![](img/f170ed2eee2c96c3946c3e884ea55c96_42.png)

容器内的数据通常是易失的。为了持久化数据，我们可以使用 `-v` 参数将宿主机的目录映射到容器内部。

`-v` 参数的基本格式为：`-v <宿主机目录>:<容器内目录>`。

以下是创建一个Apache容器并将宿主机网页目录映射到容器内的示例：
```bash
# 1. 在宿主机创建目录和测试文件
mkdir -p /var/www/html
echo "Hello from Host" > /var/www/html/index.html

![](img/f170ed2eee2c96c3946c3e884ea55c96_44.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_46.png)

# 2. 运行容器并进行目录映射
docker run -it --name web1 -v /var/www/html:/var/www/html centos
```
进入容器后，您可以在 `/var/www/html/` 下看到宿主机创建的文件。同样，在容器内对该目录的修改也会直接反映在宿主机上。这确保了即使容器被删除，数据也不会丢失。

---

![](img/f170ed2eee2c96c3946c3e884ea55c96_48.png)

## 磁盘IO资源控制 ⚡

最后，我们来学习如何控制容器对磁盘的读写速度，防止某个容器耗尽IO资源影响其他服务。主要使用 `--device-write-bps` 和 `--device-read-bps` 参数。

![](img/f170ed2eee2c96c3946c3e884ea55c96_50.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_52.png)

为了对特定设备进行限速，首先需要将该设备映射到容器内。

![](img/f170ed2eee2c96c3946c3e884ea55c96_54.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_56.png)

以下是限制容器对 `/dev/sda` 设备的写入速度不超过1MB/s的完整示例：
```bash
docker run -it \
  -v /var/www/html:/var/www/html \
  --device /dev/sda:/dev/sda \
  --device-write-bps /dev/sda:1mb \
  centos
```
在容器内，可以使用 `dd` 命令配合 `oflag=direct` 参数（绕过缓存，直接写入磁盘）来测试实际的写入速度：
```bash
time dd if=/dev/sda of=/var/www/html/test bs=1M count=50 oflag=direct
```
您将观察到写入速度被稳定限制在约1MB/s。

![](img/f170ed2eee2c96c3946c3e884ea55c96_58.png)

---

![](img/f170ed2eee2c96c3946c3e884ea55c96_60.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_62.png)

## 课程总结 📝

![](img/f170ed2eee2c96c3946c3e884ea55c96_63.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_65.png)

本节课中我们一起学习了Docker容器资源管理的几个核心方面：
1.  **自动资源释放**：使用 `--rm` 参数让容器退出后自动删除。
2.  **内存限制**：使用 `-m` 参数控制容器最大内存使用量。
3.  **数据持久化**：使用 `-v` 参数将宿主机目录映射到容器内，实现数据持久存储。
4.  **磁盘IO限制**：使用 `--device-write-bps` 和 `--device-read-bps` 参数限制容器对磁盘的读写速率，防止IO资源被独占。

![](img/f170ed2eee2c96c3946c3e884ea55c96_67.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_69.png)

![](img/f170ed2eee2c96c3946c3e884ea55c96_71.png)

通过合理运用这些资源控制机制，可以在共享的宿主机上稳定、公平地运行多个容器，为构建可靠的容器化应用环境打下坚实基础。