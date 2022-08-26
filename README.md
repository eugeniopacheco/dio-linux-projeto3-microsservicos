Docker: Utilização prática no cenário de Microsserviços
Denilson Bonatti, Instrutor - Digital Innovation One

Muito se tem falado de containers e consequentemente do Docker no ambiente de desenvolvimento. Mas qual a real função de um container no cenários de microsserviços? Qual a real função e quais exemplos práticos podem ser aplicados no dia a dia? Essas são algumas das questões que serão abordadas de forma prática pelo Expert Instructor Denilson Bonatti nesta Live Coding. IMPORTANTE: Agora nossas Live Codings acontecerão no canal oficial da dio._ no YouTube. Então, já corre lá e ative o lembrete! Pré-requisitos: Conhecimentos básicos em Linux, Docker e AWS.

----------

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

Ressalvas:
- A aula de criação do container MySQL está faltando, embora esteja o nome, na realidade a aula se refere a criação do container da aplicação php, não do container MySQL.
- Como utilizei o container mysql:latest, a forma de autenticação do MySQL mudou para caching_sha2_password, ainda não suportado pelo PHP, desta forma foi necessário alterar o usuário root no mysql utilizando os comandos:
docker exec -it db bash
mysql -p
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Senha123';