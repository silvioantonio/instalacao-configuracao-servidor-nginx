---
description: >-
  Neste artigo sera descrito de forma simples e descomplicada como instalar e
  configurar o servidor NGinx no sistema operacional Linux, mais especificamente
  na distribuição Ubuntu 18.04 LTS.
---

# Instalação e Configuração do servidor NGinx \(Linux\)

## Introdução

O Nginx é um dos servidores web mais populares no mundo e é responsável por hospedar alguns dos maiores e mais acessados sites na internet. Ele possui mais facilidades de recursos do que o Apache em muitos casos e pode ser utilizado como servidor web ou proxy reverso.

## Pré-requisitos

A principio todos os comando utilizados nesse guia sao feitos no terminal linux como super usuario\(usuario root\), porem, voce pode utilizar um usuario regular nao-root com privilegios `SUDO`configurado em sua maquina para que voçe possa utilizar os comandos no sistema.


 Para utilizar os privilegios de super usuario, voçe deve acessar o terminal e para isso utilize as teclas de atalho `CTRL + ALT + T` e em seguida digite `$SUDO SU` o comando ira pedir a senha do usuario root\(super usuario\) .


Atenção!!!

Enquanto estiver como usuario root, todo comando ou ação que for executado sera valido para o sistema sem restrições, por isso cuidado.

## Instalação

O NGinx esta disponivel na lista de repositorios do Ubuntu, entao iremos utilizar o gerenciador de pacotes **APT** para baixar e instalar o servidor NGinx.

Primeiro iremos atualizar nosso indice local de pacotes dentro do sistema utilizando o comando `apt-get` , e dessa forma essa e outras dependencias que forem necessarias serao baixadas e instaladas.

Em seguida usamos o comando `install` passando o nome so servidor **nginx** para que o download seja feito e o servidor instalado.

```text
apt-get update
apt-get install nginx
```

Vamos habilitar a instalação do servidor

```text
systemctl enable nginx
```

## Checar Instalação do Servidor

Apos baixar e instalar o servidor em sua maquina, é hora de verificar se ele esta ativo.

Podemos checar utilizando o sistema de init `systemd` para ter certeza que o servidor esta operante.

```text
sytemctl status nginx
```

Com o comando `systemctl status <server name>` podemos verificar quais servidores estao ativos ou nao em sua maquina.


```bash
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2019-08-31 22:14:00 EDT; 4min 2s ago
 Main PID: 12857 (nginx)
   CGroup: /system.slice/nginx.service
           ├─12857 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─12858 nginx: worker process

```

Como voce pode ver, o servidor esta funcionando corretamente, mas a melhor forma para se testar isso é acessando a pagina default do servidor em sua maquina, e dessa forma confirmar que o servidor esta operante.

