
https://dockerdocs.cn/language/nodejs/develop/index.html
# 将图像作为容器运行

## .dockerignore
要在构建上下文中使用文件，Dockerfile引用指令（例如COPY指令）中指定的文件。为了提高构建的性能，可以通过在上下文目录中添加.dockerignore文件来排除文件和目录。为了缩短上下文加载时间，请创建一个.dockerignore文件并node_modules在其中添加目录。
node_modules

## 构建（或重建）我们的第一个Docker映像。
docker build --tag node-docker .

## 要在容器内运行图像，请使用docker run命令。该docker run命令需要一个参数，即图像名称
> 这里是直接将图像作为容器运行
docker run -d -p 8000:8000 --name rest-server node-docker

-p  该--publish命令的格式为[host port]:[container port]
-- name 命名容器, 不然是随机的
--rm 标志，它会在容器退出时自动删除它

## docker ps命令以查看正在运行的容器的列表

docker ps -a 查看所有容器


## ps rm

ps rm container-id
ps rmi img-name


# 将图像作为容器运行


##  创建mongo卷
docker volume create mongodb
docker volume create mongodb_config

## 我们将创建一个网络，我们的应用程序和数据库将使用该网络相互通信。该网络称为用户定义的桥接网络，它为我们提供了很好的DNS查找服务，可在创建连接字符串时使用该服务
docker network create mongodb

## 我们可以在容器中运行MongoDB，并将其附加到上面创建的卷和网络上。Docker将从Hub提取映像并在本地为您运行。
docker run -it --rm -d -v mongodb:/data/db \
  -v mongodb_config:/data/configdb -p 27017:27017 \
  --network mongodb \
  --name mongodb \
  mongo

 
## 我们可以建立我们的image。
docker build --tag node-docker .

## 让我们运行容器。但是这一次，我们需要设置CONNECTIONSTRING环境变量，以便我们的应用程序知道要使用哪个连接字符串来访问数据库。我们将在docker run命令中正确执行此操作。
docker run \
  -it --rm -d \
  --network mongodb \
  --name rest-server \
  -p 8000:8000 \
  -e CONNECTIONSTRING=mongodb://mongodb:27017/yoda_notes \
  node-docker

  ```
  使用接口测试是否链接
  curl --request POST \
  --url http://localhost:8000/notes \
  --header 'content-type: application/json' \
  --data '{
    "name": "this is a note",
    "text": "this is a note that I wanted to take while I was working on writing a blog post.",
    "owner": "peter"
    }'
  ```

