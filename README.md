# Introdução
Vamos criar um servidor de DNS com docker para entender o funcionamento de um servidor de DNS recursivo.

# Roteiro
- Em uma máquina Linux com docker instalado e com acesso à Internet, deve-se clonar (ou copiar) o repositório [https://github.com/sidneypio/dns-recursivo]. Se o git estiver instalado, pode-ser usar o seguinte comando:
- git clone 
criar o arquivo named.conf-recursivo com o seguinte conteúdo:
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


Executar o comando (tudo em uma única linha):
docker run -tid --name dns -v ./named.conf-recursivo:/etc/bind/named.conf -v ./home:/home sidneypio/bind-inf534 /usr/sbin/named -f -c /etc/bind/named.conf 

Acessar o docker através do comando:
docker exec -it dns bash
e depois fazer os testes de consulta ao DNS, por exemplo:
host -vvv -4 -t A www.unicamp.br 127.0.0.1
host -vvv -4 -t A www.iqm.unicamp.br 127.0.0.1
host -vvv -4 -t A www.naoexiste.br 127.0.0.1
Pode-se abrir outra janela com tcpdump para fazer a captura/visualização do tráfego.

