- wsl -l -v hiện tiến trình tất cả tài nguyên máy ảo đang có như docker-destop, ubuntu,....
- wsl --shutdown là tắt hết tất cả tài nguyên máy ảo (terminal admin)
- wsl --version
- wsl --update
- wsl --unregister DISTRO-NAME (wsl --unregister Ubuntu)

- docker [OPTIONS] COMMAND

Option:
--tag,-t Đặt tên và tùy chọn một thẻ (định dạng: name:tag)

Common Commands:
run Create and run a new container from an image
exec Execute a command in a running container
ps List containers
build Build an image from a Dockerfile
pull Download an image from a registry
push Upload an image to a registry
images List images
login Log in to a registry
logout Log out from a registry
search Search Docker Hub for images
version Show the Docker version information
info Display system-wide information

- docker build -t getting-started [-f Dockerfile.dev] . || docker build -t getting-started:tagname .

* [-f | --file] build dockerfile.dev
* The docker build command uses the Dockerfile to build a new container image.
* "-t" cờ gắn thẻ hình ảnh của bạn. Hãy nghĩ về điều này đơn giản như một cái tên mà con người có thể đọc được cho hình ảnh cuối cùng. Vì bạn đã đặt tên cho hình ảnh getting-startednên bạn có thể tham khảo hình ảnh đó khi chạy vùng chứa.
* The . at the end of the docker build command tells Docker that it should look for the Dockerfile in the current directory.
* :tagname -> định danh version cho images

- docker run -dp 3000:3000 [--name name] getting-started

* Bạn sử dụng "-d" cờ để chạy container mới ở chế độ “detached” (in the background). là chạy ngầm còn nếu không có thì chạy trực tiếp trên terminal nếu tắt terminal thì tắt lun ứng dụng
* Bạn cũng sử dụng "-p" cờ để tạo ánh xạ giữa cổng 3000 của máy chủ đến cổng 3000 của bộ chứa. Nếu không có ánh xạ cổng, bạn sẽ không thể truy cập ứng dụng.
* 3001:3000 là ánh xạ port 3001 trong localhost(windown) qua port 3000 trong container , khi chạy thì trên windown có thể truy cập qua port 3001

* Chạy lệnh sau "docker ps" trong terminal để liệt kê các container đang hoạt động của bạn.
* "docker ps -a" trong terminal để liệt kê tất cả container kể cả không hoạt động của bạn.
* "docker image ls" và "docker images" xem list images

- delete container: - Use the docker stop command to stop the container. Replace <the-container-id> with the ID from docker ps.
  docker stop <the-container-id | name>

      - Once the container has stopped, you can remove it by using the docker rm command.

  docker rm <the-container-id | name>

      - You can stop and remove a container in a single command by adding the force flag to the docker rm command. For example:
      	docker rm -f <the-container-id | name>

  +delete image: - docker rmi <the-image-id | name>

* docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG] + tạo ra TARGET_IMAGE dựa trên SOURCE_IMAGE
  ex: - docker tag httpd fedora/httpd:version1.0 - docker tag httpd:test fedora/httpd:version1.0.test

* docker hub: docker push SOURCE_IMAGE[:TAG]

  - docker push trichoip/getting-started:version1

* cách update src code mới lên docker

  - build lại image không cần xóa
  - remove hoặc stop container nếu cùng port và run container lại

* truy cập terminal của container

  - docker exec <container-id> <command terminal>  
    -khi chạy lệnh này thì terminal của container sẽ thực hiện 1 lần <command terminal> thực hiện xong thì thì out ra ngoài
  - docker exec -it <container-id> sh -> thao tác liên tục trên terminal của container, bắt buộc phải có -it
    -i : là cho phép giao tiếp thông qua STDIN (tiêu chuẩn đầu vào) với container.(là cho phép thực hiện các câu command terminal trên container)
    -t : tạo một pseudo-TTY (terminal) để kết nối với container.(tạo ra giao diện terminal)

* volume: lưu trữ file trong container vào ổ đỉa volume docker nếu file container thay đổi thì nó backup vào docker

  - docker volume create todo-db
  - docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
  - docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
  - docker volume inspect todo-db -> xem thông tin lưu trữ volume
  - (local backup)(\\wsl.localhost\docker-desktop-data\data\docker\volumes)
  - docker volume ls -> danh sach volume
  - nếu không tạo volume todo-db trước mà sử dụng thì docker tự tạo volunm todo-db

* binding:
  - docker run -dp 3000:3000 -w /app -v "$(pw/d):/app" node:18-alpine sh -c "yarn install && yarn run dev"
  - docker run -dp 3000:3000 -w /app -v "$(pwd):/app" getting-started
  - docker run -dp 3000:3000 -w /app --mount "type=bind,src=$pwd,target=/app" node:18-alpine sh -c "yarn install && yarn run dev"
  - docker run -dp 3000:3000 -w /app --mount "type=bind,src=$(pwd),target=/app" getting-started
