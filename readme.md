# API Algamoney

<img src="https://i.imgur.com/aTaviZA.png" title="source: imgur.com" />

<img src="https://i.imgur.com/2GFpt6N.png" title="source: imgur.com" />

<img src="https://i.imgur.com/OmoApDs.png" title="source: imgur.com" />

<img src="https://i.imgur.com/9rTrKXK.png" title="source: imgur.com" />

<img src="https://i.imgur.com/CwztDm8.png" title="source: imgur.com" />

## Diagrama de Classes (Domínio da API)

```mermaid
classDiagram
  class CategoriaModel {
    -Long codigo
    -String nome
  }

  class LancamentoModel {
    -Long codigo
    -String descricao
    -LocalDate dataVencimento
    -LocalDate dataPagamento
    -BigDecimal valor
    -String observacao
    -TipoLancamentoEnum tipo
    -CategoriaModel categoria
    -PessoaModel pessoa
  }

  class PessoaModel {
    -Long codigo
    -String nome
    -Boolean ativo
    -EnderecoModel endereco
  }

  class EnderecoModel {
    -String logradouro
    -String numero
    -String complemento
    -String bairro
    -String cep
    -String cidade
    -String estado
  }

  class UsuarioModel {
    -Long codigo
    -String nome
    -String email
    -String senha
    -Set<PermissaoModel> permissoes
  }

  class PermissaoModel {
    -Long codigo
    -String descricao
  }

  class TipoLancamentoEnum {
    <<enumeration>>
    RECEITA
    DESPESA
  }

  LancamentoModel "N" --> "1" CategoriaModel
  LancamentoModel "N" --> "1" PessoaModel
  PessoaModel "1" *-- "1" EnderecoModel
  UsuarioModel "N" *-- "N" PermissaoModel
  LancamentoModel --> TipoLancamentoEnum
```

## Visão Geral
Backend desenvolvido para integração com o Frontend [Algamoney](https://github.com/pedro-fontinele/algamoney-frontend), um sistema de gerenciamento financeiro desenvolvido em Angular.
A API **Algamoney** é um serviço RESTful desenvolvido em **Spring Boot** para gerenciar lançamentos financeiros, categorias, pessoas e autenticação de usuários.

###
Antes de consumir a API no seu frontend, adicione o path no arquivo [CorsConfig.java no método allowedOrigins()](src/main/java/com/keidson/algamoney_api/cors/CorsConfig.java)

## Dependências
- Java 17
- Maven 3.8.1+
- PostgreSQL 13+

## Como Rodar o Projeto

1. **Clone o repositório:**
   ```sh
   git clone <https://github.com/pedro-fontinele/algamoney-api.git>
   cd algamoney-api
   ```

2. **Configure o banco de dados:**
   - Atualize as configurações do banco de dados PostgreSQL em `.env`:
     ```properties
     SPRING_DATASOURCE_URL=jdbc:postgresql://<HOST>:<PORT>/<DATABASE>
     SPRING_DATASOURCE_USERNAME=<USERNAME>
     SPRING_DATASOURCE_PASSWORD=<PASSWORD>
     ```

3. **Rodando o Projeto via Docker**

   3.1 **Certifique-se de ter o Docker instalado na sua máquina.**

   3.2 **Construa a imagem Docker:**
      ```sh
      docker build -t algamoney-api .
      ```

   3.3 **Rode o contêiner:**
      ```sh
      docker run -p 8080:8080 algamoney-api
      ```

   Isso irá iniciar o contêiner com a aplicação rodando no Java 17, sem a necessidade de ter o Java instalado na sua máquina local. A aplicação estará disponível em `http://localhost:8080`.

4. **Compile e rode o projeto localmente:**
   - Dependências:
   - Java 17
   - Maven 3.8.1+
   - PostgreSQL 13+
   ```sh
   ./mvnw clean install
   ./mvnw spring-boot:run
   ```

5. **Acesse a aplicação:**
   - Após subir o serviodor, a aplicação estará disponível em `http://localhost:8080`.


## Autenticação

A API utiliza autenticação via **JWT**. Para acessar os endpoints protegidos, é necessário incluir um token **Bearer** no cabeçalho das requisições.

**Login com User Admin:**
```json
{
  "email": "admin@algamoney.com",
  "senha": "admin"
}
```


### 1. Registrar Usuário
**Endpoint:** `POST /auth/register`

**Request Body:**
```json
{
  "nome": "teste",
  "email": "teste@gmail.com",
  "senha": "teste"
}
```

### 2. Login
**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "admin@algamoney.com",
  "senha": "admin"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

## Categorias

### 3. Criar Nova Categoria
**Endpoint:** `POST /categorias`

**Headers:**
- Authorization: Bearer {token}

**Request Body:**
```json
{
  "nome": "Alimentação"
}
```

### 4. Buscar Todas as Categorias
**Endpoint:** `GET /categorias`

**Headers:**
- Authorization: Bearer {token}

### 5. Buscar Categoria por ID
**Endpoint:** `GET /categorias/{id}`

**Headers:**
- Authorization: Bearer {token}

---

## Pessoas

### 6. Criar Nova Pessoa
**Endpoint:** `POST /pessoas`

**Headers:**
- Authorization: Bearer {token}

**Request Body:**
```json
{
  "nome": "João Silva",
  "endereco": {
    "logradouro": "Rua X",
    "numero": "123",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
  },
  "ativo": true
}
```

### 7. Buscar Todas as Pessoas
**Endpoint:** `GET /pessoas`

**Headers:**
- Authorization: Bearer {token}

### 8. Buscar Pessoa por ID
**Endpoint:** `GET /pessoas/{id}`

**Headers:**
- Authorization: Bearer {token}

### 9. Atualizar Pessoa
**Endpoint:** `PUT /pessoas/{id}`

**Headers:**
- Authorization: Bearer {token}

**Request Body:**
```json
{
  "nome": "João Silva Editado",
  "ativo": true
}
```

### 10. Excluir Pessoa
**Endpoint:** `DELETE /pessoas/{id}`

**Headers:**
- Authorization: Bearer {token}

---

## Lançamentos

### 11. Criar Novo Lançamento
**Endpoint:** `POST /lancamentos`

**Headers:**
- Authorization: Bearer {token}

**Request Body:**
```json
{
  "pessoa": { "codigo": 7 },
  "categoria": { "codigo": 5 },
  "descricao": "Salário",
  "dataVencimento": "2024-07-23",
  "valor": 3000.00,
  "tipo": "RECEITA"
}
```

### 12. Buscar Todos os Lançamentos
**Endpoint:** `GET /lancamentos`

**Headers:**
- Authorization: Bearer {token}

### 13. Buscar Lançamento por ID
**Endpoint:** `GET /lancamentos/{id}`

**Headers:**
- Authorization: Bearer {token}

### 14. Excluir Lançamento
**Endpoint:** `DELETE /lancamentos/{id}`

**Headers:**
- Authorization: Bearer {token}

---

## Filtros e Paginação

### 15. Buscar Lançamentos Paginados
**Endpoint:** `GET /lancamentos/paginados?page=1&size=5`

**Headers:**
- Authorization: Bearer {token}

### 16. Filtrar Lançamento por Descrição
**Endpoint:** `GET /lancamentos/filtrar?descricao=Aluguel`

**Headers:**
- Authorization: Bearer {token}

### 17. Filtrar Lançamento por Data de Vencimento
**Endpoint:** `GET /lancamentos/filtrar?dataVencimentoDe=2024-09-10&dataVencimentoAte=2024-09-15`

**Headers:**
- Authorization: Bearer {token}
