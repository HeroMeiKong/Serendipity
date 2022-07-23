# Docker 笔记

1. `docker ps`：列出正在运行的容器
2. `docker ps -a`：列出所有容器及状态
3. `docker start CONTAINER-ID`: 重启镜像
4. `docker exec -it docker_CONTAINER-ID_or_NAMES bash` (可使用 CONTAINER ID 或者 NAMES 进入，不能使用 IMAGE)：进入正在运行的 Docker 容器
5. `docker run -it docker_IMAGE bash` (只能使用 IMAGE)：进入未运行的 Docker 容器并启动它
