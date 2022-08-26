Docker: Utilização prática no cenário de Microsserviços
Denilson Bonatti, Instrutor - Digital Innovation One

Muito se tem falado de containers e consequentemente do Docker no ambiente de desenvolvimento. Mas qual a real função de um container no cenários de microsserviços? Qual a real função e quais exemplos práticos podem ser aplicados no dia a dia? Essas são algumas das questões que serão abordadas de forma prática pelo Expert Instructor Denilson Bonatti nesta Live Coding. IMPORTANTE: Agora nossas Live Codings acontecerão no canal oficial da dio._ no YouTube. Então, já corre lá e ative o lembrete! Pré-requisitos: Conhecimentos básicos em Linux, Docker e AWS.

----------
Ressalvas:
- A aula de criação do container MySQL está faltando, embora esteja o nome, na realidade a aula se refere a criação do container da aplicação php, não do container MySQL.
- Como utilizei o container mysql:latest, a forma de autenticação do MySQL mudou para caching_sha2_password, ainda não suportado pelo PHP, desta forma foi necessário alterar o usuário root no mysql utilizando os comandos:
docker exec -it db bash
mysql -p
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Senha123';


Topologia utilizada:

- 2 máquinas virtuais rodadas em VirtualBox, uma com ip 192.168.0.163 e a outra com ip 192.168.0.166.
- 1 container mysql rodado na máquina virtual de ip 192.168.0.163.

Comandos:
apt-get update
apt-get install docker.io -y
docker run --name web-server -dt -p 80:80 --mount type=volume,src=app,dst=/app/ webdevops/php-apache:alpine-php7
docker run --name db -e MYSQL_ROOT_PASSWORD=Senha123 -dt -p 3306:3306 mysql:latest
docker exec -it mysql bash
docker service create --name web --replicas 10 -dt -p 80:80 --mount type=volume,src=app,dst=/app/ webdevops/php-apache:alpine-php7
docker build -t proxy-app .
docker container run --name my-proxy -dti -p 4500:4500 proxy-app

Modificações:
1) Mudança nos ips dos containers criados em máquinas virtuais na minha rede local
2) Adição da criação do banco de dados caso ele não exista no arquivo .sql

Dúvidas que permaneceram:
1) Verifiquei que ao criar os containers, eles foram criados em modo bridge. Eles criaram um IP interno para cada container verificado a partir do comando docker inspect db:
 "Networks": {
    "bridge": {
        "IPAMConfig": null,
        "Links": null,
        "Aliases": null,
        "NetworkID": "3edecd09ccb9631745ee64e12a7843556a8debea6ca40c483ca25d21e871fcc0",
        "EndpointID": "b59f49127d19c493fe7b6fef099e1326196a059f9dead6c14614746cc4e6aff1",
        "Gateway": "172.17.0.1",
        "IPAddress": "172.17.0.3",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:11:00:03",
        "DriverOpts": null
    }
}
Não compreendi como que o container em modo bridge conseguiu utilizar/bloquear a porta do mysql do servidor conforme verificado a seguir:
root@servidor:/home/eugenio# netstat -nlpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      591/systemd-resolve
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      3151/docker-proxy <------------
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      655/sshd: /usr/sbin
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      2878/docker-proxy
tcp        0      0 127.0.0.1:42791         0.0.0.0:*               LISTEN      1420/containerd
tcp6       0      0 :::3306                 :::*                    LISTEN      3156/docker-proxy
tcp6       0      0 :::22                   :::*                    LISTEN      655/sshd: /usr/sbin
tcp6       0      0 :::80                   :::*                    LISTEN      2884/docker-proxy
