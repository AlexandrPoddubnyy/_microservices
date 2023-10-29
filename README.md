# AlexandrPoddubnyy_microservices
AlexandrPoddubnyy microservices repository

====================
Домашнее задание №12:
====================
    не поступало

====================
Домашнее задание №13:
====================

## В процессе сделано:

    Кратко:
    По плану ДЗ
        Создание docker host
        Создание своего образа
        Работа с Docker Hub
    + Выполнено
        Первое задание со *

    Подробнее, по планам, слайдам и хистори:
    (Слайды //Разделы /// План)
    // Технология контейнеризации. Введение в Docker
    /// Создание docker host
    План
    Проект microservices и проверка ДЗ
        git commit  -m "HW13 ansible-2. precommits"
    Директория dockermonolith
        mkdir docker-monolith
    // Устанавливаем Docker
        docker
        ? docker-compose -v
        docker-machine -v
    Устанавливаем Docker
        итого #zypper up
        #zypper in docker docker-bash-completion
        #systemctl start docker
        > sudo usermod -aG docker $USER
        docker run hello-world
        docker image ls
        docker ps -a
    Устанавливаем Docker
        docker version
        docker info
    Устанавливаем Docker
    Docker hello-world
        docker run hello-world
    Что произошло?
    Docker ps
        docker ps
        docker ps -a
    Docker images
        docker image ls
    Docker run
        docker run -it ubuntu:18.04 /bin/bash
    Docker run
    Docker ps
        docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.CreatedAt}}\t{{.Names}}"
    Docker start & attach
        docker start 395
        docker attach 395
        Ctrl+p, Ctrl+q
        docker ps -a
    Docker run vs start
          ** % docker run = docker create + docker start + docker attach*
          *при наличии опции -i
    Docker run
          **  Через параметры передаются лимиты (cpu/mem/disk), ip, volumes
            -i – запускает контейнер в foreground режиме ( docker attach )
            -d – запускает контейнер в background режиме
            -t создает TTY
        docker run -it ubuntu:18.04 bash
        docker run -dt nginx:latest
    Docker exec
    Docker exec
        docker exec -it 5a6 "/bin/bash"
    Docker commit
        docker commit 395 yourname/ubuntu-tmp-file
        docker images
        _microservices/docker-monolith> docker images > docker-1.log
    Задание со *
        edit docker-1.log
    Docker kill & stop
        docker stop 5a6
    Docker kill
        docker run -dt yourname/ubuntu-tmp-file
        docker ps -q
        docker kill 1c7
    docker system df
        docker system df
    docker system df
    Docker rm & rmi
        docker rm 7d0 d97
        docker rm c295 395 -f
    Docker rm & rmi
        docker rm $(docker ps -a -q)     # удалит все незапущенные контейнеры
        # docker rmi $(docker images -q)   # удалит все образы

    // Docker-контейнеры
    /// Создание своего образа
    yc
    yc init
    Docker machine
        %% FROM https://github.com/docker/machine/releases
            curl -L https://github.com/docker/machine/releases/download/v0.16.2/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine &&
            chmod +x /tmp/docker-machine &&
            sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
        > docker-machine -v
        docker-machine version 0.16.2, build bd45ab13
            %%  Команда создания - docker-machine create <имя> .
                Имен может быть много, переключение между ними через eval $(docker-machine env <имя>) .
                Переключение на локальный докер - eval $(docker-machine env --unset) .
                Удаление - docker-machine rm <имя> .
    Docker machine
            %%  yc создаст инстанс из стандартного образа в image-family ubuntu-1804-lts
                docker-machine инициализирует на нём докер хост систему
                Все докер команды, которые запускаются в той же консоли после eval $(docker-machine env <имя>) работают с удаленным докер демоном в Yandex Cloud.
    Docker machine
        yc compute instance create \
            --name docker-host \
            --zone ru-central1-a \
            --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
            --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
            --ssh-key ~/.ssh/id_rsa.pub
    Docker machine
                %% address: 158.160.125.105
        docker-machine create \
            --driver generic \
            --generic-ip-address=158.160.125.105 \
            --generic-ssh-user yc-user \
            --generic-ssh-key ~/.ssh/id_rsa \
            docker-host
    Docker machine
        docker-machine ls
        eval $(docker-machine env docker-host)
    Повторение практики из демо на лекции
        docker run -dt nginx:latest
        docker stop 367
        docker rm 367
    Повторение практики из демо на лекции
        docker run --rm            -ti tehbilly/htop
        docker run --rm --pid host -ti tehbilly/htop
    Структура репозитория
        docker-monolith> touch Dockerfile
        touch mongod.conf
        touch db_config
        touch start.sh
    mongod.conf
        edit mongod.conf
    start.sh
        edit start.sh
    db_config
        edit db_config
    Dockerfile X6
        edit Dockerfile
    Сборка образа
            => ERROR [ 4/10] RUN gem install bundler                                                                                                                                              27.4s
            ------
            > [ 4/10] RUN gem install bundler:
            #8 27.24 ERROR:  Error installing bundler:
            #8 27.24        The last version of bundler (>= 0) to support your Ruby & RubyGems was 2.3.26. Try installing it with `gem install bundler -v 2.3.26`
            #8 27.24        bundler requires Ruby version >= 2.6.0. The current ruby version is 2.5.0.
            ------
            process "/bin/sh -c gem install bundler" did not complete successfully: exit code: 1
        edit Dockerfile
            ?  RUN gem install bundler -v 2.3.26
        docker build -t reddit:latest .
    Сборка образа
        docker images -a
    Запуск контейнера
        docker run --name reddit -d --network=host reddit:latest
        docker ps
        docker-machine ls
        cp Dockerfile Dockerfile.ORIG
        edit Dockerfile
        docker build -t reddit:latest .
            # Норм, но !! на запушенном контейнере нельзя зарегиться в приложение
        edit Dockerfile
        docker build -t reddit_16:latest_16 .
            #7 146.3 Cannot allocate memory - /usr/bin/ruby2.3 -r ./siteconf20231008-8-1t7zg3d.rb extconf.rb 2>&1
                sudo fallocate -l 1G /swapfile
                mkswap /swapfile
                chmod 600 swapfile
                swapon /swapfile
        edit Dockerfile
        docker build -t reddit_16:latest_16 .
            #4 461.8 An error occurred while installing net-ssh (7.2.0), and Bundler cannot continue.
            #4 461.8 Make sure that `gem install net-ssh -v '7.2.0'` succeeds before bundling.
            ------
            process "/bin/sh -c cd /reddit && rm Gemfile.lock && bundle install" did not complete successfully: exit code: 5
        edit Dockerfile
        docker build -t reddit_old:latest_old .
        docker run --name reddit_old -d --network=host reddit_old:latest_old
        WORK- http://158.160.125.105:9292/

    // Docker hub: регистрация
    /// Работа с Docker Hub
    Docker hub: регистрация
        регистрация
    Docker hub: аутентификация
        docker login
    Docker hub: push
        docker tag reddit_old:latest_old alexandrpoddubnyy/otus-reddit:1.0
        docker push alexandrpoddubnyy/otus-reddit:1.0
    Проверка
        (в другой консоли) docker run --name reddit -d -p 9292:9292 alexandrpoddubnyy/otus-reddit:1.0
        WORK- http://127.0.0.1:9292/
    Ещё проверка
        docker ps
        docker logs 63a -f
        docker exec -it reddit bash  # killall5 1
        docker start 63a
    Ещё проверка
        docker start reddit
        docker stop reddit && docker rm reddit
        docker run --name reddit --rm -it /otus-reddit:1.0 bash
            docker: invalid reference format.
        docker run --name reddit --rm -it alexandrpoddubnyy/otus-reddit:1.0 bash
    И ещё проверка X2
        > docker inspect /otus-reddit:1.0
            Error: No such object: /otus-reddit:1.0
        docker inspect alexandrpoddubnyy/otus-reddit:1.0
        docker inspect alexandrpoddubnyy/otus-reddit:1.0  -f '{{.ContainerConfig.Cmd}}'
        docker run --name reddit -d -p 9292:9292 alexandrpoddubnyy/otus-reddit:1.0
        docker exec -it reddit bash
            mkdir /test1234
            touch /test1234/testfile
            rmdir /opt
            exit
        docker diff reddit
        docker stop reddit && docker rm reddit
        docker run --name reddit --rm -it alexandrpoddubnyy/otus-reddit:1.0 bash
            ls /
    Задание со * (№2)
        не рассматривалось


