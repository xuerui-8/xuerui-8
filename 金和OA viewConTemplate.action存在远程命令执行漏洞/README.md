金和 OA viewConTemplate.action 远程命令执行漏洞检测工具
工具简介
本工具用于于检测金和 OA 系统中viewConTemplate.action接口是否存在远程命令执行漏洞。该工具支持单 URL 检测和批量 URL 检测两种模式，并能自动记录存在漏洞的目标地址。

漏洞背景
金和 OA 系统的/jc6/platform/portalwb/portalwb-con-template!viewConTemplate.action接口在处理用户输入时存在安全缺陷，攻击者可通过构造恶意请求执行系统命令，获取服务器控制权。

功能特点
支持单个目标 URL 检测
支持批量导入 URL 列表进行检测
多线程检测，提升批量检测效率
随机 User-Agent 头，降低检测被拦截概率
自动记录存在漏洞的目标到结果文件
环境要求
Python 3.x
依赖库：requests
安装依赖
bash
pip install requests

使用方法
命令参数说明
参数	简写	描述	示例
--url	-u	单个目标 URL 地址	-u http://192.168.1.100
--file	-f	包含多个 URL 的文件路径	-f urls.txt
--help	-h	显示帮助信息	-h
单目标检测
bash
python poc.py -u http://target-ip:port
批量检测
准备 URL 列表文件（每行一个 URL），例如urls.txt
执行批量检测命令：

bash
python poc.py -f urls.txt
检测结果说明
[+] http://target 存在远程命令执行：目标存在漏洞，会自动记录到result.txt
[-] http://target 不存在远程命令执行：目标未检测到漏洞
[*] http://target 访问有问题，请手工检查：目标访问异常，建议人工验证

注意事项
本工具仅用于合法的安全测试，使用前请确保已获得目标系统的授权
批量检测默认使用 100 个线程，可根据网络情况修改Pool(100)中的数值
工具会忽略 SSL 证书验证警告，适用于使用自签名证书的目标
检测结果保存在当前目录的result.txt文件中，重复运行会追加内容

免责声明
本工具仅供网络安全学习和研究使用，严禁用于未经授权的非法攻击活动。使用本工具造成的任何直接或间接后果，均由使用者自行承担，作者不承担任何责任。
