# Introdução
Vamos criar um servidor de DNS com docker para entender o funcionamento de um servidor de DNS recursivo.

# Roteiro
- Em uma máquina Linux com docker instalado e com acesso à Internet, deve-se clonar (ou copiar) o repositório. Se o git estiver instalado, pode-ser usar o seguinte comando:
	- git clone https://github.com/sidneypio/dns-recursivo.git
- Será copiada a estrutura de diretórios e arquivos básicos de configuração.
- Para executar o container, ao invés de usar o `docker compose`,  podemos usar o comando `docker run` diretamente:
> [!WARNING]
> O comando é apenas uma linha ! Cuidado com o *copy-and-paste* !

	docker run -tid --name dns-recursivo -v ./named.conf-recursivo:/etc/bind/named.conf -v ./home:/home sidneypio/bind-inf534 /usr/sbin/named -f -c /etc/bind/named.conf
   
- Podemos acessar o container através do comando:
	- docker exec -it dns-recursivo /bin/bash
- no ambiente do container, podemos fazer testes de consulta ao DNS, por exemplo:
	- host -vvv -4 -t A www.unicamp.br 127.0.0.1
	- host -vvv -4 -t A www.iqm.unicamp.br 127.0.0.1
	- host -vvv -4 -t A www.naoexiste.br 127.0.0.1
- Pode-se abrir outra janela com tcpdump para fazer a captura/visualização do tráfego.

# Detalhes dos arquivos e comandos:
## Arquivo named.conf-recursivo:
```
options {
	directory "/var/bind";

	allow-recursion {
		127.0.0.1/32;
	};

	// Configure the IPs to listen on here.
	listen-on { 127.0.0.1; };
	listen-on-v6 { none; };

	pid-file "/var/run/named/named.pid";

	allow-transfer { none; };
};

zone "." IN {
	type hint;
	file "named.ca";
};
```

## Explicação dos parâmetros usados no comando docker acima
* run : cria e executa um container a partir de uma imagem Docker; 
* -tid : executa um novo container de forma interativa e em segundo plano;
* --name : nome do container;
* -v ./named.conf-recursivo:/etc/bind/named.conf : Monta o volume local para dentro do container;
* -v ./home:/home :  Monta o volume local para dentro do container;
* sidneypio/bind-inf534 : nome da imagem;
* /usr/sbin/named -f -c /etc/bind/named.conf : comando utilizado na inicialização.

## Possível erro 
- ao tentar executar o comando 'docker run' pode-se obter o seguinte erro:
```
docker: Error response from daemon: Conflict. The container name "/dns-recursivo" is already in use by container "b65ffc564b27a2bc80a805a161803b955a9f8599b6b94d7e38c802f7035c61cc". You have to remove (or rename) that container to be able to reuse that name.
``` 
- isso pode ocorrer se já existir um container com o nome "dns-recursivo" associado.
  Para verificar, podemos usar o comando `docker ps -a`.
  Pode-se usar a seguinte sequência de comandos para parar e remover o container dns-recursivo:
  ```
  docker stop dns-recursivo
  docker rm dns-recursivo
  ```