## Как запустить проект:

        > docker run --name reddit -d -p 9292:9292 alexandrpoddubnyy/otus-reddit:1.0

## Как проверить работоспособность:

        Например, перейти по ссылке http://127.0.0.1:9292/


====================
Домашнее задание №14:
====================

## В процессе сделано:

    Кратко:
    Отработано По плану, целям ДЗ
        Цели задания
	    Научиться описывать и собирать Docker-образы для сервисного приложения
	    Научиться оптимизировать работу с Docker-образами
	    Запуск и работа приложения на основе Docker-образов, оценка удобства запуска контейнеров при помощи docker run
	План
	    Разбить наше приложение на несколько компонентов
	    Запустить наше микросервисное приложение
    + Выполнено
        Второе задание со *

## Как запустить проект:

        cd src
        docker build -t alexandrpoddubnyy/ui:1.0 ./ui
        docker build -t alexandrpoddubnyy/post:1.0 ./post-py
        docker build -t alexandrpoddubnyy/comment:1.0 ./comment
        docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db  -v reddit_db:/data/db 	mongo:4
        docker run -d --network=reddit --network-alias=post                               				alexandrpoddubnyy/post:1.0
        docker run -d --network=reddit --network-alias=comment                            				alexandrpoddubnyy/comment:1.0
        docker run -d --network=reddit -p 9292:9292                                       				alexandrpoddubnyy/ui:1.0

