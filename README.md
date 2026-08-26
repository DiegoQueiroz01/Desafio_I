 # Desafio I - CloudOps API

 API simples desenvolvida em Python com FastAPI, criada para demonstrar uma esteira de CI/CD com testes automatizados, análise SAST e publicação de imagem Docker.

 ## Tecnologias

 - Python 3.11
 - FastAPI
 - Uvicorn
 - Pytest
 - Semgrep
 - Docker
 - GitHub Actions

 ## Estrutura do projeto

 ```text
 .github/workflows/
	 ci.yml              # Testes e análise SAST em Pull Requests
	 cd.yml              # Build e publicação da imagem em main
 app/
	 main.py             # Aplicação FastAPI e endpoints
 tests/
	 test_main.py        # Testes automatizados
 Dockerfile            # Imagem da aplicação
 requirements.txt      # Dependências Python
 ```

 ## Endpoints

 | Método | Rota | Descrição |
 | --- | --- | --- |
 | `GET` | `/` | Retorna a mensagem, o status e a versão da API. |
 | `GET` | `/health` | Verifica a saúde da aplicação. |

 Ao executar a aplicação localmente, a documentação interativa também fica disponível em:

 - Swagger UI: http://localhost:8000/docs
 - ReDoc: http://localhost:8000/redoc

 ## Execução local

 ### 1. Criar e ativar um ambiente virtual

 Windows PowerShell:

 ```powershell
 python -m venv .venv
 .\.venv\Scripts\Activate.ps1
 ```

 Linux/macOS:

 ```bash
 python3 -m venv .venv
 source .venv/bin/activate
 ```

 ### 2. Instalar as dependências

 ```bash
 python -m pip install --upgrade pip
 pip install -r requirements.txt
 ```

 ### 3. Iniciar a API

 ```bash
 uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
 ```

 A API estará disponível em http://localhost:8000.

 ## Testes

 Execute os testes unitários com:

 ```bash
 python -m pytest tests/ -v
 ```

 ## Execução com Docker

 Construir a imagem:

 ```bash
 docker build -t desafio_i .
 ```

 Executar o container:

 ```bash
 docker run --rm -p 8000:8000 desafio_i
 ```

 Depois, acesse http://localhost:8000/docs.

 ## Pipeline CI/CD

 O projeto utiliza dois workflows do GitHub Actions:

 1. **CI**, acionado em Pull Requests direcionados para `main` ou `develop`:
		- instala as dependências;
		- executa os testes com pytest;
		- executa análise de segurança SAST com Semgrep.
 2. **CD**, acionado em pushes para `main`:
		- autentica no Docker Hub usando `DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN` configurados como secrets;
		- constrói a imagem Docker;
		- publica as tags `latest` e o SHA do commit.

 ```mermaid
 flowchart LR
	 F[feature/*] -->|Pull Request| D[develop]
	 D -->|Pull Request| M[main]
	 F -.-> CI[CI: pytest + Semgrep]
	 D -.-> CI
	 M --> CD[CD: Docker build + push]
	 CD --> DH[Docker Hub: latest + SHA]
 ```

 ## Fluxo de branches

 O fluxo utilizado é baseado em GitFlow:

 - `main`: versão principal e publicação da imagem;
 - `develop`: integração das funcionalidades;
 - `feature/*`: desenvolvimento de novas funcionalidades.

 As credenciais do Docker Hub não devem ser armazenadas no código. Configure-as em **Settings > Secrets and variables > Actions** do repositório:

 - `DOCKERHUB_USERNAME`
 - `DOCKERHUB_TOKEN`
