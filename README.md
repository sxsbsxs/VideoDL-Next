# VideoDL-Next

HTML 前端 + C++17 后端的视频链接信息读取与下载项目，面向后续 Cloudflare 部署。

## 目录

```
.
├── index.html
├── backend/
│   ├── main.cpp
│   └── CMakeLists.txt
├── Dockerfile
└── README.md
```

## 当前版本

- 手机/桌面自适应 HTML 界面
- C++ HTTP API：POST /api/resolve
- 读取网页 Open Graph 标题、封面等公开元数据
- 对直接媒体 URL 提供 GET /api/download 下载代理
- Docker 多阶段构建

**当前还不是 yt-dlp 的替代品。** 目前不能解析 YouTube、Bilibili、TikTok 等平台的签名流、分段流、登录态或 DRM 内容；需要后续逐个平台实现 C++ extractor。

## 本地运行

需要 C++17、CMake、Git、libcurl 开发包。

```bash
cmake -S backend -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j2
./build/videodl-api
```

API 默认监听 0.0.0.0:8080，可通过 PORT 修改。打开 index.html 后，前端默认请求 http://localhost:8080。部署时请把 window.VIDEODL_API 设置为后端 API 的 HTTPS 地址。

## Docker

```bash
docker build -t videodl-api .
docker run --rm -p 8080:8080 -e PORT=8080 videodl-api
```

## Cloudflare 部署规划

- 前端：Cloudflare Pages，发布仓库根目录。
- C++ API：构建 Docker 镜像，使用 Cloudflare Containers 部署，再通过 Worker/容器路由公开 API。
- Pages 本身不能直接运行原生 C++ 服务；容器路由与 Worker 配置在部署阶段按账号可用产品补齐。

## 安全注意

这是开发起步版本，不要直接公开部署。正式开放前必须增加 SSRF 防护（拒绝内网/环回/链路本地地址并防止重定向绕过）、请求大小和速率限制、超时、下载流式传输及 CORS 来源限制。当前下载代理会缓冲响应，不适合大文件或高并发。

## 许可证

暂未指定许可证；确定许可证后再添加 LICENSE 文件。
