亿赛通 SQL 注入漏洞检测工具 README
1. 工具简介
本工具用于检测亿赛通相关系统中存在的 SQL 注入漏洞，通过构造特定的 SQL 注入 Payload，向目标 URL 发送请求并分析响应结果，快速识别存在漏洞的目标。工具支持单 URL 检测和批量 URL 检测，采用多线程技术提升批量检测效率，同时自动忽略 SSL 证书验证警告，适用于多种网络环境。
2. 漏洞原理
目标系统的goods.php接口在处理id参数时，未对用户输入进行严格过滤和参数化查询，导致存在 SQL 注入漏洞。
工具通过以下逻辑判断漏洞是否存在：

向目标 URL 发送正常请求（无 Payload），记录响应状态码和请求耗时；
向目标 URL 拼接注入 Payload（/goods.php?id=%27+UNION+ALL+SELECT+NULL,NULL,NULL,md5(1),NULL,...--+-），发送恶意请求；
对比两次请求的耗时（若恶意请求耗时在 5 秒及以上，且与正常请求耗时差≤5 秒），同时验证目标可访问（状态码 200），则判定存在 SQL 注入漏洞。
3. 环境依赖
依赖项	版本要求	说明
Python	3.6+	工具基于 Python 3 开发，不兼容 Python 2
requests	2.20.0+	用于发送 HTTP/HTTPS 请求
urllib3	1.24.0+	处理 HTTPS 证书警告（工具已禁用不安全证书警告）
multiprocessing.dummy	内置模块	提供多线程支持，提升批量检测效率
4. 安装步骤
安装 Python 3
从Python 官方网站下载并安装对应系统（Windows/macOS/Linux）的 Python 3.6 及以上版本，安装时需勾选 “Add Python to PATH”（Windows）。
验证 Python 环境
打开终端 / 命令提示符，执行以下命令，确认 Python 版本符合要求：
bash
python --version  # Windows
# 或
python3 --version  # macOS/Linux

安装依赖库
通过pip命令安装requests和urllib3（若系统中同时存在 Python 2 和 Python 3，需使用pip3）：
bash
pip install requests urllib3  # Windows
# 或
pip3 install requests urllib3  # macOS/Linux

下载工具文件
将工具脚本（如yisaitong_sql_injection.py）和本README.md文件保存到同一目录下。
5. 使用说明
5.1 命令格式
工具通过命令行参数指定检测模式（单 URL / 批量 URL），基本命令格式如下：

bash
python yisaitong_sql_injection.py [参数]

（macOS/Linux 系统需将python替换为python3，下同）
5.2 参数说明
参数	简写	作用	示例
--url	-u	单 URL 检测，指定单个目标 URL	-u https://example.com
--file	-f	批量检测，指定包含多个 URL 的文本文件路径	-f url_list.txt
--help	-h	查看工具帮助信息，显示参数说明	-h
5.3 操作示例
示例 1：单 URL 检测
检测单个目标 URL 是否存在漏洞：

bash
python yisaitong_sql_injection.py -u https://target.com

执行后，工具会直接在终端输出检测结果（存在漏洞 / 不存在漏洞 / 目标异常）。
示例 2：批量 URL 检测
准备 URL 列表文件
创建文本文件（如url_list.txt），每行填写一个目标 URL（需包含http://或https://），格式如下：
plaintext
https://target1.com
http://target2.com:8080
https://target3.com/path

执行批量检测
指定 URL 列表文件路径，工具将以 50 个线程并发检测：
bash
python yisaitong_sql_injection.py -f url_list.txt


检测过程中，终端会实时输出每个 URL 的检测结果；
检测完成后，存在漏洞的 URL 会自动保存到当前目录下的result1.txt文件中（若文件不存在则自动创建，若已存在则追加内容）。
示例 3：查看帮助信息
若忘记参数用法，可执行以下命令查看帮助：

bash
python yisaitong_sql_injection.py -h

输出内容如下：

plaintext
usage: yisaitong_sql_injection.py [-h] [-u URL] [-f FILE]

亿赛通 接口存在 SQL注入漏洞

optional arguments:
  -h, --help            show this help message and exit
  -u URL, --url URL     place input like (e.g. https://example.com)
  -f FILE, --file FILE  place input your file path (e.g. url_list.txt)
6. 结果说明
输出标识	含义	后续建议
[+] [URL] 存在sql注入漏洞	目标确认存在 SQL 注入漏洞	立即停止该系统对外访问，联系开发团队修复漏洞
[-] 该[URL]不存在sql注入漏洞	目标未检测到 SQL 注入漏洞	定期复测，同时排查其他类型漏洞（如 XSS、命令注入）
[*] 该[URL]存在问题,请手工测试	目标响应状态码非 200（如 404、500）	检查 URL 是否正确、目标是否可访问，建议手工访问 URL 验证
无输出	目标请求超时 / 网络不可达	检查目标网络连通性（如 ping、telnet），确认 URL 格式正确
7. 注意事项
合法性声明
本工具仅用于合法授权的渗透测试或漏洞检测，禁止用于未经授权的第三方系统。使用前需确保已获得目标系统所有者的书面授权，违规使用导致的法律责任由使用者自行承担。
性能影响
批量检测默认使用 50 个线程，若目标数量过多（如 > 1000 个），可能导致本地网络带宽占用过高或目标服务器压力过大。建议根据本地网络环境调整线程数（修改代码中Pool(50)的数值，如Pool(20)）。
SSL 证书问题
工具已通过urllib3.disable_warnings()禁用 SSL 证书验证警告，若目标使用自签名证书，仍可正常检测，但存在一定安全风险（如中间人攻击），建议在可信网络环境中使用。
误报 / 漏报可能性
漏洞判断依赖请求耗时对比，若目标服务器响应不稳定（如耗时波动大），可能导致误报或漏报。对于疑似结果，建议手工构造 Payload 验证（如直接访问[URL]/goods.php?id=' UNION ALL SELECT NULL,NULL,NULL,md5(1),NULL,...--+-，查看响应中是否包含c4ca4238a0b923820dcc509a6f75849b（md5 (1) 的结果））。
8. 故障排查
问题现象	可能原因	解决方案
执行命令提示 “ModuleNotFoundError: No module named 'requests'”	未安装 requests 库	执行pip install requests（Windows）或pip3 install requests（macOS/Linux）
批量检测时无结果输出	1. URL 列表文件路径错误；2. 文件中 URL 格式错误（无 http/https）；3. 目标全部不可达	1. 确认文件路径正确（如使用绝对路径-f C:\url_list.txt）；2. 检查 URL 是否包含http://或https://；3. 手工访问 1-2 个 URL 确认可访问
单 URL 检测提示 “[*] 该 URL 存在问题”	1. URL 错误（如拼写错误、端口错误）；2. 目标服务器拒绝访问（如 IP 黑名单）	1. 核对 URL 是否正确；2. 使用浏览器访问该 URL，确认是否能正常打开
多线程检测时程序崩溃	线程数过高，超出本地系统资源限制	减少线程数（修改Pool(50)为Pool(10)或Pool(20)），重新执行