Acesse o endereço:  [_**http://IPDOSERVIDORNGINX**_](http://ipdoservidornginx./)

Uma pagina de "Welcome to nginx" deve ser mostrada.

## Configurando o virtual host no NGinx

Primeiramente, vamos criar o arquivo de configuração do nosso virtual host. Lembrando que você pode alterar o nome do arquivo para o que você quiser:

```text
gedit /etc/nginx/sites-available/virtualhost1
```

Editor de texto e Nome do arquivo

#### Editor

Nexte exemplo estou utilizando o gedit, mas voce pode utilizar editores do sistema se preferir, o resultado sera o mesmo.

#### Nome do arquivo

Neste exemplo coloquei o nome do arquivo como "virtualhost1", porem voce pode colocar qualquer nome que quiser, apenas lembre-se desse nome, voce ira precisar em breve nos proximos passos da configuraçao.

Preencha com:

```text
server {

       listen 80;

       server_name www.virtualhost1.com.br;

       root /var/www/virtualhost1;

       index index.html index.htm index.php;

}
```

Onde:

**listen** é a porta que ele estará escutando para receber uma conexão, neste caso porta 80.

**server\_name** é o endereço que digitaremos para acessá-lo.

**root** é o diretório onde está localizado os arquivos deste _virtual_ host.

A última linha define como poderão ser chamados os arquivos padrões.

Dapois _salve_ e _saia_ do editor de texto que estiver utilizando.

## Diretorio e arquivos

Agora, vamos criar o diretório de arquivos deste _virtual_ host. Lembrando que, o endereço deve ser o mesmo que colocamos na diretiva **root** do passo anterior.

```text
mkdir /var/www/virtualhost1
gedit /var/www/virtualhost1/index.html
```

mkdir

Este é um comando do terminal linux para criação de diretorios/pastas.

no exemplo acima estou criando via terminal uma pasta dentro de /var/www chamada de virtualhost1

Coloque qualquer conteudo dentro desse arquivo \(index.html\). Este aquivo é a pagina default do seu servido recem criado. É altamente recomendado que paginas html possuam no minimo um cabeçalho e um corpo.

```text
<!DOCTYPE html>
<html>
    <head>
        <title>Seu titulo aqui!</title>
    </head>
    <body>
        <h1>Minha Pagina utilizando NGinx</h1>
    </body>
</html>
```

## Configuração de ip para o servidor

Em seguida, devemos criar uma resolução de nomes locais para o endereço que preenchemos na diretiva **server\_name**:

```text
gedit /etc/hosts
```

Então, adicione uma nova linha e coloque seu IP de loopback que é 127.0.0.1. Ao lado, adicione o endereço correspondente ao **server\_name**. Ou seja, sempre que digitarmos [_www.virtualhost1.com.br_](http://www.vhost1.com.br/) ele será direcionado para o IP _127.0.0.1_ que é o mesmo de nossa maquina. Então, o NGINX irá direcioná-lo para o diretório em questão, porem, se voce quiser utilizar o seu ip de rede, tambem é possivel.

### Verificar ip de rede

Dentro do terminal digite:

```text
ifconfig
```

```text
enp8s0: flags=4095<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 08:9e:01:2a:9f:2c  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Loopback Local)
        RX packets 4173  bytes 447027 (447.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4173  bytes 447027 (447.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp7s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.29.88  netmask 255.255.255.0  broadcast 172.16.29.255
        inet6 fc80::c8eb:7ba89:3af2:f768  prefixlen 64  scopeid 0x20<link>
        ether 44:6d:57:ac:45:c5  txqueuelen 1000  (Ethernet)
        RX packets 915188  bytes 1111727528 (1.1 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 531725  bytes 500622855 (500.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

Esse comando serve para verificarmos qual ip temos em uma arquitetura de rede local de internet\(dentre outras coisas\).

Na linha 9 do retorno do comando ifconfig é possivel ver o endereço de loop back `inet 127.0.0.1` e na linha 18 o ip de rede `inet 192.168.0.15`

```text

127.0.0.1	localhost
127.0.1.1	silvio

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

172.16.29.88 havaianademadeira.com.br
172.16.29.88 www.havaianademadeira.com.br
172.16.29.88 jacaresemdente.com.br
172.16.29.88 www.jacaresemdente.com.br
172.16.29.88 silvioweb.com.br
172.16.29.88 www.silvioweb.com.br
172.16.29.88 www.silvio.ifto.com.br
```

## `Habilitar o host`

Por fim, vamos para o último passo que é habilitar o virtual host, por meio da criação de um link.

```text
ln -sf /etc/nginx/sites-available/virtualhost1 /etc/nginx/sites-enabled/
```

Em seguida reinicie o NGinx com o comando: 

```text
systemctl restart nginx
```

Agora, você pode testar localmente, basta abrir o navegador de sua preferencia e colocar na barra de URL o seu endereço de servidor local, em nosso caso 

> www.virtualhost1.com.br

## Alguns comandos NGinx

Para parar seu servidor web, você pode digitar:

```text
systemctl stop nginx
```

Para iniciar o servidor web quando ele estiver parado, digite:

```text
systemctl start nginx
```

Para parar e depois iniciar o serviço novamente, digite:

```text
systemctl restart nginx
```

Se você estiver simplesmente realizando alterações de configuração, o Nginx muitas vezes recarrega sem perder as conexões. Para fazer isso, esse comando pode ser utilizado:

```text
systemctl reload nginx
```

Por padrão, o Nginx é configurado para iniciar automaticamente quando o servidor é inicializado. Se isso não é o que você quer, você pode desabilitar esse comportamento digitando:

```text
systemctl disable nginx
```

Para reativar o serviço para iniciar na inicialização do servidor, você pode digitar:

```text
systemctl enable nginx
```

Lembrete

Em todos os comandos mostrados ate o momento é entendido que voce esteja como super usuario, caso contrario, eles nao irão funcionar corretamente.

Se voce nao estiver utilizando o perfil de super usuario, coloque o comando `sudo` no inicio de todos os codigos mostrados no artigo e tudo ira funcionar coretamente.

## Link oficial do servidor

Segue o link para o site oficial do servidor com novas features e documentação do produto. [https://www.nginx.com/](https://www.nginx.com/)

> Compartilhar conhecimento é a melhor forma de aprender
>
> -OLIVEIRA, Silvio Antonio Junior.



