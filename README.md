# Desafio de Hackathon: "Ecommerce Manager"

## Contexto

Bem-vindo à nossa equipe!

Gostaria de dar as boas-vindas e compartilhar algumas informações importantes sobre o nosso projeto. Estamos trabalhando no desenvolvimento de uma plataforma de e-commerce que precisa oferecer todas as funcionalidades essenciais para um site de compras online.

Utilizaremos conceitos avançados de arquitetura distribuída, implementados com Java e Spring, para assegurar que entregamos a melhor solução possível para a empresa. Isso envolve não apenas o desenvolvimento das nossas aplicações, mas também a integração com serviços externos que outras equipes utilizarão.

O diagrama abaixo fornece uma visão geral do nosso ecossistema, destacando as aplicações sob nossa responsabilidade. As requisições são inicialmente processadas por um BFF (Backend for Frontend), que gerencia a segurança para o acesso às aplicações dentro da VPC. Após essa verificação, as requisições são encaminhadas para os serviços internos.

![Ecossistema ADJT](/docs/readme-files/diagrama.svg)

De acordo com a estratégia da nossa empresa, cada equipe possui um Api-Gateway que controla quais endpoints podem ser acessados através do BFF. Nossa equipe se encarrega da funcionalidade de busca de produtos (excluindo controle de estoque), da gestão dos carrinhos de compras dos clientes e do processo de checkout desses carrinhos.

Estamos entusiasmados com as contribuições que você trará para o projeto e confiantes de que, juntos, alcançaremos excelentes resultados. Boa sorte com o desafio!

## Requisitos gerais 

1. **Sistema Distribuído:** Nossos sistemas são complemente independentes, porém estão organizados neste monorepo, com o intuito de facilitar o agrupamento das aplicações de cada squad. Você deverá clonar esse repositório e em seguida criar uma Branch com o nome do seu grupo. Após a conclusão, você deverá abrir uma Pull Request para Branch `develop`. Os requisitos funcionais de cada microsserviço está nos seus respectivos readmes:

   
# Aplicação de Gerenciamento de Carrinho de Compras

Esta aplicação fornece um serviço de gerenciamento de carrinho de compras para consumidores em um ambiente de e-commerce. O serviço permite que os usuários interajam com seus carrinhos de compras de forma eficiente e prática através de uma API RESTful. As principais funcionalidades incluem:

- **Adicionar Itens ao Carrinho:** Permite que os consumidores adicionem itens ao seu carrinho, especificando a quantidade desejada.
- **Remover Itens do Carrinho:** Oferece a capacidade de remover itens específicos do carrinho.
- **Limpar o Carrinho:** Facilita a limpeza total do carrinho quando o checkout é finalizado.

A aplicação é projetada para lidar com a manipulação de itens em carrinhos de compras de forma robusta, garantindo que as operações sejam realizadas com dados válidos e retornando mensagens de erro claras quando necessário.

A aplicação visa melhorar a experiência do usuário e a eficiência no gerenciamento de carrinhos de compras em plataformas de e-commerce.

2. Projetos

## Especificação do Serviço de Carrinho de Compras

### 1. Adicionar Item ao Carrinho

- **Endpoint:** `POST /items`
- **Descrição:** Adiciona um item ao carrinho de um consumidor específico.
- **Corpo da Requisição (JSON):**
  ```json
  {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002",
    "itemId": "1",
    "quantity": "2"
  }
  ````
- **Respostas**
  - 200 OK
  ```
  {
  "message": "Item added to cart successfully"
  }
  ```
  - 400 Bad request
    - Quando o consumerId não for um UUID
    ```
    {
     "error": "Invalid consumerId format"
    }
    ```
    - Quando o itemId não existir
    ```
    {
     "error": "Invalid itemId does not exist"
    }
    ```
    - Quando a quantidade do itemId for inválida
    ```
    {
     "error": "Invalid itemId quantity"
    }
    ```
### 2. Remover Item do Carrinho
- **Endpoint:** `DELETE /item`
- **Descrição:** Remove uma unidade do item específico do carrinho de um consumidor. Caso o item atinga quantidade zero, removê-lo.
- **Corpo da Requisição (JSON):**
  ```json
    {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002",
    "itemId": 1
    }
  ````
