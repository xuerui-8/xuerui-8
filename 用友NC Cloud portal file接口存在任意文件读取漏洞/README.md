用友 NC Cloud portal file 接口任意文件读取漏洞检测工具 README
一、工具简介
本工具用于检测用友 NC Cloud 系统中 portal file 接口是否存在任意文件读取漏洞。通过发送特定构造的请求，尝试读取系统敏感文件（如 /etc/passwd 等 ），判断目标是否存在该安全风险，支持单 URL 检测和批量 URL 检测模式。
二、功能特性
单 URL 检测：可针对单个目标 URL，快速验证是否存在漏洞。
批量检测：支持从文件中读取多个目标 URL，多线程（multiprocessing.dummy.Pool 实现 ）并发检测，提升检测效率。
结果记录：检测到存在漏洞的目标 URL，会自动记录到 result.txt 文件中，方便后续整理分析。
三、使用依赖
运行本工具需要安装以下 Python 第三方库：

argparse：用于解析命令行参数，Python 标准库，一般随 Python 安装自带。
json：处理 JSON 数据，Python 标准库。
requests：发送 HTTP 请求，需额外安装，可通过 pip install requests 安装。
urllib3：处理 HTTP 连接相关，requests 依赖它，一般安装 requests 时会顺带安装，用于关闭 SSL 证书验证警告 。
四、使用说明
（一）命令行参数
-u, --url：指定单个目标 URL，如 http://example.com ，用于单 URL 漏洞检测。
-f, --file：指定包含多个目标 URL 的文件路径，文件中每行一个 URL ，用于批量检测。
（二）操作示例
单 URL 检测
在命令行执行：
bash
python 脚本名.py -u http://target-url

工具会对指定的 http://target-url 进行漏洞检测，输出检测结果（存在漏洞、不存在漏洞或访问异常 ）。
批量检测
准备一个包含目标 URL 的文本文件（如 urls.txt ），每行一个 URL ，然后在命令行执行：
bash
python 脚本名.py -f urls.txt

工具会启动多线程，并发对文件中所有 URL 进行检测，将存在漏洞的 URL 记录到 result.txt ，同时在控制台输出各 URL 检测状态。
五、注意事项
网络环境：检测过程依赖网络连接，确保目标可访问，若目标网络限制、超时等，可能影响检测结果，需人工排查。
权限与合法性：使用本工具检测目标系统时，需确保符合相关法律法规及授权要求，仅用于授权的渗透测试、漏洞验证等合法场景，未经授权检测他人系统可能触犯法律。
误报与漏报：虽经测试优化，但仍可能存在误报（误判存在漏洞 ）或漏报（实际有漏洞未检测出 ）情况，重要场景建议结合人工验证、其他辅助工具进一步确认。
代码完善性：当前代码 mp.close 和 mp.join 缺少括号，实际使用需修正为 mp.close()、mp.join() ，否则多线程资源可能无法正确释放，影响批量检测效果 。
