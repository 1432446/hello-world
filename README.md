#-*- codeing =utf-8 -*-
#@Time :2021/1/14 12:31
#@Auster :liucd
#@File :打卡.py
#@Software :PyCharm
import  requests
import  time
# import json
import re
#修改line13、14的username和password，对应服务门户的账号密码
#如需推送服务，到酷推官网注册 cp.xuthus.cc,替换line51的sendxxxx,  xxxx为酷推的skey
#默认不开启推送，需要的将line69的#push(rs)改成push(rs)



def login(s):
    login_url='https://app.xaut.edu.cn/uc/wap/login/check'
    header={'User-Agent':'Mozilla/5.0 (Linux; Android 8.1.0; 16 X Build/OPM1.171019.026; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/77.0.3865.120 MQQBrowser/6.2 TBS/045318 Mobile Safari/537.36 V1_AND_SQ_8.4.1_1442_YYB_D QQ/8.4.1.4680 NetType/WIFI WebP/0.3.0 Pixel/1080 StatusBarHeight/84 SimpleUISwitch/1 QQTheme/3064',
            'Referer':'https://app.xaut.edu.cn/uc/wap/login?redirect=https%3A%2F%2Fapp.xaut.edu.cn%2Fncov%2Fwap%2Fdefault%2Findex'}
    data={'username':'3180342065',
          'password':'366494cd'}

    res=s.post(url=login_url,headers=header,data=data)
    if res.json()['m']=='操作成功':
        print("登录成功")
    pass

def check(s):#获取信息
    check_url='https://app.xaut.edu.cn/ncov/wap/default/index'
    referer_url='https://app.xaut.edu.cn/uc/wap/login?redirect=https%3A%2F%2Fapp.xaut.edu.cn%2Fncov%2Fwap%2Fdefault%2Findex'
    header={'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.43 Safari/537.36',
            'Referer':referer_url,
            # 'cookie':'eai-sess=slh8ld7dsbuofvgbd94990oe06; UUkey=9342a9623a07a919ca49f8bdd369caf6; Hm_lvt_48b682d4885d22a90111e46b972e3268=1596989108,1596989257,1597025731,1597028479; Hm_lpvt_48b682d4885d22a90111e46b972e3268=1597028563'
            }
    res=s.get(url=check_url,headers=header)
    # print(res.text)
    # sou=re.findall(r'var def = (.*);',res.text)
    # sou=eval(sou[0])
    # print(sou)
    soup=re.findall(r'oldInfo: (.*),',res.text)
    # print(type(eval(soup[0])))
    data=eval(soup[0])
    print(data)
    return data

def qiandao(s):
    url='https://app.xaut.edu.cn/ncov/wap/default/save'
    refere = 'https://app.xaut.edu.cn/ncov/wap/default/index'
    header={'Users-Agent':'Mozilla/5.0 (Linux; Android 8.1.0; 16 X Build/OPM1.171019.026; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/77.0.3865.120 MQQBrowser/6.2 TBS/045317 Mobile Safari/537.36 MMWEBID/9217 MicroMessenger/7.0.16.1700(0x27001035) Process/tools WeChat/arm64 NetType/WIFI Language/zh_CN ABI/arm64',
            'Referer':refere}
    data=check(s)
    time.sleep(2)
    res=s.post(url=url,headers=header,data=data)
    rs=res.json()['m']
    print(rs)
    return rs
    # print(type(res.text))
#
def push(rs):#推送
    kutuiurl = 'https://push.xuthus.cc/send/'
    rs='疫情通每日打卡通知'+'\n'+rs
    re = requests.get(url=kutuiurl+'?c='+rs)
    print(rs)
    pass
def main_handler(event,context):
    s = requests.Session()
    rs = ''
    login(s)
    rs = qiandao(s)
    push(rs)
    pass
if __name__=='__main__':
    s = requests.Session()
    rs = ''
    login(s)
    rs = qiandao(s)
    push(rs)
# s = requests.Session()
# rs=''
# login()
# rs=qiandao()
# push(rs)