- **Respostas**
    - 200 OK
  ```
    {
    "message": "Item removed from cart successfully"
    }
  ```
  - 400 Bad request
      - Quando o consumerId não for um UUID
    ```
    {
     "error": "Invalid consumerId format"
    }
    ```
### 3. Incrementar Quantidade de Item do Carrinho
- **Endpoint:** `UPDATE /item`
- **Descrição:** Incrementa uma unidade de um item específico do carrinho de um consumidor.
- **Corpo da Requisição (JSON):**
  ```json
    {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002",
    "itemId": 1
    }
  ````
- **Respostas**
    - 200 OK
  ```
    {
    "message": "Item removed from cart successfully"
    }
  ```
    - 400 Bad request
        - Quando o consumerId não for um UUID
      ```
      {
       "error": "Invalid consumerId format"
      }
      ```
        - Quando o itemID não existir no carrinho
      ```
      {
       "error": "Invalid itemId"
      }
      ```
### 4. Buscar carrinho do consumidor
- **Endpoint:** `GET /`
- **Descrição:** Deve retorna o carrinho de um consumidor.
- **Corpo da Requisição (JSON):**
  ```json
    {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002"
    }
  ````
- **Respostas**
    - 200 OK
  ```
  {
    "items": [
      {
        "itemId": 1,
        "qnt": 4
      }
    ]
  }
  ```
    - 400 Bad request
        - Quando o consumerId possui um carrinho vazio
      ```
      {
       "error": "Empty cart"
      }
      ```
    - 400 Bad request
        - Quando o consumerId não for um UUID
      ```
      {
       "error": "Invalid consumerId format"
      }
      ```
      
