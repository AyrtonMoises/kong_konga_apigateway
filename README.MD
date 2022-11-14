# Kong API Gateway + Konga

#### Cria network para arquivos docker-compose do projeto se comunicarem
```
docker network create kong-net
```
##### Rodar containers do Kong na pasta docker-kong
```
docker-compose up -d
```
#### Rodar containers da pasta raiz
```
docker-compose up -d
```
#### Criar usuário dentro do Kong
[http://localhost:1337/register]

#### Registrar dados da API do Kong no Konga
Kong
http://kong:8001

#### Criar services para cada serviço como exemplo abaixo:
name: servicea
protocol: http
host: servicea
port:8081

#### Após criar service, editar e registrar as routes por exemplo:
name: a
path: /a (dar enter)

#### Acessar rota, pode ter um delay após aplicar
http://localhost:8000/a

#### Criando upstream, no menu Upstream para loadbalancer
name:servicec_upstream

##### Criar targets do upstream, editando um upstream criado
target:servicec1:8083
weight: 100 (peso de distribuição, pode ser modificado para maquinas mais fortes/fracas e
até para deploy onde apenas x usuários usaram a versão nova etc)
target:servicec2:7083
weight: 100

#### Criar service para o upstream
name:servicec
protocol:http
host:servicec_upstream
port:8000

#### Criar a route para o upstream também
- Editar service criado e colocar a route
name: c
path: /c (dar enter)

#### Health check para acompanhar saúde dos services
 - No menu Upstreams > service desejado > Active health check 
Verify https certificate = off (desativar em desenvolvimento)
Unhealthy timeouts = 1 (numero de timeouts)
Unhealthy HTTP failures = 1 (algum erro de http listado no campo http statuses)
unhealthy interval = 5 (se for 0 ele fica desativado)
healthy interval = 5 (se for 0 ele fica desativado)
healthy success = 1 (tentativas pra considerar o serviço saudavel novamente)

- Voltando a grid de targets do upstream irão aparecer icones verdes de healthy
para monitoramento

#### Derrube um serviço para testar configuração
```
docker-compose stop name_service 
```
- Na grid de targets o item de healthy deve ficar vermelho

### Autenticação de service por exemplo em JWT 
- Service > serviço desejado > plugins > authentication > JWT
maximum_expiration: tempo de expiração (definir em producao)
header names: Authorization (dar enter)
- Service deverá mostrar mensagem de não autorizado ao acessar
Para dar acesso basta ir em Consumers > e criar um user e depois credentials > JWT
key: nome_key
secret: chave_secreta
- Para testar pode acessar https://jwt.io/
em payload colocar: { "iss": "nome_key" } 
em verify signature colocar a secret e copiar o hash encoded
- Agora pode ser testado no Postman por exemplo onde em authorization do tipo
'bearer token' basta colar o token e testar url protegida

##### E possível exportar e importar configurações do Konga já realizadas no menu Snapshot