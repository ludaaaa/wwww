# Установка Докера

sudo yum install wget

sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io

sudo systemctl enable docker --now

# Установка компос

sudo yum install curl

COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

sudo chmod +x /usr/bin/docker-compose

sudo docker-compose --version

# Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker, создаёт папки /mnt/common_volume/swarm/grafana, /mnt/common_volume/grafana, помещает туда конфигурации и запускает контейнеры. 

sudo git clone https://github.com/skl256/grafana_stack_for_docker.git

# Заходит в папку - cd

# cd .. - возвращает в папку выше

cd grafana_stack_for_docker

# Cоздает папку - mkdir

sudo mkdir -p /mnt/common_volume/swarm/grafana/config

sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

# Выдача прав 

sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana} 

# Создает файл - touch

sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini 

# Копирование файлов - cp

sudo cp config/* /mnt/common_volume/swarm/grafana/config/

# переименовывает файл

sudo mv grafana.yaml docker-compose.yaml

# собрать докер, нужно запускать из папки где docker-compose.yaml

docker compose up -d

# установка git

sudo yum install git

# выйти не сохраняясь из vim

esc -> :q!

# выйти сохраняясь из vim

esc -> :wq!

# эскпортер

node-exporter:
    image: prom/node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    container_name: exporter
    hostname: exporter
    command:
      - --path.procfs=/host/proc
      - --path.sysfs=/host/sys
      - --collector.filesystem.ignored-mount-points
      - ^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)
    ports:
      - 9100:9100
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default