### 5. Limpar o Carrinho
- **Endpoint:** `DELETE /`
- **Descrição:** Remove todo o carrinho de um consumidor.
- **Corpo da Requisição (JSON):**
  ```json
    {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002"
    }
  ````
- **Respostas**
    - 200 OK
  ```
    {
    "message": "Items removed from cart successfully"
    }
  ```
    - 400 Bad request
        - Quando o consumerId não for um UUID
      ```
      {
       "error": "Invalid consumerId format"
      }
      ```

## Validações e Erros

### Consumer
- **Invalid consumerId format:** O consumerId deve ser um UUID válido. Caso contrário, retorna erro 400.

### Item
- **Invalid itemId:** O itemId deve corresponder a um item existente no sistema. Caso contrário, retorna erro 400. O item deve existir na 
- **Invalid quantity:** A quantidade deve ser um número inteiro positivo. Caso contrário, retorna erro 400.

***ATENÇÃO: As validações que envolvem a manipulação do item devem consultar a api responsável pelos produtos.***

# Checkout Service

Esta aplicação é responsável por gerenciar o processo de checkout no ecossistema de e-commerce. Ele atua como o ponto central para a finalização de compras, garantindo que todas as etapas necessárias sejam executadas corretamente.

## Responsabilidades

- **Processamento de Pagamentos:** Gerencia a comunicação com a API de pagamento externa para efetuar cobranças.
- **Persistência de Pedidos:** Cria e armazena objetos de pedido (`order`) após a confirmação do pagamento.
- **Integração com Carrinho:** Obtém informações do carrinho de compras para processar o pedido.


#### Fluxo de Processamento
1. Obter Carrinho: O serviço deve buscar o carrinho do consumidor através do endpoint GET /cart/.
2. Persistir Pedido: Após obter o carrinho, deve persistir um objeto chamado order, gerando um UUID para o mesmo e com os items e suas quantidades.
3. Efetuar Cobrança: Utilizar a API externa payment-api para efetuar a cobrança.
4. Tempo de Resposta: A requisição deve responder em menos de 300 ms.
5. Processamento Assíncrono: Estar preparado para cenários onde a payment-api não consiga responder a tempo, recomendamos o processamento do pagamento de forma assíncrona.
6. O order pode ter 3 status: `pending`,`approved` e `declined`
7. O carrinho deve ser limpo após a geração do pedido


## Requisitos do Serviço

### Criar um checkout

O endpoint `/` deve receber uma chamada `POST` com o `consumerId` e as informações de pagamento.

- **Endpoint:** `POST /`
- **Descrição:** Processa o pagamento e cria um pedido. Casso 
- **Corpo da Requisição (JSON):**
  ```json
  {
    "consumerId": "153e23c8-322e-4fec-b9c4-72b8f74ad002",
    "amount": 1050,
    "currency": "BRL",
    "payment_method": {
        "type": "br_credit_card",
        "fields": {
            "number": "4111111111111111",
            "expiration_month": "12",
            "expiration_year": "25",
            "cvv": "789",
            "name": "John Doe"
        }
    }
  }
  ```

- **Respostas**
  - 200 OK
      ```
        {
        "orderId": "e7b8f8e2-8b8d-4d8b-8b8d-4d8b8d8b8d8b",
        "status": "pending"
        }
      ```
    - 400 Bad request
        - Quando o OrderId não for um UUID
      ```
        {
         "error": "Invalid orderId format"
        }
        ```


### Busca todos os checkouts feitos

- **Endpoint:** `GET /{consumerId}`
- **Descrição:** Processa o pagamento e cria um pedido.
- **Resposta esperada (JSON):**
  ```json
  {
    "orders" : [{
      "orderId" : "53e2d452-7337-4569-9c7b-75ef99c3820d",
      "items": [
      {
        "itemId": 1,
        "qnt": 4
      }
    ],
    "paymentType" : "br_credit_card",
    "value" : 1050,
    "paymentStatus" : "approved"
    }]
  }
  ```

### Busca por pedido

- **Endpoint:** `GET /by-order-id/{orderId}`
- **Descrição:** Processa o pagamento e cria um pedido.
- **Resposta esperada (JSON):**
  ```json
  {
      "orderId" : "53e2d452-7337-4569-9c7b-75ef99c3820d",
      "items": [
      {
        "itemId": 1,
        "qnt": 4
      }
    ],
    "paymentType" : "br_credit_card",
    "value" : 1050,
    "paymentStatus" : "declined"
    }
  ```
## Validações e Erros
### Pagamento
- Invalid payment information: As informações de pagamento devem ser válidas e completas. Caso contrário, retorna erro 400.
- Payment processing failed: Caso a payment-api não consiga processar o pagamento, retorna erro 500.
### Carrinho
- Empty cart: O carrinho do consumidor não pode estar vazio. Caso contrário, retorna erro 400.
- Invalid consumerId format: O consumerId deve ser um UUID válido. Caso contrário, retorna erro 400

## Payment API (EXTERNA) - Informações importantes

### Gerar ApiKey

- **Endpoint:** `POST https://payment-api-latest.onrender.com/create-group`
- **Descrição:** Cria uma ApiKey para o seu grupo. Essa apiKey deverá ser usada em todas as requisições de pagamento. Caso não seja fornecida apiKey a resposta será um HttpStatus 401.
- **Corpo da Requisição (JSON):**
````json
{
	"groupName":"Grupo11",
	"studentNames":["José Almeida","Maria da Silva"] 
}
````

- **Resposta esperada:**

```json
{
	"apiKey": "777396e205b7881490af58e82df453333673428889284694abab7dd9"
}
```

### Solicitar processamento de pagamento

- **Endpoint:** `POST https://payment-api-latest.onrender.com/create-payment`
- **Descrição:** Cria uma ApiKey para o seu grupo. Essa apiKey deverá ser usada em todas as requisições de pagamento. Caso não seja fornecida apiKey a resposta será um HttpStatus 401.


- **Observação sobre API:** 
  - Caso o já exista um **orderId** na Api de pagamentos ela retornará o status já salvo.
  - Essa API usa CloudFunction, isso indica que o sistema pode demorar para responder. Atente-se que sua aplicação deve ter tempo de resposta inferior a 500ms. Faça o manejo necessário na sua aplicação para assegurar esse requisito. 


- **Corpo da Requisição (JSON):**

- **Header:**
```json
{
  "apiKey": "777396e205b7881490af58e82df453333673428889284694abab7dd9"
}
```

