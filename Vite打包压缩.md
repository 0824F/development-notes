# Vite Web 项目打包

## 遇到问题

- 打包后，js 文件太大，导致页面加载速度过慢

## 解决方案

vite.config.js 中配置

```ts
// 配置打包后js文件大小
export default defineConfig({
  plugins: [
    // gzip压缩
    VitePluginCompression({
      algorithm: "gzip",
      ext: ".gz",
      verbose: true,
      disable: false,
      threshold: 10240,
      deleteOriginFile: false,
    }),
  ],
  build: {
    // 分包
    rollupOptions: {
      output: {
        manualChunks(id) {
          if (id.includes("node_modules")) {
            return "vendor";
          }
        },
      },
    },
  },
});
```

docker 配置

```dockerfile
FROM nginx:stable-alpine
COPY /build /usr/share/nginx/html

RUN  sed -i '12a error_page 404 /index.html;' /etc/nginx/conf.d/default.conf

// 开启gzip
RUN sed -i '/^http {/a \
    gzip on;\n\
    gzip_static on;' /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
