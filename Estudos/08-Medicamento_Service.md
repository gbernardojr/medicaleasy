Vamos criar o serviço para o cadastro de medicamentos, utilizando Python e Flask, assim como fizemos para o cadastro de pacientes. Este serviço permitirá o registro de medicamentos com os campos que você especificou e também usará o MongoDB como banco de dados.

### 1. **Estrutura do Projeto**

A estrutura do projeto será semelhante à do serviço de cadastro de pacientes:

```
medicamento_service/
│
├── app.py
├── requirements.txt
└── config.py
```

### 2. **Instalação de Dependências**

Se você já possui o arquivo `requirements.txt` do serviço anterior, adicione as seguintes dependências:

```plaintext
Flask
Flask-PyMongo
Flask-RESTful
python-dotenv
```

### 3. **Configuração do MongoDB**

Reutilizaremos o arquivo `config.py` que já foi criado anteriormente. Não é necessário fazer alterações.

### 4. **Código do Serviço**

Crie ou edite o arquivo `app.py` para incluir o seguinte código:

```python
from flask import Flask, request, jsonify
from flask_pymongo import PyMongo
from flask_restful import Resource, Api
from config import Config

app = Flask(__name__)
app.config.from_object(Config)
mongo = PyMongo(app)
api = Api(app)

class Medicamento(Resource):
    def post(self):
        # Captura os dados do medicamento do corpo da requisição
        data = request.json
        
        # Insere os dados do medicamento no MongoDB
        mongo.db.medicamentos.insert_one(data)
        return jsonify({"message": "Medicamento cadastrado com sucesso!"})

    def get(self):
        # Recupera todos os medicamentos cadastrados
        medicamentos = list(mongo.db.medicamentos.find())
        for medicamento in medicamentos:
            medicamento['_id'] = str(medicamento['_id'])  # Converte ObjectId para string
        return jsonify(medicamentos)

# Rotas da API
api.add_resource(Medicamento, '/medicamento')

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5001)
```

### 5. **Explicação do Código**

- **Classe `Medicamento`**:
  - O método `post` permite cadastrar um novo medicamento. Os dados são recebidos no corpo da requisição em formato JSON.
  - O método `get` retorna todos os medicamentos cadastrados.
- **Rota**:
  - `POST /medicamento`: Cadastrar um novo medicamento.
  - `GET /medicamento`: Listar todos os medicamentos.

### 6. **Exemplo de Requisição para Cadastrar Medicamento**

Para cadastrar um medicamento, você pode usar um cliente HTTP como o Postman ou `curl`. O corpo da requisição deve ser um JSON com os campos especificados. Exemplo:

```json
{
    "nome": "Paracetamol",
    "principio_ativo": "Paracetamol",
    "posologia": "1 comprimido a cada 6 horas, se necessário",
    "tipo": "Genérico",
    "efeitos_colaterais": "Náuseas, dor de estômago",
    "contra_indicacoes": "Insuficiência hepática",
    "interacoes_medicamentosas": "Pode interagir com anticoagulantes",
    "recomendacao_medica": "Não exceder 4g por dia",
    "forma_farmaceutica": "Comprimido"
}
```

### 7. **Execução do Serviço**

Para executar o serviço, você pode usar o comando:

```bash
export FLASK_APP=app.py
flask run --port=5001
```

Ou, se você quiser usar o Docker, pode criar um `Dockerfile` semelhante ao do serviço anterior.

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

Agora você tem um serviço funcional para o cadastro de medicamentos. Esse serviço é capaz de cadastrar novos medicamentos e listar todos os medicamentos existentes no banco de dados. Sinta-se à vontade para adicionar mais funcionalidades, como validações, autenticação, e tratamento de erros. Se precisar de mais ajuda ou ajustes, é só avisar!