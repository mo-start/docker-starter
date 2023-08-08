# syntax=docker/dockerfile:1

# 以告诉Docker我们想要为应用程序使用什么基本映像。
FROM node:12.18.1

# 在NODE_ENV环境变量指定在其中的应用程序运行的环境（通常，开发或生产）。要提高性能，最简单的操作之一就是将设置NODE_ENV为production。
ENV NODE_ENV=production

# 为了使运行其余命令更容易，让我们创建一个工作目录。这指示Docker使用此路径作为所有后续命令的默认位置。这样，我们不必键入完整的文件路径，但可以根据工作目录使用相对路径。
WORKDIR /app

# 在运行之前npm install，我们需要将package.json和package-lock.json文件放入映像中。我们使用COPY命令来执行此操作。该 COPY命令有两个参数。第一个参数告诉Docker您要将哪些文件复制到映像中。第二个参数告诉Docker您要将文件复制到哪里。我们将把package.json和package-lock.json文件复制到我们的工作目录中/app。

COPY ["package.json", "package-lock.json*", "./"]

# 一旦将package.json文件包含在映像中，就可以使用该RUN命令执行命令npm install。它的工作原理与我们在计算机上本地运行npm install时完全相同，但是这次这些Node模块将安装到node_modules映像内的目录中。

RUN npm install --production