## Как проверить работоспособность:

        Например, перейти по ссылке http://<docker-host>:9292/


====================
Домашнее задание №15:
====================

## В процессе сделано:

    Кратко:
    Отработано По плану, целям ДЗ
	Docker: сети, docker-compose/ План
	     • Работа с сетями в Docker
	     • Использование docker-compose
	Работа с сетью в Docker / План
	     • Разобраться с работой сети в Docker
		  • none
		  • host
		  • bridge
	docker-compose / План
	     • Установить docker-compose на локальную машину
	     • Собрать образы приложения reddit с помощью docker-compose
	     • Запустить приложение reddit с помощью docker-compose

	Ответы на вопросы-задания внутри ДЗ:

	1. Вопрос про Сравнение вывода

	    docker run -ti --rm --network host joffotron/docker-net-tools -c ifconfig
	    docker-machine ssh docker-host ifconfig

	Ответ: при сравнении вывода замечено, что в eth0 и Lo для inet6 в контейнере есть дополнение в ipv6 адресе вида ...%32714/128 , примеры

	    inet6 addr: fe80::d20d:1cff:fe6c:eb69%32714/64 Scope:Link
	    inet6 addr: ::1%32714/128 Scope:Host

	чего на докер-хосте нет. С чем это связано выяснить не удалось. Как будто некорректный расчет, вывод маски внтри контейнера.
	В остальном конфигурация в выводе идентична (за исключением Ос-зависимого формата вывода).
	Ремарка - пингv6 c контейнера (и на самом докерхосте, настройки по дефолту) на яндексе не проходит

	    # ping6 www.google.com
	    PING www.google.com (2a00:1450:4010:c0d::67): 56 data bytes
	    ping6: sendto: Network unreachable


	2. Вопрос про многократный запуск комманды:

	    docker run --network host -d nginx - несколько раз

	Каков результат? Что выдал docker ps? Как думаете почему?
	Ответ: Остался в живых один, первый контейнер, остальные завершились, вероятно упали из-за невозможности повторного бинда порта.
	Подтверждение гипотезы-

	    ..AlexandrPoddubnyy_microservices> docker logs 384  2>&1 | tail -n 2
	    2023/10/17 17:31:34 [emerg] 1#1: still could not bind()
	    nginx: [emerg] still could not bind()


	3. Задание со стр. 35
	    1) docker-compose под кейс с множеством сетей, сетевых алиасов (стр 18). - изменен
	    2) Параметризирован
	    3) Парметризированные параметры выложены в отдельный файл c расширением .env
	    4) Проверено, docker-compose подхватывает переменные из этого файла
	    P.S. Файл .env указан в .gitignore, в репозитории закоммичен .env.example

	4. Задание со стр 36:
	    Узнайте как образуется базовое имя проекта. Можно ли его задать? Если можно то как?
	    Ответ добавьте в Readme.md данного ДЗ
	Ответ:
	    В выводе docker-compose --help есть ответ

	      -p, --project-name NAME     Specify an alternate project name
	                                  (default: directory name)

	т.е по дефолту задается имя директории. Пример использования без дефолта: docker-compose -p stage up -d


## Как запустить проект:
	docker-compose -p stage up -d

## Как проверить работоспособность:

        Например, перейти по ссылке http://<docker-host>:9292/


