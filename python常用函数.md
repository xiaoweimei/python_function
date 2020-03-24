### 在桌面创建文件夹
```
desktopPath=os.path.join(os.path.expanduser("~"),'Desktop')
base_path=os.path.join(desktopPath,"data_folder")#存放最终文件的路径
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
        jiaoyanzhi = int(idnumber[-1])
        jiaoyanlist = ['1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2']
        if (int(jiaoyanlist[xishuhe % 11]) == jiaoyanzhi):
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
