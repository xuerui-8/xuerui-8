Cleo Synchronization 接口任意文件读取漏洞检测工具 README
一、工具简介
本工具用于检测 Cleo 系统中 Synchronization 接口是否存在任意文件读取漏洞。通过构造特定 HTTP 请求，尝试读取系统敏感文件（如 /etc/passwd ），快速判断目标是否存在该安全风险，支持单 URL 检测与批量 URL 检测模式，助力安全测试与漏洞排查。
二、功能特性
（一）检测模式
单 URL 检测：针对单个目标地址，快速验证漏洞存在性，适合小范围、针对性测试。
批量检测：支持从文件导入多个目标 URL，借助多线程（multiprocessing.dummy.Pool 实现 ）并发检测，大幅提升检测效率，适配大规模资产排查场景。
（二）结果记录
检测到存在漏洞的目标 URL，会自动追加写入 result.txt 文件，方便后续整理、分析漏洞影响范围 。
三、依赖环境
运行工具需安装以下 Python 库：

argparse：解析命令行参数，Python 标准库自带。
requests：发送 HTTP 请求，用于与目标系统交互，可通过 pip install requests 安装。
urllib3：处理 HTTP 连接相关逻辑，requests 依赖库，通常随 requests 一同安装，用于关闭 SSL 证书验证警告 。
四、使用说明
（一）命令行参数
-u, --url：指定单个目标 URL（如 http://example.com ），执行单 URL 漏洞检测。
-f, --file：指定包含多个目标 URL 的文件路径，文件需每行一个 URL ，用于批量检测。
（二）操作示例
1. 单 URL 检测
在命令行执行：

bash
python 脚本名.py -u http://target-url 

工具会对 http://target-url 发起检测，控制台输出检测结果（存在漏洞、不存在漏洞或访问异常 ）。
2. 批量检测
准备 URL 列表文件（如 urls.txt ），每行填写一个目标 URL 。
命令行执行：

bash
python 脚本名.py -f urls.txt 

工具启动多线程，并发检测文件中所有 URL ，存在漏洞的 URL 会记录到 result.txt ，控制台同步输出各 URL 检测状态。
五、注意事项
网络与权限：检测依赖网络连接，需确保目标系统可访问；检测行为应遵循授权与法律法规，仅限合法渗透测试、漏洞验证场景，未经授权检测他人系统可能违法。
误报漏报：受网络环境、目标系统配置等影响，可能出现误报（误判漏洞存在 ）或漏报（实际有漏洞未检出 ），关键场景建议结合人工验证、辅助工具复核。
代码完善性：代码中 mp.close 和 mp.join 缺少括号，实际使用需修正为 mp.close()、mp.join() ，否则多线程资源无法正确释放，可能影响批量检测效果 。
