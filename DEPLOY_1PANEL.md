# 1Panel 部署说明

这个项目是 Vite + React 静态站点。推荐优先使用 1Panel 的静态网站方式部署；如果想统一用 Docker 管理，也可以使用本仓库的生产镜像配置。

## 方案一：静态网站部署

适合绑定独立域名.

1. 确认 `gitprofile.config.ts` 的 `base` 与访问路径一致。

   - 独立域名根路径：`base: '/'`
   - 子路径：`base: '/gitprofile/'`
2. 本地或服务器执行：

   ```bash
   npm ci
   npm run build
   ```

3. 在 1Panel 中进入 `网站`，创建 `静态网站`。
4. 将 `dist/` 目录中的文件上传到该网站根目录。
5. 在网站设置里绑定域名，并按需开启 HTTPS。

### `mygithub.aktools.cn` 推荐步骤

1. 在 DNS 服务商处添加解析记录：

   - 主机记录：`mygithub`
   - 记录类型：`A`
   - 记录值：你的云服务器公网 IPv4

2. 在服务器上进入项目目录并构建：

   ```bash
   npm ci
   npm run build
   ```

3. 在 1Panel 中创建静态网站：

   - 主域名：`mygithub.aktools.cn`
   - 运行目录：网站根目录
   - 站点内容：上传 `dist/` 目录里的所有文件

4. 访问确认页面正常。
5. 在 1Panel 网站设置中申请 HTTPS 证书，开启强制 HTTPS 后访问。

## 方案二：Docker Compose 部署

适合希望由 1Panel 的容器或编排功能管理项目。

1. 将项目上传到服务器，例如 `/opt/gitprofile`。
2. 在 1Panel 的容器编排中使用 `docker-compose.prod.yml`，或在服务器执行：

   ```bash
   docker compose -f docker-compose.prod.yml up -d --build
   ```

3. 容器会监听宿主机 `8080` 端口。
4. 在 1Panel 中创建 `反向代理` 网站：

   - 主域名：你的域名
   - 代理地址：`http://127.0.0.1:8080`
   - HTTPS：按需开启

如果部署在根域名下，`docker-compose.prod.yml` 中已经设置 `VITE_BASE_PATH: /`。如果要部署到子路径，请同步修改该值。

## 常见问题

- 页面空白或资源 404：通常是 `base` 与实际访问路径不一致。根域名用 `/`，子路径用 `/子路径/`。
- 端口打不开：检查云服务器安全组、系统防火墙和 1Panel 防火墙是否放行 80/443；Docker 方案还要确认 8080 只作为本机反代端口使用。
- GitHub 资料加载慢或失败：站点运行后会从 GitHub 拉取公开数据，服务器或访客网络需要能访问 GitHub API。
