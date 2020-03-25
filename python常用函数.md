### 在桌面创建文件夹
```
def create_desktop_file_folder(foldername):
    desktopPath=os.path.join(os.path.expanduser("~"),'Desktop')
    base_path=os.path.join(desktopPath,foldername)#存放最终文件的路径
    is_exists = os.path.exists(base_path)   
    if not is_exists:
        os.makedirs(base_path)
        print('{0} creat successful!'.format(base_path))
    else:
        print('{0} has been exists.'.format(base_path))
```
### 获取网络时间，参数可以为'www.baidu.com'
```
def networkTime(self,host):
    try:
        conn = http.client.HTTPConnection(host)
        conn.request("GET", "/")
        r = conn.getresponse()
        # r.getheaders() #获取所有的http头
        ts = r.getheader('date')  # 获取http头date部分
        # 将GMT时间转换成北京时间
        ltime = time.strptime(ts[5:25], "%d %b %Y %H:%M:%S")
        ttime = time.localtime(time.mktime(ltime) + 8 * 60 * 60)
        dat = "%u-%02u-%02u" % (ttime.tm_year, ttime.tm_mon, ttime.tm_mday)
        tm = "%02u:%02u:%02u" % (ttime.tm_hour, ttime.tm_min, ttime.tm_sec)
        wangluotime=str(dat+' '+tm)
    except:
        wangluotime=''
    return wangluotime
```
### 获取本地时间的函数
```
def localtime(self):
    return time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
```
### 校验身份证号最后一位是否通过校验
```
def idnum(self,idcard):
    # 处理空格判断是否是18位
    idnumber = str(idcard).replace(' ', '')
    if (len(idnumber) != 18):
        return False
    else:
        xishu = [7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2]
        xishuhe = 0
        for i in range(17):
            xishuhe += int(idnumber[i]) * xishu[i]
        jiaoyanzhi = str(idnumber[-1])
        jiaoyanlist = ['1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2']
        jiaoyanlist1 = ['1', '0', 'x', '9', '8', '7', '6', '5', '4', '3', '2']
        if (str(jiaoyanlist[xishuhe % 11]) == jiaoyanzhi or str(jiaoyanlist1[xishuhe % 11]) == jiaoyanzhi):
            return True
        else:
            return False
```
### 校验电话号码是否合理
```
def telephone(self,phonumber):
    #先处理空格
    phonum=str(phonumber).replace(' ','')
    if(len(phonum)!=11):
        return False
    else:
        phostr=phonum[:3]
        tele=[130,131,132,133,134,155,156,145,185,186,176,185,134,135,136,137,138,139,150,151,152,158,159,182,183,184,155,187,188,147,178]
        if (int(phostr) in tele):
            return True
        else:
            return False
```
### 图片转base64编码字符串
```
def imgtobase64(self,imageRoute):
    with open(imageRoute, "rb") as f:  # 转为二进制格式
        base64_data = str(base64.b64encode(f.read()), encoding="utf-8")  # 使用base64进行加密
    return base64_data
```
### base64编码字符串转图片
```
def base64toimg(self,base64str):
    imgdata = base64.b64decode(base64str)
    file_route = os.path.join(base_path, "tmp.jpg")
    file = open(file_route, 'wb')
    file.write(imgdata)
    file.close()
```
### 对身份证号进行分析，提取出性别，出生日期
```
def getData(self, sfzh):
    birth_year = int(sfzh[6:10])
    birth_month = int(sfzh[10:12])
    birth_day = int(sfzh[12:14])
    birthday = "{0}-{1}-{2}".format(birth_year, birth_month, birth_day)
    # """男生：1 女生：2"""
    num = int(sfzh[16:17])
    if num % 2 == 0:
        sex = '女'
    else:
        sex = '男'
    return birthday, sex, sfzh
```
### python连接与操作数据库
```
# -*- coding: UTF-8 -*-
#!/usr/bin/python3
 
import pymysql
import xlrd

data = xlrd.open_workbook("C://Users//root//Desktop//1.xlsx")
table = data.sheets()[0]
nrows = table.nrows
ncols = table.ncols

user=input("请输入你的用户名：")
password=input("请输入你的密码：")
database=input("请输入你的数据库：")

# 打开数据库连接
db = pymysql.connect("localhost", user, password,database, charset='utf8' )
print(11111111111111)
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
print(22222222222222)
# 如果数据表已经存在使用 execute() 方法删除表。
cursor.execute("DROP TABLE IF EXISTS ADDRESS")
print(33333333333333)
# 创建数据表SQL语句
sql = """CREATE TABLE ADDRESS(
         ADDRESS_CODE  CHAR(10) NOT NULL,
         ADDRESS_ENTRY CHAR(40) NOT NULL
         )ENGINE=InnoDB DEFAULT CHARSET=UTF8MB4
"""
cursor.execute(sql)
print("数据库创建成功")
sheng=''
shi=''
for i in range(nrows):#所有的循环
    a=table.row_values(i)
    diyige=int(table.row_values(i)[0])
    if(diyige%10000==0):#省一级的循环
        shengqu=table.row_values(i)[0]
        sheng=table.row_values(i)[1]
    elif(diyige%100==0):#市一级的循环)
        shiqu=table.row_values(i)[0]
        shi=table.row_values(i)[1]
    else:
        diyige=int(table.row_values(i)[0])
        if(str(shi)!=''and str(diyige)[0:4]==str(shiqu)[0:4]):
            dierge=str(sheng)+str(shi)+table.row_values(i)[1]
            print(type(diyige),type(dierge))
            sql1="insert into ADDRESS values('%s','%s')" % (str(diyige),dierge)
            cursor.execute(sql1)
            cursor.connection.commit()
            print("插入成功")
        else:
            dierge=str(sheng)+table.row_values(i)[1]
            print(type(diyige),type(dierge))
            sql1="insert into ADDRESS values('%s','%s')" % (str(diyige),dierge)
            cursor.execute(sql1)
            cursor.connection.commit()
            print("插入成功")
# 关闭数据库连接
db.close()

```
### python抓取百度百科
```
'''
本脚本目的是写入全国县级行政区的基本信息
'''
import pymysql
import re
import requests
from lxml import html
from bs4 import BeautifulSoup
import xlrd

data = xlrd.open_workbook("C://Users//root//Desktop//list.xlsx")

table = data.sheets()[0]
nrows = table.nrows
ncols = table.ncols

user=input("请输入你的用户名：")
password=input("请输入你的密码：")
database=input("请输入你的数据库：")

db = pymysql.connect("localhost", user, password,database, charset='utf8' )
cursor = db.cursor()

totalnumber=0
baseurl='https://baike.baidu.com/item/'


headers={'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
sheng=''
shi=''
for i in range(nrows):#所有的循环
    url=baseurl+str(table.row_values(i)[1])

    response=requests.get(url,headers=headers)
    print(response.status_code)
    print(response.url)
    text=response.content.decode("utf-8")
    tree=html.fromstring(text) 

    result0=tree.xpath('//div[@class="basic-info cmn-clearfix"]//*/text()')
    result9="".join(result0)
    result99=re.sub("\[.*?]","",result9)
    result10=result99.replace("'",'')
    result1=result10.replace("\xa0","")
    result2=result1.split('\n')
    while "" in result2:
        result2.remove("")
    #print(result2)
    number=range(0,len(result2),2)
    s={}
    if(len(result2)%2==0):
        for i in number:
            s[result2[i]]=result2[i+1]
        print(s)
    else:
        continue
    zwmc=ywmc=jc=ssz=sd=zycs=gqr=gg=gfyy=gjdm=hb=sq=zztz=gjlx=rksl=rkmd=zymz=zyzj=gtmj=syl=gdpzj=rjgdp=gjdhqh=gjymsx=dltx=gjxz=rlfzzs=''
    fltx=zyyh=dlzgd=zmqy=zyxf=qh=lsrw=mzxz=zmrw=zmjd=nh=gq=dbrw=dbsw=whsx=tywz=''
    pp=['中文名称','英文名称','简称','所属洲','首都','主要城市','国庆日','国歌','官方语言','国家代码','货币','时区','政治体制','国家领袖',
    '人口数量','人口密度','主要民族','主要宗教','国土面积','水域率','GDP总计','人均GDP','国际电话区号','国际域名缩写','道路通行','国家象征',
    '人类发展指数','法律体系','中央银行','地理最高点','著名企业','主要学府','气候','历史人物','民族象征','著名人物','著名景点',
    '年号','国旗','代表人物','代表事物','文化思想','通用文字']
    ddd=[]
    for i in range(len(pp)):
        if(str(pp[i]) in s):
            ddd.append(str(s[str(pp[i])]))
        else:
            ddd.append('')
    print(len(ddd),tuple(ddd))
    totalnumber+=1
    print("这是第"+str(totalnumber)+"条记录")
    if(tuple(ddd)[0]!=''):
        sql1="insert into countrynation_information values('%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s')" % tuple(ddd)
        print('----------------------------------------------------------------------')
        cursor.execute(sql1)
        print("插入"+str(totalnumber)+"数据")
        cursor.connection.commit()
    else:
        pass
print(totalnumber)
```
### python自动生成男女多个姓名
```
def name_generator(firstname,gender,number):#第一个参数为姓氏，第二个参数为性别，第三个参数为数量，最终生成一个列表
    girl= "秀娟英华慧巧美娜静淑惠珠翠雅芝玉萍红娥玲芬芳燕彩春菊兰凤洁梅琳素云莲真环雪荣爱妹霞香月莺媛艳瑞凡佳嘉琼勤珍贞莉桂娣叶璧璐娅琦晶妍茜秋珊莎锦黛青倩婷姣婉娴瑾颖露瑶怡婵雁蓓纨仪荷丹蓉眉君琴蕊薇菁梦岚苑婕馨瑗琰韵融园艺咏卿聪澜纯毓悦昭冰爽琬茗羽希宁欣飘育滢馥筠柔竹霭凝晓欢霄枫芸菲寒伊亚宜可姬舒影荔枝思丽"
    boy = "伟刚勇毅俊峰强军平保东文辉力明永健世广志义兴良海山仁波宁贵福生龙元全国胜学祥才发武新利清飞彬富顺信子杰涛昌成康星光天达安岩中茂进林有坚和彪博诚先敬震振壮会思群豪心邦承乐绍功松善厚庆磊民友裕河哲江超浩亮政谦亨奇固之轮翰朗伯宏言若鸣朋斌梁栋维启克伦翔旭鹏泽晨辰士以建家致树炎德行时泰盛雄琛钧冠策腾楠榕风航弘"
    result=[]
    if(gender=='男'):
        for i in range(int(number)):
            if(randint(0,20)>16):
                name=str(firstname)+choice(boy)
                result.append(name)
            else:
                name=str(firstname)+choice(boy)+choice(boy)
                result.append(name)
    else:
        for i in range(int(number)):
            if(randint(0,20)>16):
                name=str(firstname)+choice(girl)
                result.append(name)
            else:
                name=str(firstname)+choice(girl)+choice(girl)
                result.append(name)
    return result
```
### 百度百科
```
def baidubaike(name):
    baseurl='https://baike.baidu.com/item/'
    headers={'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
    url=baseurl+str(name)

    response=requests.get(url,headers=headers)
    print(response.status_code)
    print(response.url)
    text=response.content.decode("utf-8")
    tree=html.fromstring(text) 

    result0=tree.xpath('//div[@class="basic-info cmn-clearfix"]//*/text()')
    result9="".join(result0)
    result99=re.sub("\[.*?]","",result9)
    result10=result99.replace("'",'')
    result1=result10.replace("\xa0","")
    result2=result1.split('\n')
    while "" in result2:
        result2.remove("")
    number=range(0,len(result2),2)
    s={}
    if(len(result2)%2==0):
        for i in number:
            s[result2[i]]=result2[i+1]
    else:
        pass
    return s
```
### 判断字符串是否全为中文字符,全为中文字符返回True，否则返回False
```
def is_all_chinese(strs):
    for _char in strs:
        if not '\u4e00' <= _char <= '\u9fa5':
            return False
    return True
```
### 判断字符串中是否含有中文字符，有中文字符返回True，否则返回False
```
def is_contains_chinese(strs):
    for _char in strs:
        if '\u4e00' <= _char <= '\u9fa5':
            return True
    return False
```