====================
Домашнее задание №16:
====================

## В процессе сделано:

    Кратко:
    Отработаны все задания в соотвествии с документом к ДЗ
        Подготовить инсталляцию Gitlab CI
        Подготовить репозиторий с кодом приложения
        Описать для приложения этапы пайплайна
        Определить окружения
    * Кроме заданий со *

## Как запустить проект:

        Основное. Создание ВМ для DZ
        > yc compute instance create \
            --name gitlab-ci-vm \
            --hostname gitlab-ci-vm \
            --memory=8 \
            --cores=2 \
            --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=50GB \
            --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
            --metadata serial-port-enable=1 \
            --metadata-from-file='user-data=ya_gitlab-ci-vm_startup.yaml'

        Подготовить докер на ней-
        > docker-machine create \
            --driver generic \
            --generic-ip-address=51.250.7.249 \
                    --generic-ssh-user ap1 \
                    --generic-ssh-key ~/.ssh/id_rsa \
                    gitlab-ci-vm
        docker-machine ls
        eval $(docker-machine env gitlab-ci-vm)

        На хосте с докером подготовиться, развернуть контейнер для Gitlab
            mkdir -p /srv/gitlab/config /srv/gitlab/data /srv/gitlab/logs
            cd /srv/gitlab
            touch docker-compose.yml
            vi docker-compose.yml
            cat docker-compose.yml
                        web:
                          image: 'gitlab/gitlab-ce:latest'
                          restart: always
                          hostname: 'gitlab.example.com'
                          environment:
                            GITLAB_OMNIBUS_CONFIG: |
                              external_url 'http://51.250.7.249'
                          ports:
                            - '80:80'
                            - '443:443'
                            - '2222:22'
                          volumes:
                            - '/srv/gitlab/config:/etc/gitlab'
                            - '/srv/gitlab/logs:/var/log/gitlab'
                            - '/srv/gitlab/data:/var/opt/gitlab'
            apt install docker-compose
            docker-compose up -d
            sudo docker exec -it a6d06 grep 'Password:' /etc/gitlab/initial_root_password

        Действия через вебинтерфейс Gitlab,
        Первоночальные подстройки, настройки узеров, групп и проектов, проверки работы папйплайнов тут -
            http://51.250.7.249/

        Добавление раннера, на хосте
            docker run -d --name gitlab-runner --restart always -v /srv/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest
        Регистрация раннера
            docker exec -it gitlab-runner gitlab-runner register \
                --url http://51.250.7.249/ \
                --non-interactive \
                --locked=false \
                --name DockerRunner \
                --executor docker \
                --docker-image alpine:latest \
                --registration-token XXXXXXXNNNNNNNNNNDDDDDDDDD \
                --tag-list "linux,xenial,ubuntu,docker" \
                --run-untagged
                !!! ..... 'register' command has been deprecated ...., но пока работает

        Работа локально с гитом
            git remote add gitlab http://51.250.7.249/homework/example.git
            git push gitlab gitlab-ci-1
            Работа по настройке pipeline, в разных вариантах, в файле.gitlab-ci.yml
            подготовка тестового проекта, работа с "окружениями" и тп
            и др.

## Как проверить работоспособность:

        Например, перейти по ссылке Gitlab http://51.250.7.249/homework/example,  ссылки Pipeline, Enviroment и др


====================
Домашнее задание №17:
====================

## В процессе сделано:

    Кратко: Отработаны все задания в соотвествии с документом к ДЗ
        Введение в мониторинг. Системы мониторинга.
        План
            Prometheus: запуск, конфигурация, знакомство с Web UI
            Мониторинг состояния микросервисов
            Cбор метрик хоста с использованием экспортера
            Задания со * (1 из 3)

    В Дз запрошено - >> ... Дать ссылку на докер хаб с вашими образами в README.md и описание PR
    Ссылка- https://hub.docker.com/repositories/alexandrpoddubnyy

## Как запустить проект:

        docker-machine ls
        eval $(docker-machine env gitlab-ci-vm)

        export USERNAME=alexandrpoddubnyy
        cd AlexandrPoddubnyy_microservices/docker
        docker-compose up -d

## Как проверить работоспособность:

        Например, перейти по ссылкам
                http://158.160.104.202:9292/ -app
                http://158.160.104.202:9090/ -prometheus
                http://158.160.104.202:9090/targets - prom-targets
                http://158.160.104.202:9115/- интерфейс blackbox-exporter и тп
