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
