# 微商城系统 goods.php SQL注入漏洞检测工具 README

## 一、工具简介
本工具用于检测**微商城系统**中存在的SQL注入漏洞，具体漏洞点位于 `goods.php` 文件的 `id` 参数处。工具通过发送包含特定Payload的HTTP请求，判断目标系统是否返回预期的MD5特征值（`md5(1)` 结果：`c4ca4238a0b923820dcc509a6f75849b`），从而快速识别漏洞是否存在。

工具支持**单URL检测**和**批量URL检测**两种模式，采用多线程（`multiprocessing.dummy.Pool`）提升批量检测效率，同时默认忽略HTTPS证书验证警告，适配更多目标环境。


## 二、环境依赖
使用本工具前，需确保本地环境已安装以下Python库：
| 依赖库 | 作用 | 安装命令 |
|--------|------|----------|
| `requests` | 发送HTTP/HTTPS请求 | `pip install requests` |
| `argparse` | 解析命令行参数（Python标准库，无需额外安装） | - |
| `multiprocessing` | 实现多线程批量检测（Python标准库，无需额外安装） | - |


## 三、参数说明
工具通过命令行参数指定检测模式（单URL/批量），参数详情如下：

| 参数短格式 | 参数长格式 | 参数作用 | 示例 |
|------------|------------|----------|------|
| `-u` | `--url` | 指定**单个目标URL**（支持IP或域名，可省略`http://`/`https://`） | `python poc.py -u http://test.com` 或 `python poc.py -u test.com` |
| `-f` | `--file` | 指定**URL列表文件**（批量检测，文件中每行一个目标URL/IP） | `python poc.py -f url_list.txt` |
| `-h` | `--help` | 查看工具帮助信息（参数说明、使用示例） | `python poc.py -h` |


## 四、使用步骤
### 1. 准备工作
1. 确保已安装上述依赖库（重点安装`requests`）；
2. 若需批量检测，提前创建URL列表文件（如`url_list.txt`），格式要求：
   ```txt
   http://target1.com
   https://target2.com
   target3.com  # 工具会自动补全为http://target3.com
   192.168.1.100
   ```


### 2. 具体使用场景
#### 场景1：单URL检测
检测单个目标是否存在漏洞，命令如下：
```bash
# 示例1：带http://前缀
python poc.py -u http://test.com

# 示例2：不带协议前缀（工具自动补全http://）
python poc.py -u test.com
```

#### 场景2：批量URL检测
从文件中读取多个目标，批量检测漏洞，命令如下：
```bash
# 示例：从url_list.txt中读取目标，多线程批量检测
python poc.py -f url_list.txt
```

#### 场景3：查看帮助信息
若忘记参数用法，可通过以下命令查看帮助：
```bash
python poc.py -h
```


## 五、结果说明
### 1. 实时输出结果
工具运行时会在控制台实时打印每个目标的检测状态，状态标识含义如下：
- `[+] {target} 存在sql注入漏洞`：目标存在漏洞，结果会同步写入输出文件；
- `[-] 该{target} 不存在sql注入漏洞`：目标不存在漏洞；
- `[*] 该{target} 存在问题，请手工测试`：目标HTTP响应状态码非200（如404、500），需手动验证；
- 无输出：目标无法访问（如网络不通、超时），工具会捕获异常并跳过。


### 2. 结果文件
检测到的**存在漏洞的目标URL**会自动写入当前目录下的 `result1.txt` 文件中，格式为每行一个漏洞URL，便于后续进一步验证或处理。


## 六、注意事项
1. **合法性声明**：本工具仅用于**合法授权的渗透测试**或**漏洞验证**，禁止用于未授权的攻击行为，使用者需承担因非法使用导致的全部法律责任；
2. **HTTPS证书问题**：工具默认通过`urllib3.disable_warnings()`忽略HTTPS证书验证警告，若目标环境需严格验证证书，可删除代码中`urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)`这一行；
3. **多线程并发数**：批量检测时默认使用50个线程（`Pool(50)`），若目标数量较少或本地网络带宽有限，可修改代码中`mp=Pool(50)`的数字（如改为10、20），避免请求过于频繁导致目标拦截或本地网络拥堵；
4. **Payload说明**：工具使用的Payload为`/goods.php?id='+UNION+ALL+SELECT+NULL,NULL,NULL,md5(1),NULL,...--+-`，通过判断响应中是否包含`md5(1)`的结果来确认漏洞，若目标系统对Payload有过滤，可能导致误判，需手动调整Payload。


## 七、常见问题
### Q1：运行时提示“ModuleNotFoundError: No module named 'requests'”？
A：未安装`requests`库，执行`pip install requests`即可解决。

### Q2：批量检测时，部分目标无输出？
A：可能是目标网络不通、超时或拒绝连接，工具会捕获异常并跳过，此类目标建议手动访问验证。

### Q3：目标返回200但提示“不存在漏洞”，是否一定无漏洞？
A：不一定。可能是目标系统对Payload中的关键字（如`UNION`、`SELECT`）进行了过滤，可尝试修改Payload（如调整`NULL`数量、替换关键字大小写）后重新检测。
