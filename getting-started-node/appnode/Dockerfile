# syntax=docker/dockerfile:1
FROM node:18-alpine
WORKDIR /app
COPY package.json yarn.lock ./
# RUN được sử dụng để thực thi một lệnh trong quá trình xây dựng hình ảnh Docker.
# Chỉ thị RUN có thể xuất hiện nhiều lần trong Dockerfile
RUN yarn install --production
COPY . .
# CMD được sử dụng để chỉ định lệnh mặc định mà container sẽ thực thi khi khởi chạy từ hình ảnh.
# Đối với câu lệnh này, khi container được khởi chạy, nó sẽ tự động thực thi lệnh node src/index.js.
# Chỉ thị CMD chỉ được định nghĩa duy nhất một lần trong Dockerfile và nếu có nhiều chỉ thị CMD, chỉ có chỉ thị cuối cùng sẽ được áp dụng.
CMD ["node", "src/index.js"]


# Maven/Tomcat example
# syntax=docker/dockerfile:1
# FROM maven AS build
# WORKDIR /app
# COPY . .
# RUN mvn package

# FROM tomcat
# COPY --from=build /app/target/file.war /usr/local/tomcat/webapps 


# React example
# syntax=docker/dockerfile:1
# FROM node:18 AS build
# WORKDIR /app
# COPY package* yarn.lock ./
# RUN yarn install
# COPY public ./public
# COPY src ./src
# RUN yarn run build

# FROM nginx:alpine
# COPY --from=build /app/build /usr/share/nginx/html