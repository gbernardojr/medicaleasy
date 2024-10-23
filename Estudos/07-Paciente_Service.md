Vamos criar o código do primeiro serviço, que gerencia o cadastro do paciente, utilizando Python e Flask. Este serviço irá permitir o cadastro de pacientes com os campos que você especificou. Para isso, utilizaremos o MongoDB como banco de dados para armazenar as informações dos pacientes.

### 1. **Estrutura do Projeto**

A estrutura do projeto será a seguinte:

```
paciente_service/
│
├── app.py
├── requirements.txt
└── config.py
```

### 2. **Instalação de Dependências**

Primeiro, crie um arquivo `requirements.txt` com as dependências necessárias:

```plaintext
Flask
Flask-PyMongo
Flask-RESTful
python-dotenv
```

### 3. **Configuração do MongoDB**

Crie um arquivo `config.py` para armazenar a configuração do MongoDB:

```python
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    MONGO_URI = os.getenv("MONGO_URI", "mongodb://localhost:27017/paciente_db")
```

### 4. **Código do Serviço**

Agora, crie o arquivo `app.py` com o seguinte código:

```python
from flask import Flask, request, jsonify
from flask_pymongo import PyMongo
from flask_restful import Resource, Api
from datetime import datetime
from config import Config

app = Flask(__name__)
app.config.from_object(Config)
mongo = PyMongo(app)
api = Api(app)

class Paciente(Resource):
    def post(self):
        # Captura os dados do paciente do corpo da requisição
        data = request.json
        data['data_nascimento'] = datetime.strptime(data['data_nascimento'], "%Y-%m-%d")
        data['idade'] = self.calcular_idade(data['data_nascimento'])
        
        # Insere os dados do paciente no MongoDB
        mongo.db.pacientes.insert_one(data)
        return jsonify({"message": "Paciente cadastrado com sucesso!"})

    def calcular_idade(self, data_nascimento):
        hoje = datetime.today()
        idade = hoje.year - data_nascimento.year - ((hoje.month, hoje.day) < (data_nascimento.month, data_nascimento.day))
        return idade

class Pacientes(Resource):
    def get(self):
        # Recupera todos os pacientes cadastrados
        pacientes = list(mongo.db.pacientes.find())
        for paciente in pacientes:
            paciente['_id'] = str(paciente['_id'])  # Converte ObjectId para string
        return jsonify(pacientes)

# Rotas da API
api.add_resource(Paciente, '/paciente')
api.add_resource(Pacientes, '/pacientes')

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

### 5. **Explicação do Código**

- **Configuração do Flask e MongoDB**: O Flask é configurado para se conectar ao MongoDB usando Flask-PyMongo.
- **Classe `Paciente`**:
  - O método `post` permite cadastrar um novo paciente. Os dados são recebidos no corpo da requisição em formato JSON.
  - O método `calcular_idade` calcula a idade do paciente com base na data de nascimento.
- **Classe `Pacientes`**:
  - O método `get` retorna todos os pacientes cadastrados.
- **Rotas**:
  - `POST /paciente`: Cadastrar um novo paciente.
  - `GET /pacientes`: Listar todos os pacientes.

### 6. **Exemplo de Requisição para Cadastrar Paciente**

Para cadastrar um paciente, você pode usar um cliente HTTP como o Postman ou `curl`. O corpo da requisição deve ser um JSON com os campos especificados. Exemplo:

```json
{
    "nome": "João Silva",
    "data_nascimento": "1990-05-15",
    "genero": "Masculino",
    "cor": "Parda",
    "nacionalidade": "Brasileiro",
    "naturalidade": "São Paulo",
    "estado_civil": "Solteiro",
    "nome_mae": "Maria Silva",
    "nome_pai": "José Silva",
    "nome_conjuge": "N/A",
    "profissao": "Engenheiro",
    "cpf": "123.456.789-09",
    "cartao_sus": "9876543210",
    "endereco": "Rua das Flores, 123",
    "bairro": "Jardim das Rosas",
    "cidade": "São Paulo",
    "estado": "SP",
    "pais": "Brasil",
    "celular": "(11) 91234-5678",
    "email": "joao.silva@example.com",
    "plano_saude": "Unimed"
}
```

### 7. **Execução do Serviço**

Para executar o serviço, você pode usar o comando:

```bash
export FLASK_APP=app.py
flask run
```

Ou, se você quiser usar o Docker, pode criar um `Dockerfile` e construir a imagem.

### 8. **Dockerfile (opcional)**

```Dockerfile
# Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python", "app.py"]
```

### Conclusão

Com esse código, você terá um serviço básico de cadastro de pacientes. Sinta-se à vontade para adicionar mais funcionalidades, como validações, autenticação, e tratamento de erros. Se precisar de mais ajuda ou ajustes, é só avisar!