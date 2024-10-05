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

   ## Cart Service:
   # Aplicação de Gerenciamento de Carrinho de Compras

Esta aplicação fornece um serviço de gerenciamento de carrinho de compras para consumidores em um ambiente de e-commerce. O serviço permite que os usuários interajam com seus carrinhos de compras de forma eficiente e prática através de uma API RESTful. As principais funcionalidades incluem:

- **Adicionar Itens ao Carrinho:** Permite que os consumidores adicionem itens ao seu carrinho, especificando a quantidade desejada.
- **Remover Itens do Carrinho:** Oferece a capacidade de remover itens específicos do carrinho.
- **Limpar o Carrinho:** Facilita a limpeza total do carrinho quando o checkout é finalizado.

A aplicação é projetada para lidar com a manipulação de itens em carrinhos de compras de forma robusta, garantindo que as operações sejam realizadas com dados válidos e retornando mensagens de erro claras quando necessário.

A aplicação visa melhorar a experiência do usuário e a eficiência no gerenciamento de carrinhos de compras em plataformas de e-commerce.

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

3. O tempo de resposta máximo dos endpoints não pode ultrapassar 500ms.

3.**Arquitetura Clean:** Aplique os conceitos de Clean Architecture para garantir que cada módulo tenha uma separação clara entre as camadas de apresentação, aplicação, domínio e infraestrutura.

4.**Documentação e Testes:** Inclua documentação básica sobre como executar e testar cada módulo. Implemente testes unitários e de integração para garantir que o sistema funcione corretamente.

5**CI:** Utilizaremos Github Actions para validar a cobertura de testes e a funcionalidade dos endpoints. **Não será permitida nenhuma alteração na pasta .github!!!**

6**Armazenamento de Dados:** Você poderá escolher qual tecnologia de banco de dados que utilizará, sem qualquer restrição. No entanto, é importante lembrar que nessa etapa seram necessarias alterações no [`docker-compose.yml`](docker-compose.yml) para que seu ecossistema continue completamente funcional. Entregas sem banco dados, ou que não consigam ser executas completamente via `docker-compose up` e via Github Action serão penalizadas.



## Entregáveis

1. PR aberto para esse repositório com todos serviços implementados, conforme os requisitos gerais e específicos (ver item 1 de Requisitos gerais), e funcionando adequadamente. Devidamente documentado e com  cobertura de teste requerida é de 80%. 
2. Um vídeo em qualquer plataforma de streaming (ex: Youtube), com acesso público ou via link, demonstrando todas as funcionalidades.
3. Um arquivo pdf com um texto avaliando a solução arquitetural proposta nesse desafio. O texto deve ser endereçado a Equipe de Arquitetura da ADJT Company e deve avaliar, dissertando criticas e sugestões, com base conceitos que estudamos durante o Pós. Se acharem necessário podem adicionar um diagrama ilustrando a solução proposta por você.

