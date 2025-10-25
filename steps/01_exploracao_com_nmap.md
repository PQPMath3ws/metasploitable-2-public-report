# 01. Exploração com NMAP

Bem, para a gente conhecer melhor o cenário do nosso <i>sistema operacional alvo</i>, que se encontra instalado e configurado na máquina virtual, em uma rede local isolada (que a máquina <b>"maliciosa"</b> irá realizar os seus testes e tentar comprometer), precisamos tentar conhecer melhor o nosso alvo, procurando analisar: <b>portas abertas/fechadas/filtradas, serviços rodando nessas portas, falhas reportadas publicamente de algum desses serviços, e a exploração desses serviços</b>, a fim de tentar alcançar o maior nível de usuário possível, e/ou comprometer o máximo possível da nossa máquina alvo.

Vamos começar então - usaremos para a etapa de <i><b>exploração</b></i> uma ferramenta chamada <i><b>NMAP</i></b>.

O <i><b>NMAP</i></b> (abreviação de <i><b>Network Mapper</i></b>) é uma ferramenta <i><b>open-source</i></b> de uso em <i><b>terminal/console</i></b> criada para realizar a varredura e detecção de endereços IP (máquinas/componentes ativos na rede), e através de cada IP, encontrar portas e serviços ativos rodando nas mesmas. É um programa extremamente necessário e importante para o administrador de rede poder realizar o gerenciamento de toda a rede como um todo e possivelmente previnir o ambiente de falhas de segurança médias/graves/severas.

Enfim - explicação feita, vamos começar!

Realizo uma busca simples de serviços que estejam rodando nessa minha máquina configurada com o seguinte comando:

`$ nmap -p- -sV 192.168.56.102`

onde,
- `-p-` - faz a busca por TODAS as portas existentes em um sistema operacional. (<b>1-65535</b>)
- `-sV` - tenta realizar a detecção do serviço que está sendo executado na porta encontrada.
- `192.168.56.102` - endereço da máquina alvo local.

Após um pequeno tempo, me é retornado o seguinte, dessa máquina:

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-13 16:53 -03
Verbosity Increased to 1.
Stats: 0:01:40 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 96.67% done; ETC: 16:55 (0:00:03 remaining)
Completed Service scan at 16:56, 126.15s elapsed (30 services on 1 host)
NSE: Script scanning 192.168.56.102.
Initiating NSE at 16:56
Completed NSE at 16:56, 0.17s elapsed
Initiating NSE at 16:56
Completed NSE at 16:56, 0.05s elapsed
Nmap scan report for 192.168.56.102
Host is up (0.00011s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
53/tcp    open  domain      ISC BIND 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp   open  exec        netkit-rsh rexecd
513/tcp   open  login?
514/tcp   open  shell       Netkit rshd
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp  open  vnc         VNC (protocol 3.3)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
6697/tcp  open  irc         UnrealIRCd
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
38718/tcp open  nlockmgr    1-4 (RPC #100021)
38811/tcp open  java-rmi    GNU Classpath grmiregistry
38909/tcp open  mountd      1-3 (RPC #100005)
55674/tcp open  status      1 (RPC #100024)
MAC Address: 08:00:27:0E:4E:EA (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 128.04 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.622MB)
```

Interessante, legal - nos foi retornado bastante informação útil, que nós poderemos tentar explorar a fim de conseguir comprometer nossa máquina alvo.

Agora, vamos com tudo pra nossa próxima etapa!