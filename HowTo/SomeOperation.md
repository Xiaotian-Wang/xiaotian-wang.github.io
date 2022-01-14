## 1. 打开cmd命令提示符

## 2. 通过ssh连接服务器

在cmd中，输入
```
ssh xiaotian@10.216.6.224
```
密码是：xiaotian

成功连接后，进入路径
```
cd /home/xiaotian/frontend_xiaotian/dist
```
进入后，操作该目录下的dist文件。

## 3. 将dist文件（或文件夹）复制到服务器

在cmd中，输入命令
```
scp -r local/file/to/copy xiaotian@10.216.6.224:remote/path/to/save/file
```
其中，如果复制的内容是文件夹，则加-r，否则不加r

## 4. 重启vue的docker容器

利用ssh连接服务器后，输入
```
sudo docker restart vue-frontend
```
即可重启vue的docker容器

## 5. 查看前端页面

浏览器输入10.216.6.224:8833访问前端页面
