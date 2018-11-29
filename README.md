# springboot-fastdfs
> clone sample git hub author , thanks https://github.com/happyfish100/fastdfs provide file 
#### zhihu zhuanlan look docker publish docker fastdfs 
> https://zhuanlan.zhihu.com/artskill

#### upload sucessful
![upload success](https://github.com/andotorg/springboot-fastdfs/blob/master/img/df.png)

### upload page
![upload success](https://github.com/andotorg/springboot-fastdfs/blob/master/img/upload.png)


### docker pull images
```
docker pull morunchang/fastdfs
```

### run tracker
```shell
docker run -d --name tracker --net=host -e GROUP_COUNT=2 -e TRACKER_PORT=22122 morunchang/fastdfs sh tracker.sh
docker run -d --name tracker --net=host -e GROUP_COUNT=2 -e TRACKER_PORT=22123 morunchang/fastdfs sh tracker.sh
```

### run storage
```shell
docker run -d --name storage1 --net=host -v <<server data address>>:/data/fast_data -e TRACKER_IP=10.0.75.2:22122 -e GROUP_NAME=andot -e STORAGE_PORT=23001 morunchang/fastdfs sh storage.sh

docker run -d --name storage2 --net=host -v <<server data address>>:/data/fast_data -e TRACKER_IP=10.0.75.2:22122 -e GROUP_NAME=andot -e STORAGE_PORT=23001 morunchang/fastdfs sh storage.sh
```

### update resource request port
> in storage nginx config listen port

```shell
# docker exec -it 1a44d2968752 /bin/bash
# vi /etc/nginx/conf/nginx.conf
$ update listen port 
```

### update tracker config file
```
vi /etc/fdfs/mod_fastdfs.conf
[group1]
group_name=mine1
storage_server_port=23000
store_path_count=1
store_path0=/data/fast_data
[group2]
group_name=mine2
storage_server_port=23001
store_path_count=1
store_path0=/data/fast_data
```

### update storage config file
```
vi /etc/fdfs/storage.conf
tracker_server=10.0.75.2:22122
tracker_server=10.0.75.2:22123
```

### FastDFS file download setting old file name 
文件被上传到FastDFS后Storage服务端将返回的文件索引（FID），其中文件名是根据FastDFS自定义规则重新生成的 例如：wKgB-lkdxUmAPb-QAAIbD3CxJDw317.txt，而不是原始文件名,使用http下载时如不加处理，显示给用户的文件名会是这样的wKgB-lkdxUmAPb-QAAIbD3CxJDw317.txt，这样的用户体验很不好。由于FastDFS不会存储原始文件名，也不提供回复原始名的方法，我们需要自己实现

那么就需要我们将原始文件名记录在数据库中，在下载的时候将原文件名传递到服务器，然后用nginx获取到原始文件名，在写入响应头里面

> http://192.168.1.124:8000/andot/M00/00/00/wKgB-Vkb2yuAEk80AAAABpDVNbM781.txt?name=name.txt

```java
if ($arg_name ~* \.(doc|docx|txt|pdf|zip|rar|txt)$) {
    add_header Content-Disposition "attachment;filename=$arg_name";
}
```

#### SpringBoot project source download
> https://github.com/andotorg/springboot-fastdfs.git
