



gossip protocol &rarr; protocolo utilizado pelo consul 

client  
server   
agent   


consul agent -dev &rarr; sobe um server em modo dev  
consul members &rarr; lista os membros  

## para acessar a api do consul 
curl localhost:8500/v1/catalog/nodes

servidor dns do consul roda na porta 8600

apk -U add bind-tools  // instala dig para  pesquisas no servidor de dns 

dig @localhost -p 8600 consult01.node.consul --> lista os servicos 

dig @localhost -p 8600 consult01.node.consul +short --> lista apenas os ips 


# subir um cluster consul 
docker exect -it consulserver01 sh  
ifconfig --> pega o ip   
mkdir /etc/consul.d  
mkdir /var/lib/consul  
```
consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=172.20.0.x -data-dir=/var/lib/consul -config-dir=/etc/consul.d  
```
* onde  -bootstrap-expect=3 &rarr; esta sendo esperado 3 servers  
* -node=consulserver01 &rarr; nome do node  
* -bind=172.20.0.x &rarr; ip da maquina que esta subindo o consul   


docker exect -it consulserver02 sh  
ifconfig &rarr; pega o ip  
mkdir /etc/consul.d  
mkdir /var/lib/consul  
consult agent -server -bootstrap-expect=3 -node=consulserver02 -bind=172.20.0.y -data-dir=/var/lib/consul -config-dir=/etc/consul.d   
* onde  -bootstrap-expect=3 &rarr; esta sendo esperado 3 servers  
* -node=consulserver02 &rarr; nome do node  
* -bind=172.20.0.y &rarr; ip da maquina que esta subindo o consul   
	
consul join 172.20.0.x &rarr; adiciona o 172.20.0.x no nosso cluster  
consul members &rarr; ira mostrar duas   

# usando retry-join 
**Já entra automaticamente no cluster**   
```consult agent -server -bootstrap-expect=3 -node=consulserver02 -bind=172.20.0.y -data-dir=/var/lib/consul -config-dir=/etc/consul.d -retry-join=<ip-de-outro-server> -retry-join=<ip-de-outro-server>   ```
* pode se usar quantos retry-join quiser ele vai tentar em todos até conseguir 


<br>

# Subir client 
ifconfig &rarr; pega o ip   
mkdir /etc/consul.d  
mkdir /var/lib/consul  
consult agent -bind=172.20.0.y -data-dir=/var/lib/consul -config-dir=/etc/consul.d  
consul join <ip-de-um-server>  


# registrar um serviço  
feito atraves de arquivo json  
consul reload &rarr; recarrega o arquivo json e registra o servicos

dig @localhost -p 8600 nginx.service.consul &rarr; lista service
 
consul catalog nodes -service nginx &rarr; lista os clients que estao rodando o nginx   
consul catalog nodes -detailed &rarr; lista detalhado
 

# Implementando checks 
tem de colocar no arquivo de service.json o Heath check   
https://www.consul.io/docs/discovery/checks


# Outra maneira de subir o server 
* cria um arquivo de configuracao server.json  
* consul agent -config-dir=/etc/consul.d  

# criptografia no consul 
consul keygen  --> gera a chave  
nos arquivos server.json e client.json acrescentar a tag "encrypt":"chave-recebida-pelo-consul-keygen" 

# User interface do consul 
* habilitar : 
	- consul agent -config-dir=/etc/consul.d -ui -client-addr=0.0.0.0 
	- ou colocar no arquivo server.json

# Consul em produção  
* utilizar tls 
* minimo 3 servers
* utilizar encrypt key 

 
	
### LINKS IMPORTANTES 	
https://medium.com/rapaduratech/service-discovery-com-spring-cloud-consul-691bf59fa4a8	
https://www.consul.io/
https://www.consul.io/commands

### IMPORTANTE: se você trabalha com Kubernets - o kubernetes ja tem o serviço de service discovery 
