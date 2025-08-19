Check Point Security Gateways 任意文件读取漏洞检测工具
工具介绍
本工具用于检测 Check Point Security Gateways 安全网关是否是否存在任意文件读取漏洞。通过发送特定构造的请求，判断目标是否允许未授权读取服务器敏感文件（如 /etc/passwd），帮助安全人员快速识别潜在风险。
漏洞原理
该漏洞存在于 Check Point Security Gateways 安全网关的特定接口中，攻击者可通过构造包含路径遍历字符的恶意请求，绕过系统限制读取服务器上的任意敏感文件（本工具默认检测 /etc/passwd 文件）。
使用前提
安装 Python 3.x 环境
安装依赖库：requests
安装命令：pip install requests
命令参数说明
参数	作用	示例
-u/--url	检测单个目标 URL	python tool.py -u http://example.com
-f/--file	批量检测文件中的 URL（每行一个 URL）	python tool.py -f urls.txt
-h/--help	查看帮助信息	python tool.py -h
使用示例
单个目标检测
bash
python tool.py -u http://192.168.1.1

批量目标检测
（需提前准备包含目标 URL 的文件，如 urls.txt，每行一个 URL）
bash
python tool.py -f urls.txt

结果说明
[+] [URL] 存在任意文件读取：目标存在漏洞，已记录到 result1.txt 文件中
[-] 该[URL]不存在任意文件读取：目标未检测到漏洞
[*] 该[URL]存在问题,请手工测试：目标响应异常，建议手动验证
注意事项
请确保测试目标为合法授权范围，禁止未经授权的漏洞检测
批量检测时，工具默认使用 100 个进程，可根据网络环境在代码中调整 Pool(100) 的数值
工具已禁用 SSL 证书验证警告，适用于使用自签名证书的目标环境
检测结果会保存在当前目录的 result1.txt 文件中，可直接查看漏洞目标列表
