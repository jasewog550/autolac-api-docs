---
title: Documentação da API Autolac para Parceiros

language_tabs: # Define as abas de linguagem no painel direito
  - json: Resposta

toc_footers:
  - <a href='#'>Voltar para o Topo</a>
  - <a href='https://github.com/slatedocs/slate'>Documentação criada com Slate</a>

# Adicione todos os seus blocos de código que estarão no painel direito aqui
# Isso ajuda o Slate a saber quais linguagens destacar
includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Descreve as regras para integração entre sistemas parceiros e o Autolac
---

[TOC]
<div style="page-break-after: always"></div>


## Controle de versão da documentação

| Versão | Data de liberação | Autor            | Observação                                                   |
| :----- | :---------------- | :--------------- | :----------------------------------------------------------- |
| 1.0.0  |  20/05/2025     | Lucas Cardoso | Construção. |
| 1.0.1 | 01/07/2025 | Lucas Cardoso | Adicionado informação sobre as credenciais de acesso a API. |

<div style="page-break-after: always"></div>

## Result

- Objeto padrão utilizado para encapsular as respostas da API Autolac. Indica o status da operação e contém os dados de retorno ou mensagens de erro.
### Exemplo (Sucesso)
```json
{
  "statusCode": 200,
  "success": true,
  "message": "Mensagem descritiva de sucesso.",
  "data": {
    // O conteúdo aqui varia dependendo do endpoint e do resultado da operação.
  }
}
```

### Exemplo (Erro)
```json
{
  "statusCode": 400,
  "success": false,
  "message": "Mensagem descritiva do erro.",
  "data": null
}
```

### Propriedades
| Nome       | Tipo    |  Obrigatório | Descrição                                                                                                                               |
| :--------- | :------ | :---------- | :-------------------------------------------------------------------------------------------------------------------------------------- |
| statusCode | integer |  Sim         | Código de status HTTP que reflete o resultado geral da operação.                                                                        |
| success    | boolean | Sim         | Indica se a operação foi bem-sucedida (true) ou não (false).                                                                          |
| message    | string  |  Não         | Mensagem descritiva sobre o resultado da operação, fornecendo detalhes em caso de sucesso ou erro.                                       |
| data       | object  | Não         | Contém os dados de retorno da operação em caso de sucesso. A estrutura deste objeto varia conforme o endpoint (ver no retorno de cada endpoint). |

<div style="page-break-after: always"></div>
## Endpoints

### Autenticação (Login)

- Autentica o laboratório apoiado na API, retornando um token de acesso JWT para ser usado em chamadas subsequentes.
- As credenciais de acesso da API devem ser solicitadas ao nosso setor comercial.
- Efetuar login:
  - Path: /Api/Inter-Autolac/Login
  - Método: POST
  - Content-Type: application/json
  - Autenticação: Nenhuma (este endpoint é usado para obter o token).

#### Request

##### Visão geral
  - Objeto de dados utilizado no corpo da requisição do endpoint de Login (POST /Api/Inter-Autolac/Login) para autenticar o laboratório apoiado.

##### Exemplo
  ```json
  {
    "apoiadoId": 123,
    "senha": "senha"
  }
  ```

##### Propriedades
| Nome        | Tipo    | Obrigatório | Descrição                                                                |
| :---------- | :------ | :---------- | :----------------------------------------------------------------------- |
| apoiadoId | integer | Sim         | Número de identificação único do laboratório apoiado no sistema Autolac (apoio). |
| senha     | string  | Sim         | Senha de autenticação do laboratório apoiado (Deve ser enviada em Base64). |


#### Response

##### Sucesso (200 OK)
- Autenticação bem-sucedida.

