孚盟软件 - 孚盟云 SQL 注入漏洞检测工具 README
1. 工具简介
本工具用于检测孚盟软件 - 孚盟云系统是否存在特定 SQL 注入漏洞（漏洞触发点：/goods.php?id 参数）。通过构造恶意 SQL 注入 payload，发送 HTTP 请求并分析响应结果，快速识别目标系统是否存在漏洞，支持单 URL 检测和批量 URL 检测两种模式。
1.1 漏洞原理
目标系统的 goods.php 页面中，id 参数未对用户输入进行有效过滤，直接拼接进 SQL 语句执行。本工具通过 UNION ALL SELECT 语句构造注入 payload（包含 md5(1) 特征值），若响应中包含 md5(1) 的计算结果（c4ca4238a0b923820dcc509a6f75849b，工具中误写为 "master"，实际使用时建议修正该判断逻辑），则判定目标存在 SQL 注入漏洞。
2. 环境依赖
2.1 运行环境
Python 3.x（推荐 Python 3.6 及以上版本）
支持 Windows、Linux、macOS 等主流操作系统
2.2 依赖库
工具依赖以下 Python 第三方库，需提前安装：

requests：用于发送 HTTP 请求
urllib3：用于处理 HTTPS 请求警告（工具已内置禁用不安全请求警告的逻辑）
安装命令
在命令行中执行以下命令，安装所需依赖库：

bash
pip install requests urllib3
3. 工具使用说明
3.1 命令格式
工具通过命令行参数接收检测目标，支持两种模式：单 URL 检测和批量文件检测。基本命令格式如下：

bash
python 工具文件名.py [参数]
3.2 参数说明
参数	简写	功能描述	示例
--url	-u	指定单个检测目标 URL（需完整填写目标系统的基础 URL，如 http://xxx.com）	-u http://test.fumengyun.com
--file	-f	指定包含多个目标 URL 的文件路径（文件中每行一个 URL）	-f target_urls.txt
--help	-h	查看工具使用帮助信息	-h
3.3 使用示例
示例 1：单 URL 检测
检测单个目标系统是否存在漏洞：

bash
python fumeng_sql_injection.py -u http://demo.fumengyun.com
示例 2：批量文件检测
从 target_urls.txt 文件中读取多个目标 URL，批量检测漏洞（工具默认开启 50 个线程，可在 Pool(50) 中调整线程数）：

bash
python fumeng_sql_injection.py -f target_urls.txt
示例 3：查看帮助信息
查看工具参数说明和使用方法：

bash
python fumeng_sql_injection.py -h
4. 输出说明
4.1 控制台输出
工具运行时会在控制台实时输出检测结果，不同状态对应不同标识：

[+] [目标URL] 存在sql注入漏洞：目标存在漏洞
[-] 该[目标URL]不存在sql注入漏洞：目标不存在漏洞
[*] 该[目标URL]存在问题,请手工测试：目标响应状态码非 500，可能无法通过当前 payload 检测，需手工验证
无输出：目标无法访问（如网络超时、连接拒绝等），工具会忽略该目标（except 块中未处理异常输出）
4.2 结果文件
检测到存在漏洞的目标 URL 会自动写入当前目录下的 result1.txt 文件，每行存储一个存在漏洞的 URL，便于后续分析和处理。
5. 注意事项
合法性声明：
本工具仅用于合法授权的安全测试（如企业内部系统自查、获得客户明确授权的渗透测试），严禁用于未经授权的非法攻击行为。
使用本工具造成的任何法律责任、财产损失或其他风险，均由使用者自行承担。
网络环境：
批量检测时，建议根据目标网络环境调整线程数（Pool(50) 中的 50），过高的线程数可能导致目标服务器拒绝服务（DoS）或本地网络拥堵。
若目标系统使用 HTTPS 协议且证书无效，工具已通过 urllib3.disable_warnings 禁用证书验证警告，无需额外处理。
工具局限性：
工具仅检测特定路径（/goods.php?id）的 SQL 注入漏洞，无法覆盖目标系统的其他漏洞。
漏洞判断逻辑中，if "master" in res2.text 存在逻辑错误（正确应为判断 md5(1) 的结果 c4ca4238a0b923820dcc509a6f75849b），使用前建议修正该代码块，否则可能导致误判或漏判。
结果验证：
工具输出的 “存在漏洞” 结果需进一步手工验证（如使用 SQL 注入工具（如 sqlmap）深入测试），避免因目标系统特殊配置导致误判。
6. 代码修改建议
若需优化工具功能，可参考以下修改方向：

修正漏洞判断逻辑：
将 if "master" in res2.text: 改为 if "c4ca4238a0b923820dcc509a6f75849b" in res2.text:（md5(1) 的正确结果），确保漏洞判断准确性。
增加异常处理输出：
在 except 块中添加 print(f"[!] {target} 访问失败（网络错误/连接拒绝等）")，便于排查无法访问的目标。
可配置线程数：
在命令行参数中添加 --thread（-t）参数，支持用户自定义批量检测的线程数，例如：
python
运行
parser.add_argument('-t','--thread',dest='thread',type=int,default=50,help='Number of threads (default: 50)')


后续使用 mp=Pool(args.thread) 调用线程池。
输出格式优化：
增加检测时间戳、响应时间等信息，便于日志分析
