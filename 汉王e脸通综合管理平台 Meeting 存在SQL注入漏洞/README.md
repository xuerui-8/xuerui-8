汉王 e 脸通综合管理平台 SQL 注入漏洞检测工具
工具简介
本工具用于检测汉王 e 脸通综合管理平台的Meeting模块是否存在 SQL 注入漏洞。通过发送特定构造的请求，根据返回结果判断目标是否存在漏洞，可支持单 URL 检测与批量 URL 检测。
漏洞背景
汉王 e 脸通综合管理平台的/manage/meeting/queryMeeting.do接口在处理order参数时，未对用户输入进行严格过滤，导致存在 SQL 注入漏洞。攻击者可利用该漏洞执行恶意 SQL 语句，获取数据库敏感信息或进一步控制系统。
工具功能
单 URL 检测：对单个目标 URL 进行漏洞检测
批量检测：读取包含多个 URL 的文件，批量检测漏洞
自动记录：将存在漏洞的目标自动保存至结果文件
使用环境
Python 3.x
依赖库：requests
安装依赖
使用前请先安装必要的依赖库：

bash
pip install requests
使用方法
命令参数说明
参数	简写	描述	示例
--url	-u	单个目标 URL	-u http://192.168.1.1
--file	-f	包含多个 URL 的文件路径	-f urls.txt
--help	-h	显示帮助信息	-h
单 URL 检测
bash
python poc.py -u http://target-url
批量检测
准备包含目标 URL 的文件（每行一个 URL），例如urls.txt
执行批量检测命令：

bash
python poc.py -f urls.txt
检测结果说明
[+] http://target-url 存在sql漏洞：目标存在漏洞，会自动记录到result1.txt
[-] 该http://target-url 不存在sql漏洞：目标不存在漏洞
[*] 该http://target-url 存在问题,请手工测试：目标访问异常，建议手工验证
注意事项
本工具仅用于合法的安全测试与研究，使用前请确保已获得目标系统的授权，禁止未授权检测
批量检测时默认使用 50 个进程，可根据网络环境在代码中调整Pool(50)的数值
检测结果会保存至result1.txt文件中，重复运行会追加内容
免责声明
本工具仅用于网络安全学习和研究目的，请勿用于未经授权的渗透测试。使用本工具造成的任何违法违规行为，由使用者自行承担全部责任。