###### Visão geral (campo data)
- O campo data do [Result](#result) contém um objeto com o token de acesso e informações sobre sua validade.

###### Exemplo (campo data)
```json
{
  "created": "2024-10-26T10:00:00Z",
  "expiration": "2024-10-26T11:00:00Z",
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "message": "Login realizado com sucesso."
}
```

###### Propriedades (campo data)
| Nome          | Tipo      | Obrigatório | Descrição                                                                      |
| :------------ | :-------- | :---------- | :----------------------------------------------------------------------------- |
| created     | date-time | Sim         | Data e hora de geração do token (UTC).                                         |
| expiration  | date-time | Sim         | Data e hora de expiração do token (UTC).                                       |
| accessToken | string    | Sim         | O token de acesso (JWT) a ser usado no cabeçalho Authorization: Bearer <token> das próximas requisições. |
| message     | string    | Sim         | Mensagem indicando o status da autenticação.                                  |

**Exemplo Completo (Response 200 OK):**
```json
{
    "statusCode": 200,
    "success": true,
    "message": "Autenticado com sucesso.",
    "data": {
        "created": "2024-10-26T10:00:00Z",
        "expiration": "2024-10-26T11:00:00Z",
        "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "message": "Login válido."
    }
}
```

##### Falha (Outros Status Codes)
- A API pode retornar os seguintes códigos de erro, seguindo o [Result](#result):
  - **404 Not Found**: Falha na autenticação (credenciais inválidas, apoiadoId não encontrado, senha não cadastrada).
    - Content: application/json
    - Exemplo:
      ```json
      {
          "statusCode": 404,
          "success": false,
          "message": "Dados de login inválidos.",
          "data": null
      }
      ```

##### Mensagens de retorno

| Mensagem retornada                                       | Descrição                                                    |
| :------------------------------------------------------- | :----------------------------------------------------------- |
| Login válido.                                            | Autenticação bem-sucedida.                                   |
| ID do Apoiado não informado.                             | O campo apoiadoId não foi enviado ou é inválido.           |
| Senha do Apoiado não informada.                          | O campo senha não foi enviado.                             |
| Apoiado não encontrado.                                  | O apoiadoId fornecido não existe.                          |
| Senha do Apoiado não cadastrada.                         | O Apoiado existe, mas não tem senha configurada.             |
| Dados de login inválidos.                                | A senha fornecida está incorreta.                            |
| O apoiado informado {0} não está ativo para integrar via API no Autolac. | Laboratório apoiado não configurado para integração API. |
| Tipo do laboratório de apoio não encontrado.             | O Tipo de integração não está configurado no cadastro do apoiado. |
| Token inválido.                                          | Erro durante a geração/validação do token.                   |

<div style="page-break-after: always"></div>
### Exames

#### Visão geral

- Consulta o cadastro de exames e seus componentes do laboratório de apoio, permitindo ao laboratório apoiado realizar o De/Para de exames.
- Retorna apenas exames autorizados para o laboratório apoiado (identificado pelo token JWT).
- Permite filtrar por ID do exame/componente e por versão de controle para obter apenas dados novos ou alterados desde a última consulta.
- Consulta de exames:
  - Path: /Api/Inter-Autolac/Exames
  - Método: GET
  - Autenticação: Requer Token JWT (Cabeçalho Authorization: Bearer <token>)

#### Request

- Observação: Por ser uma requisição GET por query, não há corpo (body) na requisição.

##### Parâmetros da Query

- A requisição é feita via método GET e os filtros são passados como parâmetros na query string.
- A consulta retorna apenas exames que estão autorizados para o laboratório apoiado identificado no token JWT.

| Parâmetro            | Tipo    | Obrigatório | Descrição                                                                                    | Exemplo         |
| :------------------- | :------ | :---------- | :------------------------------------------------------------------------------------------- | :-------------- |
| exameId              | string  | Não         | Filtra pelo ID específico do exame.                                                          | HEMO          |
| componenteId         | integer | Não         | Filtra por um ID de componente específico.                                                     | 123           |
| exameCtrlVersao      | long    | Não         | Filtra por exames com versão de controle maior que a informada (timestamp).                  | 1678886400000 |
| componenteCtrlVersao | long    | Não         | Filtra por componentes com versão de controle maior que a informada (timestamp).             | 1678886400000 |
| pageNumber           | integer | Não         | Número da página desejada (padrão: 1).                                                       | 2             |
| pageSize             | integer | Não         | Quantidade de registros por página (padrão definido no sistema).                             | 50            |

##### Exemplo de URL de requisição
```
https://[endereco-api]/Api/Inter-Autolac/Exames?ExameCtrlVersao=1678886400000&PageSize=20
```

#### Response

##### Sucesso (200 OK)
- Consulta realizada com sucesso. Retorna uma lista paginada de exames autorizados que correspondem aos filtros.
- Content-Type: application/json
- Header X-Pagination: Contém metadados da paginação.
  **Exemplo X-Pagination:**
  ```json
  {
    "TotalCount": 150,
    "PageSize": 20,
    "CurrentPage": 1,
    "TotalPages": 8,
    "HasNext": true,
    "HasPrevious": false
  }
  ```
  **Propriedades X-Pagination:**

  | Nome        | Tipo    | Descrição                                           |
  | :---------- | :------ | :-------------------------------------------------- |
  | TotalCount  | integer | Número total de itens disponíveis sem paginação.    |
  | PageSize    | integer | Número máximo de itens por página.                  |
  | CurrentPage | integer | Número da página atual retornada.                   |
  | TotalPages  | integer | Número total de páginas disponíveis.                |
  | HasNext     | boolean | Indica se existe uma próxima página (true/false). |
  | HasPrevious | boolean | Indica se existe uma página anterior (true/false).|

###### Visão geral (campo data)
- O campo data do [Result](#result) contém uma lista (array) de objetos ExameAutorizado. Cada objeto representa um exame autorizado para o laboratório apoiado, com seus respectivos componentes.

###### Exemplo (campo data)
```json
[
  {
    "exameId": "HEMOG",
    "descricao": "HEMOGRAMA COMPLETO",
    "ctrlVersao": 34001,
    "integracaoId": 1,
    "bloqueado": "F",
    "bloqueadoTemp": "F",
    "resultadoEmPdf": "F",
    "componentes": [
      {
        "componenteId": 101,
        "modoPreenchimentoResultado": "Informado",
        "descricao": "ERITROCITOS",
        "formatoResultado": "Numérico",
        "sigla": "ERI",
        "mascara": "ZZZ",
        "ctrlVersao": 38001
      }
      // ... outros componentes
    ]
  }
  // ... outros exames
]
```

###### Estruturas de Dados (campo data)

- ###### **Exame**
  
  - Representa um exame retornado pela consulta da integração. Contém informações básicas do exame e seus componentes.
  - **Propriedades:**
    | Nome           | Tipo    | Obrigatório | Descrição                                                                 | Modelo Relacionado          |
    | :------------- | :------ | :---------- | :------------------------------------------------------------------------ | :-------------------------- |
    | exameId      | string  | Sim         | Identificador único do exame (Sigla/Código).                              |                             |
    | descricao    | string  | Sim         | Nome/Descrição principal do exame.                                        |                             |
    | ctrlVersao   | integer | Sim         | Timestamp da última atualização do exame.                                 |                             |
    | integracaoId | integer | Sim         | ID de integração.                                                         |                             |
    | bloqueado    | string  | Sim         | Indica se o exame está bloqueado permanentemente.                         |                             |
    | bloqueadoTemp| string  | Sim         | Indica se o exame está bloqueado temporariamente.                         |                             |
    | resultadoEmPdf| string  | Sim         | Indica se o exame está configurado para receber resultados em PDF.        |                             |
    | componentes  | array   | Sim         | Lista dos componentes que formam o exame.                                 | [Componente](#componente) |
  
- ###### **Componente**
  
  - Representa um componente de um exame retornado pela consulta da integração InterAutolac.
  - **Propriedades:**
    
    | Nome                       | Tipo    | Obrigatório | Descrição                                                                                                |
    | :------------------------- | :------ | :---------- | :------------------------------------------------------------------------------------------------------- |
    | componenteId             | integer | Sim         | Identificador único do componente.                                                                       |
    | modoPreenchimentoResultado | string  | Sim         | Indica o tipo do resultado esperado para o componente. (Informado, Calculado, Inform/Calc)                   |
    | descricao                | string  | Não         | Nome/Descrição do componente.                                                                            |
    | formatoResultado         | string  | Não         | Indica o tipo do resultado. (Numérico, Descrição Digitada, Descrição Padrão, Germe, Parasita, Código/Descrição, Texto Digitado, Texto Padrão, Texto Formatado, Antimicrobiano, Imagem) |
    | sigla                    | string  | Não         | Sigla do componente.                                                                                     |
    | mascara                  | string  | Não         | Máscara de formatação para resultados numéricos.                                                         |
    | ctrlVersao               | integer | Sim         | Timestamp da última atualização do componente.                                                           |

**Exemplo Completo (Response 200 OK):**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Requisição processada com sucesso.",
  "data": [
    {
      "exameId": "HEMOG",
      "descricao": "HEMOGRAMA COMPLETO",
      "ctrlVersao": 34001,
      "integracaoId": 1,
      "bloqueado": "F",
      "bloqueadoTemp": "F",
      "resultadoEmPdf": "F",
      "componentes": [
        {
          "componenteId": 101,
          "modoPreenchimentoResultado": "Informado",
          "descricao": "ERITROCITOS",
          "formatoResultado": "Numérico",
          "sigla": "ERI",
          "mascara": "ZZZ",
          "ctrlVersao": 38001
        }
        // ... outros componentes
      ]
    }
    // ... outros exames
  ]
}
```

##### Falha (Outros Status Codes)
- A API pode retornar os seguintes códigos de erro, seguindo o [Result](#result):
  - **401 Unauthorized**: Token JWT ausente, inválido ou expirado.
    - Exemplo:
      ```json
      {
          "statusCode": 401,
          "success": false,
          "message": "Unauthorized",
          "data": null
      }
      ```
  - **403 Forbidden**: O usuário autenticado não tem permissão para a operação.
    - Exemplo:
      ```json
      {
          "statusCode": 403,
          "success": false,
          "message": "Acesso Negado",
          "data": null
      }
      ```
  - **404 Not Found**: Recurso não encontrado. Pode indicar que o laboratório apoiado (extraído do token) não existe ou que a consulta não retornou resultados para os filtros fornecidos.
    - Exemplo (Consulta não retornou resultados):
      ```json
      {
          "statusCode": 404,
          "success": false,
          "message": "Consulta não retornou resultados.",
          "data": null
      }
      ```
  - **422 Unprocessable Entity**: Erro na validação dos parâmetros da requisição (ex: tipo de dado incorreto).
    - Exemplo:
      ```json
      {
          "statusCode": 422,
          "success": false,
          "message": "Parâmetro(s) de consulta inválido(s).",
          "data": null
      }
      ```
  - **500 Internal Server Error**: Falha inesperada durante o processamento no servidor.
    - Exemplo:
      ```json
      {
          "statusCode": 500,
          "success": false,
          "message": "Ocorreu um erro interno no servidor.",
          "data": null
      }
      ```

##### Mensagens de Retorno
| Mensagem retornada                                           | Descrição                                                                                                 |
| :----------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| Requisição processada com sucesso.                           | A consulta foi bem-sucedida e retornou dados (ou uma lista vazia se nenhum item corresponder aos filtros).       |
| Unauthorized                                                 | Token JWT ausente, inválido ou expirado.                                                                       |
| Acesso Negado                                                | O usuário autenticado não tem permissão para a operação.                                                       |
| Laboratório apoiado não encontrado.                          | O ID do laboratório apoiado extraído do token não existe no cadastro.                                          |
| Consulta não retornou resultados.                            | Nenhum exame autorizado encontrado para os filtros fornecidos (ou nenhuma alteração desde a versão informada). |
| Parâmetro(s) de consulta inválido(s).                        | Erro na validação dos parâmetros da requisição (ex: tipo de dado incorreto). Mensagem específica pode variar. |
| Ocorreu um erro interno no servidor.                         | Falha inesperada durante o processamento no servidor.                                                          |
| Identificação do laboratório apoiado não encontrado no contexto da requisição HTTP. | Erro interno ao tentar extrair o ID do apoiado do token JWT.                                   |

##### Nota sobre versionamento
- Se um filtro de versão (exameCtrlVersao ou componenteCtrlVersao) for utilizado na requisição, a API retornará apenas os exames que foram alterados (ou tiveram componentes alterados) desde a versão informada. Um exame é retornado por completo, incluindo todos os seus componentes (mesmo os não alterados), com seus respectivos ctrlVersao, caso ele ou algum de seus componentes tenha sido modificado após o ctrlVersao do exameCtrlVersao fornecido no filtro.

<div style="page-break-after: always"></div>
### Pedidos

#### Visão geral
- Envia um lote de pedidos do laboratório apoiado para o Autolac para integração e geração de etiquetas.
- Envio de pedidos:
  - Path: /Api/Inter-Autolac/Pedidos
  - Método: POST
  - Content-Type: application/json
  - Autenticação: Requer Token JWT (Cabeçalho Authorization: Bearer <token>)

#### Request

##### Visão geral (Corpo da Requisição)
- O corpo da requisição deve ser um objeto PedidoLote, que representa um lote de pedidos enviados pelo laboratório apoiado.

##### Exemplo (Corpo da Requisição - PedidoLote)
```json
 {
   "codigoApoiado":123,
   "codigoLote":2024070401,
   "pedidos":[
      {
         "sequencial":1,
         "local":"01",
         "protocolo":987654,
         "atendimento":555111,
         "data":"2024-07-04",
         "hora":"09:15:00",
         "setorHospitalar":10,
         "leito":"201A",
         "paciente":{
            "codigo":112233,
            "nome":"João da Silva",
            "dataNascimento":"1990-05-20",
            "idade":34,
            "sexo":"Masculino",
            "nacionalidade":"10",
            "peso":80.5,
            "altura":1.75,
            "dataUltMens":null,
            "prontuario":"PAC9988",
            "cpf":"111.222.333-44",
            "nomeMae":"Maria Oliveira Silva"
         },
         "observacao":"Paciente relata dor abdominal.",
         "guias":[
            {
               "numeracao":1,
               "convenio":"CONV002",
               "plano":"PLAN002",
               "dataPedido":"2024-07-04",
               "solicitante":{
                  "nome":"Dra. Ana Souza",
                  "conselho":"CRM",
                  "ufConselho":"RJ",
                  "numeroConselho":"54321"
               },
               "guiaExames":[
                  {
                     "codigoExameApoio":"GLI",
                     "descricaoExameApoio":"Glicose",
                     "dataColeta":"2024-07-04",
                     "horaColeta":"09:20:00",
                     "material":"Soro",
                     "prioridade":"N",
                     "faturaExame":"T"
                  },
                  {
                     "codigoExameApoio":"HEMO",
                     "descricaoExameApoio":"Hemograma Completo",
                     "dataColeta":"2024-07-04",
                     "horaColeta":"09:20:00",
                     "material":"Sangue Total EDTA",
                     "prioridade":"U",
                     "faturaExame":"F"
                  }
               ]
            }
         ]
      }
   ]
}
```

##### Propriedades (Corpo da Requisição - PedidoLote)

| Nome            | Tipo    | Obrigatório | Descrição                                                                        | Modelo Relacionado    |
| --------------- | ------- | ----------- | -------------------------------------------------------------------------------- | --------------------- |
| codigoApoiado | integer | Sim         | Número de identificação único do laboratório apoiado no sistema Autolac (apoio). |                       |
| codigoLote    | integer | Sim         | Número sequencial que identifica o lote de pedidos sendo enviado.                |                       |
| pedidos       | array   | Sim         | Lista contendo um ou mais pedidos individuais.                                   | [Pedido](#pedido) |

---
##### Estruturas de Dados (Corpo da Requisição)

###### Pedido
- Representa um pedido individual dentro de um lote (PedidoLote) enviado pelo laboratório apoiado.
- **Exemplo:**
  
  ```json
  {
    "sequencial": 1,
    "local": "01",
    "protocolo": 123456,
    "atendimento": 98765,
    "data": "2024-10-26",
    "hora": "10:30:00",
    "setorHospitalar": 5,
    "leito": "A101",
    "paciente": { /* ... Objeto Paciente ... */ },
    "observacao": "Paciente em jejum de 8 horas.",
    "guias": [ { /* ... Objeto Guia ... */ } ]
  }
  ```
- **Propriedades:**
  
  | Nome              | Tipo    | Obrigatório | Tamanho Máximo | Descrição                                                    | Modelo Relacionado    |
  | ----------------- | ------- | ----------- | -------------- | ------------------------------------------------------------ | --------------------- |
  | sequencial      | integer | Sim         |                | Número sequencial do pedido dentro do lote.                  |                       |
  | local           | string  | Sim         | 2              | Identificação do local de coleta no apoiado.                 |                       |
  | protocolo       | integer | Sim         |                | Identificação do número do pedido no apoiado.                |                       |
  | atendimento     | integer | Não         |                | Identificação do número do atendimento no apoiado.           |                       |
  | data            | date    | Sim         |                | Data de cadastro do pedido no apoiado. Formato: YYYY-MM-DD. |                       |
  | hora            | time    | Sim         |                | Hora de cadastro do pedido no apoiado. Formato: HH:MM:SS.  |                       |
  | setorHospitalar | integer | Não         |                | ID do setor hospitalar.                                      |                       |
  | leito           | string  | Não         | 10             | Leito do paciente.                                           |                       |
  | paciente        | object  | Sim         |                | Dados do paciente associado a este pedido.                   | [Paciente](#paciente) |
  | observacao      | string  | Não         | 300            | Observação geral do pedido.                                  |                       |
  | guias           | array   | Sim         |                | Lista de guias associadas a este pedido.                     | [Guia](#guia)         |

###### Paciente
- Representa os dados do paciente associado a um pedido.
- **Exemplo:**
  ```json
  {
    "codigo": 556677,
    "nome": "Maria Silva",
    "dataNascimento": "1985-03-15",
    "idade": 39,
    "sexo": "Feminino",
    "nacionalidade": "10",
    "peso": 65.5,
    "altura": 1.68,
    "dataUltMens": "2024-10-01",
    "prontuario": "PRONT987",
    "cpf": "123.456.789-00",
    "nomeMae": "Ana Silva"
  }
  ```
- **Propriedades:**
  
  | Nome             | Tipo    | Obrigatório | Tamanho Máximo | Descrição                                                    |
  | ---------------- | ------- | ----------- | -------------- | ------------------------------------------------------------ |
  | codigo         | integer | Sim         |                | Código de identificação do paciente no sistema do laboratório apoiado. |
  | nome           | string  | Sim         | 50             | Nome completo do paciente.                                   |
  | dataNascimento | date    | Sim         |                | Data de nascimento do paciente. Formato: YYYY-MM-DD.       |
  | idade          | integer | Sim         |                | Idade do paciente em anos.                                   |
  | sexo           | string  | Sim         |                | Sexo do paciente. Valores permitidos: Masculino, Feminino, Não Espec., M, F. |
  | nacionalidade  | string  | Sim         |                | ID da nacionalidade do paciente. Consulte a tabela de códigos em [Nacionalidades](Nacionalidades.pdf). |
  | peso           | double  | Não         |                | Peso do paciente em Kg. Formato: NN ou NN.NN.            |
  | altura         | double  | Não         |                | Altura do paciente em metros. Formato: NN ou NN.NN.      |
  | dataUltMens    | date    | Não         |                | Data da última menstruação da paciente. Formato: YYYY-MM-DD. |
  | prontuario     | string  | Não         | 15             | Número do prontuário ou identificação do paciente em outra instituição. |
  | cpf            | string  | Não         |                | Cadastro de Pessoa Física (CPF) do paciente.                 |
  | nomeMae        | string  | Não         | 50             | Nome da mãe do paciente.                                     |
- **Observações:**
  
  - O campo peso e altura aceitam valores decimais com até duas casas após a vírgula (ex.: 70.50).

###### Guia
- Representa uma guia associada a um pedido.
- Contém informações sobre o convênio, plano, médico solicitante e a lista de exames da guia.
- **Exemplo:**
  ```json
  {
    "numeracao": 1,
    "convenio": "UNIMED",
    "plano": "DIVINOPOLIS",
    "dataPedido": "2024-10-26",
    "solicitante": { /* ... Objeto Solicitante ... */ },
    "guiaExames": [ { /* ... Objeto GuiaExame ... */ } ]
  }
  ```
- **Propriedades:**
  | Nome          | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado          |
  | ------------- | ------- | ----------- | ------------------------------------------------------------ | --------------------------- |
  | numeracao   | integer | Sim         | Número sequencial que identifica a guia dentro do pedido.    |                             |
  | convenio    | string  | Não         | Código ou identificação do convênio médico.                  |                             |
  | plano       | string  | Não         | Código ou identificação do plano de saúde dentro do convênio. |                             |
  | dataPedido  | date    | Sim         | Data em que a guia médica foi emitida ou o pedido médico foi feito. Formato: YYYY-MM-DD. |                             |
  | solicitante | object  | Não         | Dados do médico ou profissional de saúde que solicitou os exames desta guia. | [Solicitante](#solicitante) |
  | guiaExames  | array   | Sim         | Lista de exames solicitados especificamente nesta guia.      | [GuiaExame](#guiaexame)     |

###### Solicitante
- Representa os dados do médico ou profissional de saúde que solicitou os exames em uma guia.
- **Exemplo:**
  ```json
  {
    "nome": "Dr. Carlos Andrade",
    "conselho": "CRM",
    "ufConselho": "SP",
    "numeroConselho": "123456"
  }
  ```
- **Propriedades:**
  | Nome            | Tipo   | Obrigatório | Tamanho Máximo | Descrição                                                                                                                                                           |
  | :-------------- | :----- | :---------- | :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
  | nome          | string | Sim         | 50             | Nome do médico ou profissional solicitante. Obrigatório se o objeto solicitante for incluído.                                                                     |
  | conselho      | string | Sim         |                | Sigla do conselho profissional (Ex: CRM, CRO, COREN, etc.). Valores permitidos: CRAS, CRBIO, CRBM, CREF, COREN, CRF, CREFITO, CRFA, CRM, CRMV, CRN, CRO, CRP, CRTR, OUT. Obrigatório se o objeto solicitante for incluído. |
  | ufConselho    | string | Sim         | 2              | Sigla da Unidade Federativa (UF) do conselho profissional. Obrigatório se o objeto solicitante for incluído.                                                      |
  | numeroConselho| string | Sim         |                | Número de registro do profissional no conselho. Obrigatório se o objeto solicitante for incluído.                                                                 |

###### GuiaExame
- Representa um exame específico solicitado dentro de uma guia.
- **Exemplo:**
  ```json
  {
    "codigoExameApoio": "HEMO",
    "descricaoExameApoio": "Hemograma Completo",
    "dataColeta": "2024-10-26",
    "horaColeta": "10:35:00",
    "material": "Sangue Total (EDTA)",
    "prioridade": "N",
    "faturaExame": "T"
  }
  ```
- **Propriedades:**
  | Nome                  | Tipo   | Obrigatório | Tamanho Máximo | Descrição                                                                                                |
  | :-------------------- | :----- | :---------- | :------------- | :------------------------------------------------------------------------------------------------------- |
  | codigoExameApoio    | string | Sim         | 10             | Código de identificação do exame no sistema de apoio.                                                    |
  | descricaoExameApoio | string | Sim         | 60             | Descrição do exame (informativo, não usado para identificação).                                          |
  | dataColeta          | date   | Não<sup>1</sup>        |                | Data em que o material para este exame foi coletado. Formato: YYYY-MM-DD.                               |
  | horaColeta          | time   | Não<sup>1</sup>        |                | Hora em que o material para este exame foi coletado. Formato: HH:MM:SS.                                |
  | material            | string | Não         | 100            | Descrição do material biológico coletado para este exame.                                                |
  | prioridade          | string | Sim         | 1              | Indica a prioridade de processamento do exame. Valores permitidos: U (Urgente), N (Normal).          |
  | faturaExame         | string | Sim         |                | Indica se o exame deve ser faturado. Valores permitidos: T (True), F (False).                        |
- **Observações:**
  
  - <sup>1</sup>: Obrigatório de acordo com a parametrização do laboratório (Autolac).

---
#### Response

##### Sucesso (200 OK)
- Lote de pedidos recebido e processado pela API. A resposta indicará o status de processamento de cada pedido individualmente (integrado ou rejeitado) e pode conter sucessos e falhas parciais dentro do lote.
- Content-Type: application/json

###### Visão geral (campo data)
- O campo data do [Result](#result) contém um objeto PedidoLoteRetorno.
- Este objeto fornece um resumo do processamento do lote de pedidos enviado, incluindo quais pedidos foram integrados, quais foram rejeitados, e detalhes como erros e etiquetas geradas.

###### Exemplo (campo data - PedidoLoteRetorno)
```json
{
  "codigoApoiado": 123,
  "codigoLote": 2024011501,
  "qtdPedidosLote": 2,
  "qtdPedidosIntegrados": 1,
  "qtdExamesIntegrados": 3,
  "erros": [
    "Pedido Sequencial 2: Exame 'XYZ' não encontrado."
  ],
  "pedidosIntegrados": [
    {
      "sequencial":1,
      "identificacaoApoiado":"01-987654",
      "erros":[],
      "etiqueta":{
         "localApoiado":"01",
         "protocoloApoiado":987654,
         "recipientes":[
            {
               "codigoBarras":"240704000001",
               "etiqueta": "CONTEUDO_ZPL_OU_EPL_PARA_ETIQUETA_GLI",
               "exames": "GLI"
            }
         ]
      }
    }
  ],
  "pedidosRejeitados": [
    {
      "sequencial": 2,
      "identificacaoApoiado": "000001|999999911|LO|735",
      "erros": [ "Exame 'XYZ' não encontrado." ],
      "etiqueta": {
         "localApoiado": "",
         "protocoloApoiado": 0,
         "recipientes": []
       }
    }
  ]
}
```

###### Propriedades (campo data - PedidoLoteRetorno)

| Nome                   | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado              |
| :--------------------- | :------ | :---------- | :----------------------------------------------------------- | :------------------------------ |
| codigoApoiado        | integer | Sim         | Número de identificação do laboratório apoiado que enviou o lote. |                                 |
| codigoLote           | integer | Sim         | Número sequencial do lote processado.                        |                                 |
| qtdPedidosLote       | integer | Sim         | Quantidade total de pedidos recebidos no lote.               |                                 |
| qtdPedidosIntegrados | integer | Sim         | Quantidade de pedidos do lote que foram integrados com sucesso. |                                 |
| qtdExamesIntegrados  | integer | Sim         | Quantidade total de exames (dentro dos pedidos integrados) que foram processados com sucesso. |                                 |
| erros                | array   | Sim         | Lista (pode ser vazia) de mensagens de erro ocorridas durante o processamento do lote que não são específicas de um único pedido. | string[]                        |
| pedidosIntegrados    | array   | Sim         | Lista (pode ser vazia) detalhada dos pedidos que foram integrados com sucesso. | [PedidoRetorno](#pedidoretorno) |
| pedidosRejeitados    | array   | Sim         | Lista (pode ser vazia) detalhada dos pedidos que foram rejeitados devido a erros. | [PedidoRetorno](#pedidoretorno) |

**Exemplo Completo (Response 200 OK - Sucesso total):**

```json
{
   "statusCode":200,
   "success":true,
   "message":"Registro incluído com sucesso.",
   "data":{
      "codigoApoiado":123,
      "codigoLote":2024070401,
      "qtdPedidosLote":1,
      "qtdPedidosIntegrados":1,
      "qtdExamesIntegrados":2,
      "erros":[],
      "pedidosIntegrados":[
         {
            "sequencial":1,
            "identificacaoApoiado":"01-987654",
            "erros":[],
            "etiqueta":{
               "localApoiado":"01",
               "protocoloApoiado":987654,
               "recipientes":[
                  {
                     "codigoBarras":"240704000001",
                     "etiqueta": "CONTEUDO_ZPL_OU_EPL_PARA_ETIQUETA_GLI",
                     "exames": "GLI"
                  },
                  {
                     "codigoBarras":"240704000002",
                     "etiqueta": "CONTEUDO_ZPL_OU_EPL_PARA_ETIQUETA_HEMO",
                     "exames": "HEMO"
                  }
               ]
            }
         }
      ],
      "pedidosRejeitados":[]
   }
}
```
**Exemplo Completo (Response 200 OK - Sucesso parcial):**

```json
{
    "statusCode": 200,
    "success": false,
    "message": "Alguns pedidos do lote não foram processados.",
    "data": {
        "codigoApoiado": 123,
        "codigoLote": 2024070401,
        "qtdPedidosLote": 2,
        "qtdPedidosIntegrados": 1,
        "qtdExamesIntegrados": 1,
        "erros": [],
        "pedidosIntegrados": [
            {
               "sequencial":1,
               "identificacaoApoiado":"01-VALIDO",
               "erros":[],
               "etiqueta":{ /* ... dados da etiqueta ... */ }
            }
        ],
        "pedidosRejeitados": [
            {
               "sequencial": 2,
               "identificacaoApoiado": "02-INVALIDO",
               "erros": ["Exame 'XYZ' não encontrado."],
               "etiqueta": { "localApoiado": "", "protocoloApoiado": 0, "recipientes": [] }
            }
        ]
    }
}
```
---
##### Estruturas de Dados (Corpo da Resposta - campo data)

###### PedidoRetorno
- Representa o resultado do processamento de um pedido individual dentro de um lote, indicando se foi integrado ou rejeitado e fornecendo detalhes relevantes como erros ou etiquetas geradas.
- **Exemplo (Pedido integrado):**
  ```json
  {
    "sequencial": 1,
    "identificacaoApoiado": "01-123456",
    "erros": [],
    "etiqueta": { /* ... Objeto Etiqueta ... */ }
  }
  ```
- **Exemplo (Pedido rejeitado):**
  
  ```json
  {
    "sequencial": 2,
    "identificacaoApoiado": "000001|999999911|LO|735",
    "erros": [ "Exame 'XYZ' não encontrado." ],
    "etiqueta": {
       "localApoiado": "",
       "protocoloApoiado": 0,
       "recipientes": []
     }
  }
  ```
- **Propriedades:**
  | Nome                 | Tipo   | Obrigatório | Descrição                                                                                                                                                           | Modelo Relacionado                               |
  | :------------------- | :----- | :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------------------------------------- |
  | sequencial         | integer| Sim         | Número sequencial do pedido original dentro do lote enviado.                                                                                                        |                                                  |
  | identificacaoApoiado| string | Sim         | Identificação do pedido no sistema do apoiado (geralmente local-protocolo do envio).                                                                            |                                                  |
  | erros              | array  | Sim         | Lista (pode ser vazia) de mensagens de erro específicas para este pedido.                                                                                           |                                          |
  | etiqueta           | object | Sim         | Contém os dados das etiquetas geradas para este pedido (se integrado com sucesso). Será um objeto com estrutura mínima se o pedido foi rejeitado ou não gerou etiquetas. | [Etiqueta](#etiqueta) |

###### Etiqueta
- Contém a identificação original do pedido no sistema do apoiado e a lista de recipientes (com suas respectivas etiquetas e códigos de barras) gerados pelo Autolac para um pedido integrado com sucesso.
- **Exemplo:**
  ```json
  {
    "localApoiado": "01",
    "protocoloApoiado": 123456,
    "recipientes": [ { /* ... Objeto Recipiente ... */ } ]
  }
  ```
- **Propriedades:**
  | Nome               | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado        |
  | ------------------ | ------- | ----------- | ------------------------------------------------------------ | ------------------------- |
  | localApoiado     | string  | Sim         | Identificação do local de coleta original do pedido.         |                           |
  | protocoloApoiado | integer | Sim         | Número do protocolo original do pedido.                      |                           |
  | recipientes      | array   | Sim         | Lista (pode ser vazia) dos recipientes deste pedido, cada um com seu código de barras, etiqueta e exames. | [Recipiente](#recipiente) |

###### Recipiente
- Representa um recipiente específico (tubo, frasco, etc.) necessário para a coleta de um ou mais exames de um pedido, incluindo o código de barras e a etiqueta gerados pelo Autolac.
- **Exemplo:**
  ```json
  {
    "codigoBarras": "020057310002",
    "etiqueta": "CONTEUDO_ZPL_OU_EPL_DA_ETIQUETA_EXEMPLO",
    "exames": "GLI"
  }
  ```
- **Propriedades:**
  | Nome         | Tipo   | Obrigatório | Descrição                                                                              |
  | :----------- | :----- | :---------- | :------------------------------------------------------------------------------------- |
  | codigoBarras| string | Sim         | Código de barras gerado para identificação única deste recipiente/amostra.             |
  | etiqueta   | string | Sim         | Conteúdo da etiqueta para impressão (geralmente em formato ZPL ou EPL).                |
  | exames     | string | Sim         | Lista de exames (códigos/siglas) contidos neste recipiente/etiqueta.                   |

---
##### Falha (Outros Status Codes)
- Além do sucesso (200 OK, mesmo com falhas parciais no lote), a API pode retornar os seguintes códigos de erro principais, seguindo o [Result](#result):
  - **400 Bad Request**: A requisição está malformada (ex: JSON inválido).
    
    - Exemplo:
      ```json
      {
          "statusCode": 400,
          "success": false,
          "message": "Requisição inválida. Verifique o formato do JSON.",
          "data": null
      }
      ```
  - **401 Unauthorized**: Token JWT ausente, inválido ou expirado.
    - Exemplo:
      ```json
      {
          "statusCode": 401,
          "success": false,
          "message": "Unauthorized",
          "data": null
      }
      ```
  - **403 Forbidden**: O usuário autenticado não tem permissão para a operação.
    - Exemplo:
      ```json
      {
          "statusCode": 403,
          "success": false,
          "message": "Acesso Negado",
          "data": null
      }
      ```
  - **422 Unprocessable Entity**: Erros de validação que impedem o processamento do lote como um todo (detalhes em data.erros do PedidoLoteRetorno, mesmo com statusCode 200 e success false). Se o erro for na estrutura da requisição antes mesmo de tentar processar o lote, pode retornar 422 diretamente.
    
    - Exemplo (Erro de validação geral do lote):
      ```json
      {
          "statusCode": 422,
          "success": false,
          "message": "Lote não processado. Parâmetros inválidos.",
          "data": {
              "codigoApoiado": 123,
              "codigoLote": 2024011501,
              "qtdPedidosLote": 0,
              "qtdPedidosIntegrados": 0,
              "qtdExamesIntegrados": 0,
              "erros": ["Lote sem pedidos informados."],
              "pedidosIntegrados": [],
              "pedidosRejeitados": []
          }
      }
      ```
  - **500 Internal Server Error**: Falha inesperada durante o processamento no servidor.
    - Exemplo:
      ```json
      {
          "statusCode": 500,
          "success": false,
          "message": "Ocorreu um erro interno no servidor.",
          "data": null
      }
      ```

##### Mensagens de Retorno

###### Erros das validações iniciais do Lote (geralmente em data.erros do PedidoLoteRetorno)
| Mensagem retornada                                                                         | Descrição                                                                                                   |
| :----------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------- |
| Parâmetro(s) de consulta não informado(s).                                                 | O corpo da requisição (lote) está nulo ou vazio.                                                            |
| Laboratório apoiado não informado.                                                         | O campo codigoApoiado no lote não foi fornecido.                                                          |
| Código do laboratório apoiado inválido.                                                    | O campo codigoApoiado no lote é menor ou igual a zero.                                                    |
| Número do lote não informado.                                                              | O campo codigoLote no lote não foi fornecido.                                                             |
| Número do lote inválido.                                                                   | O campo codigoLote no lote é menor ou igual a zero.                                                       |
| Lote sem pedidos informados.                                                               | A lista pedidos no lote está nula ou vazia.                                                               |
| Existem pedidos no lote com sequenciais duplicados.                                         | A lista pedidos contém itens com o mesmo valor no campo sequencial.                                       |
| Laboratório apoiado não encontrado.                                                        | O codigoApoiado fornecido não corresponde a um laboratório apoiado válido no sistema.                       |
| Id do lab. apoiado não encontrado no contexto da requisição HTTP.                          | Erro interno ao obter o ID do apoiado a partir do token JWT.                                                |
| Identificação do laboratório apoiado difere da utilizada nas credenciais de login do InterAutolac. | O codigoApoiado no lote não corresponde ao ID do apoiado associado ao token JWT da requisição.            |
| Lote já importado.                                                                         | Já existe um registro de histórico para o codigoApoiado e codigoLote fornecidos.                          |
| Local de coleta informado no cadastro do laboratório apoiado não encontrado.                 | O localId configurado no cadastro do apoiado não é válido ou não foi encontrado (usado como padrão).       |
| Convênio informado no cadastro do laboratório apoiado não encontrado.                      | O convenioId configurado no cadastro do apoiado não é válido ou não foi encontrado (usado como padrão).    |
| Plano informado no cadastro do laboratório apoiado não encontrado.                         | O planoId (junto com convenioId) configurado no cadastro do apoiado não é válido ou não foi encontrado. |

###### Erros que rejeitam pedidos individuais (em data.pedidosRejeitados[n].erros)
| Mensagem retornada                                                                                   | Descrição                                                                                                     |
| :--------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------ |
| O valor do pedido não pode nulo.                                                                     | Um item na lista pedidos é nulo.                                                                            |
| O valor sequencial do pedido deve ser maior que zero.                                                | O campo sequencial de um pedido é menor ou igual a zero.                                                    |
| Guias do pedido não informadas.                                                                      | A lista guias de um pedido está vazia.                                                                      |
| Pedidos com guias sem exames informados.                                                             | Uma ou mais guias dentro de um pedido não contêm exames (guiaExames está vazio).                            |
| Existem exames duplicados na guia.                                                                   | A guia tem exames duplicados (exames com a mesma identificação).                                              |
| O pedido contém exames toxicológicos. O exame toxicológico deve ser cadastrado em um pedido exclusivo. | Um pedido contém um exame marcado como toxicológico Sodré junto com outros exames.                            |
| O Autolac utiliza protocolos com {0} dígitos. A informação recebida está maior que o permitido.       | O campo protocolo de um pedido excede o número de dígitos configurado (quando Utiliza Protocolo Apoiado). |
| Pedido já cadastrado.                                                                                | Já existe uma solicitação no Autolac com a mesma identificação (Local/Protocolo ou Identificação Apoiado).    |
| Local com informação maior que o permitido.                                                         | O campo local de um pedido tem mais de 2 caracteres.                                                        |
| Setor hospitalar não encontrado.                                                                     | O setorHospitalar informado em um pedido não corresponde a um setor válido.                                 |
| Convênio informado na guia não encontrado.                                                           | O convenio informado em uma guia não corresponde a um convênio válido.                                      |
| Plano informado na guia não encontrado.                                                              | O plano (junto com convenio) informado em uma guia não corresponde a um plano válido.                     |
| Código do exame no laboratório de apoio não informado.                                               | O campo codigoExameApoio em um guiaExame está vazio.                                                      |
| Exame informado não vinculado ao laboratório apoiado.                                                | O codigoExameApoio em um guiaExame não corresponde a um exame configurado para o apoiado no apoio.        |
| Exame informado no pedido bloqueado no laboratório de apoio.                                         | O exame correspondente ao codigoExameApoio está marcado como bloqueado (Bloqueado ou Bloqueado Temp).      |
| Tabela de preços do exame não encontrada.                                                            | Não foi encontrada uma entrada na tabela de preços para o exame e o plano da guia.                            |
| Dados do solicitante incompletos: Nome.                                                              | O campo nome do solicitante está vazio ao tentar cadastrar um novo solicitante.                             |
| Dados do solicitante incompletos: Conselho profissional.                                             | O campo conselho do solicitante está vazio ao tentar cadastrar um novo solicitante.                         |
| Dados do solicitante incompletos: UF do conselho profissional.                                       | O campo ufConselho do solicitante está vazio ao tentar cadastrar um novo solicitante.                       |
| Dados do solicitante incompletos: Número do conselho profissional.                                   | O campo numeroConselho do solicitante está vazio ao tentar cadastrar um novo solicitante.                   |
| Regime de atendimento não encontrado.                                                                | Falha ao buscar o parâmetro de sistema Regime de Atendimento durante processamento da guia.                   |

###### Mensagens gerais (no campo message da resposta principal do [Result](#result))
| Mensagem retornada                                                              | Situação Geral                                                                                                   |
| :------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------- |
| Lote não processado. Parâmetros inválidos.                                      | Uma ou mais validações iniciais do lote falharam (detalhes em data.erros).                                       |
| Todos os pedidos informados no lote foram rejeitados.                           | Nenhum pedido do lote pôde ser integrado (detalhes das rejeições em data.pedidosRejeitados).                     |
| Alguns pedidos do lote não foram processados.                                   | Houve sucesso parcial, mas nem todos os pedidos foram integrados (ver data.pedidosIntegrados, data.pedidosRejeitados). |
| Alguns pedidos do lote apresentam erros.                                        | Houve sucesso parcial, mas alguns pedidos foram rejeitados (detalhes em data.pedidosRejeitados).                 |
| Falha ao gerar as etiquetas pelo GeraEtiqueta.exe                               | O processo externo de geração de etiquetas falhou.                                                               |
| Arquivo gerado pelo GeraEtiqueta não encontrado.                                | O arquivo de saída do processo de etiquetas não foi encontrado.                                                  |
| Tempo para leitura do arquivo de etiquetas gerado pelo GeraEtiqueta expirado.   | O arquivo de saída do processo de etiquetas estava bloqueado.                                                    |
| Falha ao ler o arquivo .xml da etiqueta.                                        | Erro ao processar o conteúdo do arquivo de etiquetas.                                                            |
| Identificação do laboratório apoiado no lote de retorno não pode ser nula.      | Erro interno durante a preparação para gerar etiquetas.                                                          |
| Falha ao concluir a importação do lote.                                         | Erro genérico indicando que a importação não foi totalmente bem-sucedida.                                        |
| Pedido já inserido no lote de retorno.                                          | Erro interno ao construir o objeto de retorno.                                                                   |
| *Outras exceções inesperadas*                                                   | Erros não previstos capturados pela aplicação.                                                                   |

<div style="page-break-after: always"></div>
### Resultados

#### Visão geral
- Consulta os resultados de exames para um lote de protocolos especificados.
- Consulta de resultados:
  - Path: /Api/Inter-Autolac/Resultados
  - Método: POST
  - Content-Type: application/json
  - Autenticação: Requer Token JWT (Cabeçalho Authorization: Bearer <token>)

#### Request

##### Visão geral (Corpo da Requisição)
- O corpo da requisição deve ser um objeto ResultadoLoteConsulta.
- Representa um lote de protocolos cujos resultados estão sendo consultados pelo laboratório apoiado.

##### Exemplo (Corpo da Requisição - ResultadoLoteConsulta)
```json
{
  "codigoApoiado": 123,
  "codigoLote": 2024070501,
  "protocolos": [
    {
      "sequencial": 1,
      "localApoiado": "01",
      "protocoloApoiado": 987654,
      "exames": [
        { "codigoExameApoio": "GLI" },
        { "codigoExameApoio": "HEMO" }
      ]
    }
  ]
}
```

##### Propriedades (Corpo da Requisição - ResultadoLoteConsulta)
| Nome            | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado                      |
| --------------- | ------- | ----------- | ------------------------------------------------------------ | --------------------------------------- |
| codigoApoiado | integer | Sim         | Número de identificação único do laboratório apoiado no sistema Autolac. Deve corresponder ao token JWT. |                                         |
| codigoLote    | integer | Sim         | Número sequencial que identifica o lote de pedidos previamente enviado e que está sendo consultado. |                                         |
| protocolos    | array   | Sim         | Lista contendo um ou mais protocolos individuais a serem consultados. | [ProtocoloConsulta](#protocoloconsulta) |

---
##### Estruturas de Dados (Corpo da Requisição)

###### ProtocoloConsulta
- Representa um protocolo específico dentro do lote de consulta de resultados.
- **Exemplo:**
  ```json
  {
    "sequencial": 1,
    "localApoiado": "01",
    "protocoloApoiado": 987654,
    "exames": [
      { "codigoExameApoio": "GLI" }
    ]
  }
  ```
- **Propriedades:**
  | Nome               | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado              |
  | ------------------ | ------- | ----------- | ------------------------------------------------------------ | ------------------------------- |
  | sequencial       | integer | Sim         | Número sequencial único para identificar este protocolo dentro do lote na requisição e correlacionar com a resposta. |                                 |
  | localApoiado     | string  | Sim         | Código identificador do local de coleta no apoiado, referente ao protocolo original. Usado para encontrar a solicitação. |                                 |
  | protocoloApoiado | integer | Sim         | Número identificador do protocolo no apoiado, referente ao protocolo original. Usado para encontrar a solicitação. |                                 |
  | exames           | array   | Não         | Lista opcional de exames específicos a serem consultados. Se omitida ou vazia, todos os exames disponíveis do protocolo serão retornados. | [ExameConsulta](#exameconsulta) |


###### ExameConsulta
- Representa um exame específico a ser consultado dentro de um protocolo.
- **Exemplo:**
  ```json
  {
    "codigoExameApoio": "GLI"
  }
  ```
- **Propriedades:**
  | Nome                 | Tipo   | Obrigatório | Descrição                                                                                                  |
  | -------------------- | ------ | ----------- | ---------------------------------------------------------------------------------------------------------- |
  | codigoExameApoio   | string | Sim         | Código do exame no laboratório de apoio.                                                                   |
---

#### Response

##### Sucesso (200 OK)
- Consulta processada. A resposta indicará o status de processamento de cada protocolo individualmente e pode conter sucessos e falhas parciais.
- Content-Type: application/json

###### Visão geral (campo data)
- O campo data do [Result](#result) contém um objeto ResultadoLoteRetorno.
- Este objeto fornece os resultados consultados para o lote de protocolos especificado, detalhando o sucesso ou falha para cada protocolo.

###### Exemplo (campo data - ResultadoLoteRetorno)
```json
{
  "codigoApoiado": 123,
  "codigoLote": 2024070501,
  "protocolos": [
    {
      "sequencial": 1,
      "localApoiado": "01",
      "protocoloApoiado": 987654,
      "pacienteNome": "João da Silva",
      "erros": [],
      "exames": [
        {
          "exameApoioCodigo": "GLI",
          "exameApoioDescricao": "Glicose",
          // ... demais campos do exame
        }
      ],
      "laudoPdf": null
    },
    {
      "sequencial": 2,
      "localApoiado": "02",
      "protocoloApoiado": 987655,
      "pacienteNome": null,
      "erros": [ "Solicitação não encontrada para a identificação 02|987655." ],
      "exames": [],
      "laudoPdf": null
    }
  ]
}
```

###### Propriedades (campo data - ResultadoLoteRetorno)
| Nome            | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado                    |
| --------------- | ------- | ----------- | ------------------------------------------------------------ | ------------------------------------- |
| codigoApoiado | integer | Sim         | Número de identificação do laboratório apoiado que enviou o lote. |                                       |
| codigoLote    | integer | Sim         | Número sequencial do lote consultado.                        |                                       |
| protocolos    | array   | Sim         | Lista contendo o resultado da consulta para cada protocolo enviado na requisição (pode conter erros). | [ProtocoloRetorno](#protocoloretorno) |

**Exemplo Completo (Response 200 OK - Sucesso total):**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Consulta realizada com sucesso.",
  "data": {
    "codigoApoiado": 123,
    "codigoLote": 2024070501,
    "protocolos": [
      {
        "sequencial": 1,
        "localApoiado": "01",
        "protocoloApoiado": 987654,
        "pacienteNome": "João da Silva",
        "erros": [],
        "exames": [
          {
            "exameApoioCodigo": "GLI",
            "exameApoioDescricao": "Glicose",
            "dataHoraLiberacao": "2024-07-05T10:30:00",
            "observacao": "Q29sZXRhIHJlYWxpemFkYSBlbSBqZWp1bS4=",
            "resultadoEmPdf": false,
            "resultadoPdf": null,
            "componentes": [
              {
                "componenteApoioId": 101,
                "modoPreenchimentoResultado": "Informado",
                "formatoResultado": "Numérico",
                "codigo": "GLI",
                "resultado": "OTU=",
                "referencia": {
                  "referenciaId": 50,
                  "sexo": "Ambos",
                  "idadeMinima": { "tipo": "Dia(s)", "valor": 0 },
                  "idadeMaxima": { "tipo": "Ano(s)", "valor": 999 },
                  "valorMinimo": 70.0,
                  "valorMaximo": 99.0,
                  "valorTexto": null,
                  "referenciaTexto": "NzAgYSA5OSBtZy9kTA==",
                  "unidade": "U/mL",
                  "metodo": "RW56aW3DoXRpY28="
                },
                "reguaReferencial": {
                  "valor": 10,
                  "imagem":"BASE64_DE_IMAGEM_BMP_DA_REGUA",
                  "rtf":"BASE64_DE_IMAGEM_RTF_DA_REGUA"
                }
              }
            ],
            "responsavel": {
              "nome": "Dr. Carlos Andrade",
              "conselho": "CRBM",
              "conselhoUf": "SP",
              "conselhoNumero": "12345"
            }
          }
        ],
        "laudoPdf": null
      }
    ]
  }
}
```
**Exemplo Completo (Response 200 OK - Sucesso parcial):**
```json
{
  "statusCode": 200,
  "success": false,
  "message": "Consulta realizada com erros parciais.",
  "data": {
    "codigoApoiado": 123,
    "codigoLote": 2024070501,
    "protocolos": [
      {
        "sequencial": 1,
        "localApoiado": "01",
        "protocoloApoiado": 987654,
        "pacienteNome": null,
        "erros": [
          "Solicitação não encontrada para a identificação do apoiado."
        ],
        "exames": [],
        "laudoPdf": null
      },
      {
        "sequencial": 2,
        "localApoiado": "02",
        "protocoloApoiado": 987655,
        "pacienteNome": "Maria Oliveira",
        "erros": [],
        "exames": [ /* ... exames do protocolo 2 ... */ ],
        "laudoPdf": null
      }
    ]
  }
}
```
---
##### Estruturas de Dados (Corpo da Resposta - campo data)

###### ProtocoloRetorno
- Representa o resultado da consulta para um protocolo específico dentro do lote.
- **Exemplo (Sucesso):**
  ```json
  {
    "sequencial": 1,
    "localApoiado": "01",
    "protocoloApoiado": 987654,
    "pacienteNome": "João da Silva",
    "erros": [],
    "exames": [ { /* ... Objeto ExameRetorno ... */ } ],
    "laudoPdf": null
  }
  ```
- **Exemplo (Erro):**
  ```json
  {
    "sequencial": 2,
    "localApoiado": "02",
    "protocoloApoiado": 987655,
    "pacienteNome": null,
    "erros": [ "Solicitação não encontrada para a identificação 123|2024070501|02|987655." ],
    "exames": [],
    "laudoPdf": null
  }
  ```
- **Propriedades:**
  | Nome               | Tipo    | Obrigatório | Descrição                                                    | Modelo Relacionado                                |
  | ------------------ | ------- | ----------- | ------------------------------------------------------------ | ------------------------------------------------- |
  | sequencial       | integer | Sim         | Número sequencial do protocolo original (enviado na requisição) para correlação. |                                                   |
  | localApoiado     | string  | Sim         | Código identificador do local de coleta no apoiado, referente ao protocolo original. |                                                   |
  | protocoloApoiado | integer | Sim         | Número identificador do protocolo no apoiado, referente ao protocolo original. |                                                   |
  | pacienteNome     | string  | Não         | Nome do paciente associado ao protocolo encontrado. Pode ser nulo se o protocolo não for encontrado. |                                                   |
  | erros            | array   | Sim         | Lista (pode ser vazia) de mensagens de erro específicas para a consulta deste protocolo. |                                                   |
  | exames           | array   | Sim         | Lista (pode ser vazia) contendo os resultados dos exames solicitados para este protocolo. | [ExameRetorno](#exameretorno-response-resultados) |
  | laudoPdf         | string  | Não         | String Base64 contendo o PDF do laudo consolidado para os exames deste protocolo se o parâmetro [[Integração com Apoio e Apoiado](https://dev.azure.com/gruposym/Autolac/_wiki/wikis/Autolac.wiki?wikiVersion=GBwikiMaster&pagePath=/Autolac/Configura%C3%A7%C3%B5es/Sistema/Par%C3%A2metros/Integra%C3%A7%C3%A3o%20com%20Apoio%20e%20Apoiado&anchor=configuracao_webservice)] > Usa a geração de laudo em PDF?] estiver marcado e a configuração do cadastro Apoiado indicar que não retorna o PDF por exame, caso contrário nulo. |                                                   |

###### ExameRetorno 
- Representa o resultado de um exame específico consultado dentro de um protocolo.
- **Exemplo:**
  ```json
  {
    "exameApoioCodigo": "GLI",
    "exameApoioDescricao": "Glicose",
    "dataHoraLiberacao": "2024-07-05T10:30:00",
    "observacao": "Q29sZXRhIHJlYWxpemFkYSBlbSBqZWp1bS4=",
    "resultadoEmPdf": false,
    "resultadoPdf": null,
    "componentes": [ { /* ... Objeto ComponenteRetorno ... */ } ],
    "responsavel": { /* ... Objeto ResponsavelRetorno ... */ }
  }
  ```
- **Propriedades:**
  
  | Nome                  | Tipo    | Obrigatório | Descrição                                                                                                                            | Modelo Relacionado                         |
  | --------------------- | ------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
  | exameApoioCodigo    | string  | Sim         | Código do exame no laboratório de apoio (ou TUSS).                                                                                   |                                            |
  | exameApoioDescricao | string  | Sim         | Descrição do exame.                                                                                                                  |                                            |
  | dataHoraLiberacao   | datetime| Não         | Data e hora em que o resultado do exame foi liberado/digitado no Autolac (formato ISO 8601). Pode ser nulo se ainda não liberado.      |                                            |
  | observacao            | string  | Não         | Observações específicas registradas para este exame na solicitação. Codificado em Base64.                                            |                                            |
  | resultadoEmPdf        | boolean | Sim         | Se a configuração do Apoiado indicar retorno de PDF por exame e o exame é do tipo PDF, o conteúdo do PDF será retornado (true). Caso contrário, não será retornado PDF por exame (false). |                                            |
  | resultadoPdf          | string  | Não         | PDF do resultado apenas deste exame, se a configuração do Apoiado indicar retorno de PDF por exame e a indicação anterior for positiva (ou seja, o exame é do tipo PDF) ou a opção por exame e consolidado estejam desmarcados, codificado em Base64, ou nulo. |                                            |
  | componentes           | array   | Sim         | Lista (pode ser vazia se resultadoEmPdf for true) dos componentes do exame com seus resultados.                                  | [ComponenteRetorno](#componenteretorno-response-resultados) |
  | responsavel           | object  | Não         | Dados do profissional responsável pela liberação/conferência do resultado deste exame.                                               | [ResponsavelRetorno](#responsavelretorno-response-resultados) |

###### ComponenteRetorno
- Representa o resultado de um componente específico de um exame.
- **Exemplo:**
  ```json
  {
    "componenteApoioId": 101,
    "modoPreenchimentoResultado": "Informado",
    "formatoResultado": "Numérico",
    "codigo": "GLI",
    "resultado": "OTU=",
    "referencia": { /* ... Objeto ReferenciaRetorno ... */ },
    "reguaReferencial": { /* ... Objeto ReguaReferencialRetorno ... */ }
  }
  ```
- **Propriedades:**
  | Nome                       | Tipo    | Obrigatório | Descrição                                                                                                                            | Modelo Relacionado                             |
  | -------------------------- | ------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------- |
  | componenteApoioId        | integer | Sim         | Identificador único do componente.                                                                                                 |                                                |
  | modoPreenchimentoResultado | string  | Sim         | Tipo do resultado esperado para o componente. (Informado, Calculado, Inform/Calc).                                                 |                                                |
  | formatoResultado         | string  | Sim         | Tipo do resultado do componente ([Tipos de Componentes](#tipos-de-componentes)).                             |                                                |
  | codigo                   | string  | Não         | Código/Sigla do componente.                                                                                                          |                                                |
  | resultado                | string  | Sim         | Valor do resultado do componente, codificado em Base64.                                                                              |                                                |
  | referencia               | object  | Não         | Detalhes dos valores de referência vinculados ao resultado deste componente.                                                         | [ReferenciaRetorno](#referenciaretorno) |
  | reguaReferencial         | object  | Não         | Detalhes da régua referencial vinculada ao resultado deste componente (Componentes numéricos com referência vinculada).                | [ReguaReferencialRetorno](#reguareferencialretorno) |

###### Tipos de Componentes 
| Nome                 | Tipo               |
| -------------------- | ------------------ |
| Numérico             | Número             |
| Descrição Digitada   | Texto              |
| Descrição Padrão     | Texto              |
| Germe                | Texto              |
| Parasita             | Texto              |
| Código/Descrição     | Texto              |
| Texto Digitado       | Texto (longo)      |
| Texto Padrão         | Texto (longo)      |
| Texto Formatado      | Texto (Código RTF) |
| Antimicrobiano       | Texto (longo)      |
| Imagem               | Base64             |

###### ReferenciaRetorno
- Representa os valores de referência associados a um componente de exame.
- **Exemplo:**
  ```json
  {
    "referenciaId": 50,
    "sexo": "Ambos",
    "idadeMinima": { "tipo": "Dia(s)", "valor": 0 },
    "idadeMaxima": { "tipo": "Ano(s)", "valor": 999 },
    "valorMinimo": 70.0,
    "valorMaximo": 99.0,
    "valorTexto": null,
    "referenciaTexto": "NzAgYSA5OSBtZy9kTA==",
    "unidade": "U/mL",
    "metodo": "RW56aW3DoXRpY28="
  }
  ```
- **Propriedades:**
  | Nome            | Tipo    | Obrigatório | Descrição                                                                    |
  | --------------- | ------- | ----------- | ---------------------------------------------------------------------------- |
  | referenciaId  | integer | Sim         | ID da referência utilizada (Controle interno do apoio).                      |
  | sexo          | string  | Sim         | Sexo ao qual a referência se aplica (Ambos, Masculino, Feminino).        |
  | idadeMinima.tipo| string| Sim         | Tipo da idade mínima da referência (dia(s), mês(es), ano(s)).                  |
  | idadeMinima.valor| integer| Sim         | Idade mínima para a qual a referência se aplica.                             |
  | idadeMaxima.tipo| string| Sim         | Tipo da idade máxima da referência (dia(s), mês(es), ano(s)).                  |
  | idadeMaxima.valor| integer| Sim         | Idade máxima para a qual a referência se aplica.                             |
  | valorMinimo   | number  | Não         | Limite inferior do intervalo de referência numérico.                           |
  | valorMaximo   | number  | Não         | Limite superior do intervalo de referência numérico.                           |
  | valorTexto    | string  | Não         | Valor de referência para textos. Codificado em Base64.                         |
  | referenciaTexto| string | Não         | Texto descritivo completo da referência. Codificado em Base64.                 |
  | unidade       | string  | Não         | Unidade associado a referência.                                                |
  | metodo        | string  | Não         | Método associado a referência. Codificado em Base64.                           |

###### ReguaReferencialRetorno
- Representa a régua referencial do resultado, contendo o valor percentual do resultado dentro da referência, e a imagem da régua em formato bmp e rtf.
- **Exemplo:**
  ```json
  {
    "valor": 10,
    "imagem": "BASE64_DE_IMAGEM_BMP_DA_REGUA",
    "rtf": "BASE64_DE_IMAGEM_RTF_DA_REGUA"
  }
  ```
- **Propriedades:**
  | Nome    | Tipo    | Obrigatório | Descrição                                                                 |
  | ------- | ------- | ----------- | ------------------------------------------------------------------------- |
  | valor | integer | Sim         | Valor da régua referencial.                                               |
  | imagem| string  | Sim         | Imagem em formato BMP. Codificado em Base64.                              |
  | rtf   | string  | Sim         | Imagem em formato RTF. Codificado em Base64.                              |

###### ResponsavelRetorno
- Representa o profissional que liberou/conferiu o resultado do exame.
- **Exemplo:**
  ```json
  {
    "nome": "Dr. Carlos Andrade",
    "conselho": "CRBM",
    "conselhoUf": "SP",
    "conselhoNumero": "12345"
  }
  ```
- **Propriedades:**
  | Nome           | Tipo   | Obrigatório | Descrição                                       |
  | -------------- | ------ | ----------- | ----------------------------------------------- |
  | nome         | string | Sim         | Nome completo do profissional responsável.      |
  | conselho     | string | Não         | Sigla do conselho profissional (ex: CRM, CRBM). |
  | conselhoUf   | string | Não         | UF do conselho profissional.                    |
  | conselhoNumero| string | Não         | Número de registro no conselho profissional.    |

---
##### Falha (Outros Status Codes)
- Além do sucesso (200 OK, mesmo com falhas parciais no lote), a API pode retornar os seguintes códigos de erro principais, seguindo o [Result](#result):
  - **400 Bad Request**: A requisição está malformada (ex: JSON inválido).
    - Exemplo:
      ```json
      {
          "statusCode": 400,
          "success": false,
          "message": "Requisição inválida. Verifique o formato do JSON.",
          "data": null
      }
      ```
  - **401 Unauthorized**: Token JWT ausente, inválido ou expirado.
    - Exemplo:
      ```json
      {
          "statusCode": 401,
          "success": false,
          "message": "Unauthorized",
          "data": null
      }
      ```
  - **403 Forbidden**: O usuário autenticado não tem permissão para a operação.
    - Exemplo:
      ```json
      {
          "statusCode": 403,
          "success": false,
          "message": "Acesso Negado",
          "data": null
      }
      ```
  - **422 Unprocessable Entity**: Erros de validação que impedem o processamento do lote como um todo (detalhes em data.erros do ResultadoLoteRetorno, mesmo com statusCode 200 e success false).
    - Exemplo (Erro de validação geral do lote):
      ```json
      {
          "statusCode": 422, // Ou 200 com success: false
          "success": false,
          "message": "Lote não processado. Parâmetros inválidos.",
          "data": {
              "codigoApoiado": 123,
              "codigoLote": 2024070501,
              "erros": ["Protocolos não informados."], // Exemplo de erro de lote
              "protocolos": []
          }
      }
      ```
  - **500 Internal Server Error**: Falha inesperada durante o processamento no servidor.
    - Exemplo:
      ```json
      {
          "statusCode": 500,
          "success": false,
          "message": "Ocorreu um erro interno no servidor.",
          "data": null
      }
      ```

##### Mensagens de Retorno

###### Erros das validações iniciais do Lote (geralmente em data.erros do ResultadoLoteRetorno ou na message principal se o lote não puder ser processado)
| Mensagem retornada                                                              | Descrição                                                                                                   |
| :------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------- |
| Parâmetro(s) de consulta não informado(s).                                      | O corpo da requisição (lote) está nulo ou vazio.                                                            |
| Laboratório apoiado não informado.                                              | O campo codigoApoiado no lote não foi fornecido.                                                          |
| Código do laboratório apoiado inválido.                                         | O campo codigoApoiado no lote é menor ou igual a zero.                                                    |
| Número do lote não informado.                                                   | O campo codigoLote no lote não foi fornecido.                                                             |
| Número do lote inválido.                                                        | O campo codigoLote no lote é menor ou igual a zero.                                                       |
| Protocolos não informados.                                                      | A lista protocolos no lote está nula ou vazia.                                                            |
| Quantidade máxima de protocolos por lote excedida ({0}). Limite: {1}.            | A lista protocolos contém mais itens que o permitido (o limite é geralmente 1000).                        |
| Existem protocolos no lote com sequenciais duplicados.                           | A lista protocolos contém itens com o mesmo valor no campo sequencial.                                    |
| Existem protocolos no lote com sequenciais inválidos (menor ou igual a zero).    | A lista protocolos contém itens com sequencial <= 0.                                                    |
| Laboratório apoiado não encontrado.                                             | O codigoApoiado fornecido não corresponde a um laboratório apoiado válido no sistema.                       |
| Id do lab. apoiado não encontrado no contexto da requisição HTTP.               | Erro interno ao obter o ID do apoiado a partir do token JWT.                                                |
| Identificação do laboratório apoiado difere da utilizada nas credenciais de login do InterAutolac. | O codigoApoiado no lote não corresponde ao ID do apoiado associado ao token JWT da requisição.            |
| Lote não encontrado no histórico de importação.                                 | O lote (codigoApoiado + codigoLote) não foi encontrado no histórico, indicando que talvez não tenha sido integrado via /Pedidos. |

###### Erros durante a Consulta (em data.protocolos[n].erros)
| Mensagem retornada                                                                 | Descrição                                                                                                                     |
| :--------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| Solicitação não encontrada para a identificação {0}. (Mensagem formatada com o identificador) | Não foi encontrada uma solicitação correspondente ao localApoiado e protocoloApoiado dentro do lote/apoiado especificado. |
| Erro ao buscar resultados para o exame X.                                          | Falha específica ao tentar recuperar os dados de um exame dentro de um protocolo (ex: exame não encontrado na solicitação, erro de banco). |
| Falha ao gerar PDF do laudo/exame.                                                 | Problema durante a geração do PDF (se solicitado/aplicável).                                                                  |

###### Mensagens gerais (no campo message da resposta principal do [Result](#result))
| Mensagem retornada                                           | Situação Geral                                                                                                   |
| :----------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| Lote não processado. Parâmetros inválidos.                   | Uma ou mais validações iniciais do lote falharam (detalhes em data.erros).                                       |
| Falha ao consultar resultados.                               | Erro genérico indicando que a consulta não foi totalmente bem-sucedida (pode haver erros em data.protocolos[n].erros). |
| Consulta realizada com sucesso.                              | Todos os protocolos/exames solicitados foram encontrados e processados.                                          |
| Consulta realizada com erros parciais.                       | Alguns protocolos/exames não puderam ser encontrados ou processados (ver detalhes em data).                      |
| _Outras exceções inesperadas_                                | Erros não previstos capturados pela aplicação (Ex: 500 Internal Server Error).                                 |
