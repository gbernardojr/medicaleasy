Para integrar os dois serviços de cadastro de pacientes e medicamentos por meio de uma API Gateway, você pode usar o **Kong** ou **API Gateway** do **AWS**. O objetivo da API Gateway é agir como um ponto único de entrada para os serviços, facilitando o gerenciamento, a autenticação e o roteamento das requisições.

### Estrutura da API Gateway

Vamos supor que você esteja utilizando o Kong como API Gateway. A estrutura geral do seu projeto com o Kong ficaria assim:

```
micro_saas/
│
├── api_gateway/
│   └── kong.conf
│
├── paciente_service/
│   ├── app.py
│   ├── requirements.txt
│   └── config.py
│
└── medicamento_service/
    ├── app.py
    ├── requirements.txt
    └── config.py
```

### 1. **Instalação do Kong**

Para começar, você pode instalar o Kong localmente utilizando o Docker. Aqui está um exemplo de como fazer isso:

```bash
docker network create kong-net

# Iniciando um banco de dados para o Kong
docker run -d --name kong-database \
  --network=kong-net \
  -e "KONG_DATABASE=off" \
  -e "KONG_PG_HOST=localhost" \
  postgres:9.6

# Inicializando o Kong
docker run -d --name kong \
  --network=kong-net \
  -e "KONG_DATABASE=off" \
  -e "KONG_PROXY_LISTEN=0.0.0.0:8000" \
  -e "KONG_ADMIN_LISTEN=0.0.0.0:8001" \
  kong

# Esperar o Kong inicializar
sleep 5

# Criar um super usuário para o Kong
docker exec -it kong kong migrations bootstrap
```

### 2. **Configuração da API Gateway**

Após a instalação do Kong, você pode configurá-lo para rotear requisições para os serviços de pacientes e medicamentos.

#### 2.1. **Adicionar Serviços no Kong**

Você pode adicionar os serviços de pacientes e medicamentos ao Kong usando `curl` ou um cliente HTTP:

```bash
# Adicionar serviço de pacientes
curl -i -X POST http://localhost:8001/services \
  --data "name=paciente-service" \
  --data "url=http://paciente_service:5000/paciente"

# Adicionar rota para pacientes
curl -i -X POST http://localhost:8001/services/paciente-service/routes \
  --data "paths[]=/paciente"

# Adicionar serviço de medicamentos
curl -i -X POST http://localhost:8001/services \
  --data "name=medicamento-service" \
  --data "url=http://medicamento_service:5001/medicamento"

# Adicionar rota para medicamentos
curl -i -X POST http://localhost:8001/services/medicamento-service/routes \
  --data "paths[]=/medicamento"
```

### 3. **Usando a API Gateway**

Agora que você configurou o Kong, pode acessar seus serviços por meio da API Gateway:

- **Cadastro de Paciente**:  
  `POST http://localhost:8000/paciente`
  
- **Listar Pacientes**:  
  `GET http://localhost:8000/pacientes`
  
- **Cadastro de Medicamento**:  
  `POST http://localhost:8000/medicamento`
  
- **Listar Medicamentos**:  
  `GET http://localhost:8000/medicamentos`

### 4. **Docker Compose (opcional)**

Se você quiser simplificar a execução de todos os serviços com Docker, pode usar um arquivo `docker-compose.yml`:

```yaml
version: '3.8'

services:
  kong-database:
    image: postgres:9.6
    networks:
      - kong-net
    environment:
      POSTGRES_USER: kong
      POSTGRES_DB: kong
      POSTGRES_PASSWORD: kong

  kong:
    image: kong
    networks:
      - kong-net
    environment:
      KONG_DATABASE: postgres
      KONG_PG_HOST: kong-database
      KONG_PROXY_LISTEN: 0.0.0.0:8000
      KONG_ADMIN_LISTEN: 0.0.0.0:8001
    depends_on:
      - kong-database

  paciente_service:
    build: ./paciente_service
    networks:
      - kong-net

  medicamento_service:
    build: ./medicamento_service
    networks:
      - kong-net

networks:
  kong-net:
```

### Conclusão

Com essa configuração, você terá uma API Gateway (Kong) que roteia requisições para os serviços de cadastro de pacientes e medicamentos. O Kong pode ser expandido para incluir autenticação, rate limiting e outras funcionalidades, tornando-se uma solução robusta para gerenciar suas APIs. Se precisar de mais detalhes ou ajustes, estou à disposição para ajudar!