# Dockerfile
```Dockerfile
FROM node:20-alpine3.16

WORKDIR /app

COPY package.json .
COPY package-lock.json .
COPY tsconfig.json .
COPY ecosystem.config.js .
COPY .env.production .
COPY ./src ./src
COPY ./openapi ./openapi

RUN apk add --no-cache ffmpeg
RUN apk add python3
RUN npm install pm2 -g
RUN npm install
RUN npm run build

EXPOSE 4000

CMD ["pm2-runtime", "start", "ecosystem.config.js"]
```
- `FROM node:20-alpine3.16`: Image
- `WORKDIR /app`: thư mục làm việc (trong hệ điều hành `alpine3.16`), các lệnh (ngoại trừ lệnh `COPY`) sẽ thực hiện trong thư mục này
- `COPY package.json .`: copy file này vô thư mục /app (root)
- `COPY ecosystem.config.js .`: file cấu hình của PM2 quản lý và chạy ứng dụng
- `COPY ./src ./src`: copy folder này vô thư mục /app/src
- `RUN apk add python3`: sẽ cài vào hệ điều hành `alpine3.16`
- `RUN npm install pm2 -g`: cài đặt package cho ứng dụng (-g: toàn cục trên hệ điều hành)
- `EXPOSE 4000`: port của `Container` để truy cập từ bên ngoài
- `CMD ["pm2-runtime", "start", "ecosystem.config.js", "--env", "production"]`: CMD cần chạy khi chạy Container, sử dụng Array thay vì dấu cách như **pm2-runtime start ecosystem.config.js --env production**
- Toàn bộ được Docker cài lên máy ảo (hệ điều hành alpine3.16)
- Trong trường hợp cần `Mapping` giữa máy ảo Docker và máy thật thì xem `Docker Volume`