* -w /app - sets the “working directory” or the current directory that the command will run from (nếu không có thì container tự tạo ra, đặt thư mục làm việc hiện tại của vùng chứa nơi lệnh sẽ chạy từ đó)
* --mount type=bind,src="$(pwd)",target=/app - bind mount the current directory from the host into the /app directory in the container
* $(pwd) là để in đường dẫn tuyệt đối của thư mục làm việc

* docker logs [-f] <container-id> hiện log của container

  - nếu không có -f thì terminal chỉ hiện status logs lúc đó và thoát ra
  - nếu có -f thì terminal sẽ hiện status log và không thoát ra

* network:
  - để sử dụng network phải tạo ra từ trước không giống như volume
  - docker network create todo-app
* mysql

  - mysql_config:/etc/mysql/conf.d
  - mysql_data:/var/lib/mysql
  - db mysql container -> /var/lib/mysql
  - docker run -d --network todo-app --network-alias mysqlflag -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0
  - docker exec -it <mysql-container-id> mysql -u root -p
  - docker exec -it <mysql-container-id> mysql -p todos

* --network todo-app -> chọn network todo-app
* --network-alias -> định danh cho container sử dụng network todo-app là "mysql"
* -e -> Environment Variables

  - check ip network của container

    - docker run -it --network todo-app nicolaka/netshoot (--network todo-app -> chọn network todo-app)
    - dig mysqlflag (mysqlflag là --network-alias mysqlflag khi cấu hình network cho container)

  - docker run -dp 3000:3000 ` -w /app -v "$(pwd):/app"`
    --network todo-app ` -e MYSQL_HOST=mysqlflag`
    -e MYSQL_USER=root ` -e MYSQL_PASSWORD=secret`
    -e MYSQL_DB=todos ` node:18-alpine`
    sh -c "yarn install && yarn run dev"

  * MYSQL_HOST- tên máy chủ cho máy chủ MySQL đang chạy(--network-alias mysqlflag)
  * MYSQL_USER- tên người dùng để sử dụng cho kết nối
  * MYSQL_PASSWORD- mật khẩu để sử dụng cho kết nối
  * MYSQL_DB- cơ sở dữ liệu để sử dụng sau khi kết nối

* Docker Compose:

  - docker compose version
  - docker compose logs -f (nếu có có alias thì log tất cả)
  - docker compose logs -f mysqlflag (mysqlflag ở đây là network-alias trong docker-compose.yml)
  - docker compose [-f filename] up -d (name cho container tổng , volume , network là name của folder chứa docker-compose.yml)
  - docker compose down --volumes (nếu không có --volumes thì nó chỉ xóa hết container và network, còn nếu có thì nó xóa thêm volumes)

* Dockerfile:
  FROM node:18-alpine
  WORKDIR /app
  COPY package.json yarn.lock ./
  RUN yarn install --production
  COPY . .
  CMD ["node", "src/index.js"]
  => [2/5] WORKDIR /app 0.0s
  => [3/5] COPY package.json yarn.lock ./ 0.0s
  => [4/5] RUN yarn install --production 0.0s
  => [5/5] COPY . .

=> CACHED [2/5] WORKDIR /app 0.0s
=> CACHED [3/5] COPY package.json yarn.lock ./ 0.0s
=> CACHED [4/5] RUN yarn install --production 0.0s
=> CACHED [5/5] COPY . .

- nên sắp sếp thứ tự tiến trình làm việc trong dockerfile vì khi chạy lần đầu dockerfile sẽ lưu vào cache nếu có thay đổi tiến trình nào thì mới chạy lại tiến trình làm việc đó trở đi
  ex: nếu chạy lần đầu xong thì khi thay đổi src code thì build lại thì nó sẽ bỏ qua tiến trình 2 3 4 và chỉ chạy lại tiến trình 5, nếu package.json thay đổi thì nó sẽ chạy lại từ tiến trình 3 - 5
- vì vậy hãy để cái nào thay đổi liên tục xuống dưới cùng , cái nào it thay đổi thì đưa lên trên cùng, ví dụ nhứ build lib thì đưa lên đầu, còn code thì xuống dưới cùng

.dockerignore -> khi copy sẽ không copy thư mục chỉ định trong file .dockerignore

- CMD được sử dụng để xác định lệnh mặc định cho container khi nó chạy từ hình ảnh, trong khi RUN được sử dụng để thực thi lệnh trong quá trình xây dựng hình ảnh Docker.

- save image :
  - docker save --output [-o] myimage.tar myimage
  - docker load -i myimage.tar

mvn spring-boot:build-image
host.docker.internal

--legacy-peer-deps

ENV mysql
MYSQL_TCP_PORT ( The default TCP/IP port number.)
