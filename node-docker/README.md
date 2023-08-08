
https://dockerdocs.cn/language/nodejs/develop/index.html

# Dockerfile
Dockerfile是一个文本文档，其中包含用户可以在命令行上调用以组装映像的所有命令。当我们告诉Docker通过执行docker build命令来构建映像时，Docker读取这些指令并逐个执行它们，并创建一个Docker映像。

让我们逐步为应用程序创建一个Dockerfile的过程。在工作目录的根目录中，创建一个名为的Dockerfile文件，然后在文本编辑器中打开该文件

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



# compose

## docker-compose -f docker-compose.dev.yml up --build
传递该--build标志，以便Docker编译我们的映像，然后启动它。


# 调试
- npm install nodemon
- package.json: npm install nodemon
- 浏览器中输入 chrome://inspect/#devices

# 单元测试
> 使用与上面相同的docker run命令，但是这次，我们将使用npm run test覆盖容器内部的CMD。这将调用package.json文件中“脚本”部分下的命令。见下文。
docker-compose -f docker-compose.dev.yml run notes npm run test