- **Body:**
````json
{
  "orderId": "98b251ab-e4f1-487e-a26b-fa8fdbe356ac",
  "amount": 1050,
  "currency": "BRL",
  "payment_method": {
    "type": "br_credit_card",
    "fields": {
      "number": "4111111111111111",
      "expiration_month": "12",
      "expiration_year": "25",
      "cvv": "789",
      "name": "John Doe"
    }
  }
}
````

- **Resposta esperada**:

```json
{
  "orderId": "98b251ab-e4f1-487e-a26b-fa8fdbe356ac",
  "status": "declined"
}
```
# Gateway Service

Esta aplicação é responsável por gerenciar e rotear as requisições para os serviços apropriados dentro do sistema de e-commerce. Ele atua como um ponto central de entrada para as requisições vindas do BFF, garantindo que cada solicitação seja encaminhada corretamente para o serviço correspondente.

## Responsabilidades

- **Roteamento de Requisições:** Direciona as requisições para os serviços apropriados com base no padrão do endpoint.
- **Segurança:** Restringe o acesso a determinados endpoints para aplicações internas.
- **Integração com API Externa:** Interage com serviços externos e manipula as respostas conforme necessário.

## Requisições

### /cart**

As requisições com o padrão `/cart**` devem ser repassadas para o serviço de carrinho de compras (`cart`).

- **Exemplo de Endpoint:** `POST /cart/items`
- **Serviço Destino:** `cart`

#### Acesso Restrito

O endpoint `DELETE /` deve ter acesso restrito a aplicações internas.

- **Exemplo de Endpoint:** `DELETE /cart`
- **Acesso:** Restrito a aplicações internas, portanto deve retornar http status 404.

### /checkout**

As requisições com o padrão `/checkout**` devem ser direcionadas para o serviço de checkout (`checkout`).

- **Exemplo de Endpoint:** `POST /checkout`
- **Serviço Destino:** `checkout`

### /products**

As requisições com o padrão `/products` devem ser repassadas para a API externa `https://fakestoreapi.com`.

- **Exemplo de Endpoint:** `GET /products`
- **Serviço Destino:** `https://fakestoreapi.com` (valide a documentação no site indicado)

#### Manipulação de Respostas

- Quando a resposta da API externa for `200 OK` mas o corpo da resposta estiver vazio, a informação repassada deve ser `400 Id não localizado`.

3. O tempo de resposta máximo dos endpoints não pode ultrapassar 500ms.

 

3.**Arquitetura Clean:** Aplique os conceitos de Clean Architecture para garantir que cada módulo tenha uma separação clara entre as camadas de apresentação, aplicação, domínio e infraestrutura.

4.**Documentação e Testes:** Inclua documentação básica sobre como executar e testar cada módulo. Implemente testes unitários e de integração para garantir que o sistema funcione corretamente.

5**CI:** Utilizaremos Github Actions para validar a cobertura de testes e a funcionalidade dos endpoints. **Não será permitida nenhuma alteração na pasta .github!!!**

6**Armazenamento de Dados:** Você poderá escolher qual tecnologia de banco de dados que utilizará, sem qualquer restrição. No entanto, é importante lembrar que nessa etapa seram necessarias alterações no [`docker-compose.yml`](docker-compose.yml) para que seu ecossistema continue completamente funcional. Entregas sem banco dados, ou que não consigam ser executas completamente via `docker-compose up` e via Github Action serão penalizadas.



## Entregáveis

1. PR aberto para esse repositório com todos serviços implementados, conforme os requisitos gerais e específicos (ver item 1 de Requisitos gerais), e funcionando adequadamente. Devidamente documentado e com  cobertura de teste requerida é de 80%. 
2. Um vídeo em qualquer plataforma de streaming (ex: Youtube), com acesso público ou via link, demonstrando todas as funcionalidades.
3. Um arquivo pdf com um texto avaliando a solução arquitetural proposta nesse desafio. O texto deve ser endereçado a Equipe de Arquitetura da ADJT Company e deve avaliar, dissertando criticas e sugestões, com base conceitos que estudamos durante o Pós. Se acharem necessário podem adicionar um diagrama ilustrando a solução proposta por você.

