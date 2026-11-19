# Cadastro de Clientes

> Sistema web para cadastrar, listar, alterar e excluir clientes, feito para quem precisa manter uma lista simples de clientes.

![Java](https://img.shields.io/badge/Java-17-orange)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.0-brightgreen)
![MySQL](https://img.shields.io/badge/MySQL-8.0%20Docker-blue)

## Sobre o projeto

- **O que é:** um cadastro de clientes com listagem, inclusão, alteração e exclusão (CRUD). A página web mostra os clientes salvos no banco. Ela também permite incluir um cliente novo, trocar o nome de um cliente na própria linha e excluir. Antes de salvar, o sistema valida o nome: ele é obrigatório, precisa ter pelo menos 3 letras e não pode ter números.
- **Para quem:** pequenos negócios ou pessoas que precisam manter um cadastro básico de clientes, sem instalar nada além de um navegador.
- **Por que foi feito:** trabalho do 2º bimestre de Modelos e Linguagem de Programação III, Multivix Vitória, 2026/2.

## Equipe

| Integrante | Parte desenvolvida | O que fez |
| --- | --- | --- |
| Emily Beatriz Vailante Ferreira | Dados (entidade e repositório) | Criou o projeto Spring Boot, o MySQL no Docker, a entidade `Cliente` e o `ClienteRepository`. |
| Emily Beatriz Vailante Ferreira | Interface | Criou o `ClienteController` e a página `clientes.html` com Thymeleaf para listar, salvar, alterar e excluir. |
| Emily Beatriz Vailante Ferreira | Padrões de projeto | Aplicou o padrão Strategy nas regras de nome e separou o código em camadas com o `ClienteService`. |
| Emily Beatriz Vailante Ferreira | Testes | Escreveu os testes das regras, do serviço (com Mockito) e do repositório (com o MySQL). |

O trabalho foi desenvolvido individualmente. Por isso, as quatro partes são da mesma integrante.

## Temas do bimestre

| Tema | Como foi aplicado | Onde está no código |
| --- | --- | --- |
| Classes de manipulação de dados de BD | A entidade `Cliente` (id e nome) é mapeada com JPA (`@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`). O `ClienteRepository` estende `JpaRepository`, então já entrega `save`, `findById`, `findAll` e `deleteById` sem escrever nenhum método. O Hibernate cria a tabela `cliente` sozinho (`ddl-auto=update`). O MySQL roda no Docker. | `src/main/java/com/example/aula/Cliente.java`, `ClienteRepository.java`, `src/main/resources/application.properties`, `docker-compose.yml` |
| Interface web | O navegador faz a requisição para `/clientes`. O `ClienteController` (Spring MVC) recebe, chama o `ClienteService` e devolve a página `clientes.html`. O Thymeleaf monta a página com os dados (`th:each`, `th:text`). O formulário envia o campo `nome` (`@RequestParam`) e o id vem na URL (`@PathVariable`). Depois de salvar ou excluir, o controller faz `redirect` (padrão Post/Redirect/Get), então apertar F5 não salva de novo. Mensagens de erro voltam para a página no atributo `erro`. | `src/main/java/com/example/aula/ClienteController.java` e `src/main/resources/templates/clientes.html` |
| Padrões de projeto e boas práticas | **Padrão Strategy:** cada regra de validação é uma classe pequena que implementa a interface `RegraDeNome`. As regras são `NomeObrigatorio`, `NomeMinimo` e `NomeSemNumeros`. O Spring entrega ao `ClienteService` todas as classes `@Component` que implementam `RegraDeNome`, e o serviço aplica todas sem saber quais são. **Por quê:** antes, a regra "nome obrigatório" ficava só no HTML (`required`). Ela não valia para uma requisição direta ao servidor e só podia ser testada com o servidor no ar. **O que melhorou:** para criar uma regra nova, basta uma classe nova, e o serviço não muda. As regras são testáveis sem a interface. **Boa prática:** separação em camadas. O controller cuida da tela, o serviço cuida das regras e o repositório cuida do banco. O controller não chama mais o repositório. | `src/main/java/com/example/aula/RegraDeNome.java`, `NomeObrigatorio.java`, `NomeMinimo.java`, `NomeSemNumeros.java`, `ClienteService.java` |
| Teste unitário | Testes com **JUnit 5** e **Mockito**. `RegrasDeNomeTest` testa cada regra sozinha, sem Spring e sem banco: recusa nome vazio, nome com 2 letras e nome com números, e aceita nomes válidos. `ClienteServiceTest` testa o serviço com um repositório simulado (`mock`): salva um cliente novo com o nome sem espaços, não salva quando o nome é inválido (`verify` com `never`) e altera o nome de um cliente existente. `ClienteRepositoryTest` testa o repositório com o MySQL de verdade: salva, lista e exclui. O `@Transactional` desfaz as alterações no fim de cada teste. Para rodar: `./mvnw test`. | `src/test/java/com/example/aula/RegrasDeNomeTest.java`, `ClienteServiceTest.java`, `ClienteRepositoryTest.java` |

## Tecnologias

- Java 17
- Spring Boot e Spring Data JPA (Hibernate)
- MySQL 8.0 (em Docker, com Docker Compose)
- Interface: Spring MVC com Thymeleaf
- JUnit 5 e Mockito para testes
- Maven (`./mvnw`)
- GitHub e GitHub Codespaces

## Estrutura do projeto

```text
repositorio/
├── .devcontainer/
│   └── devcontainer.json        (ambiente do Codespaces: Java 17, Maven e Docker)
├── .gitignore
├── README.md
└── aula/
    ├── docker-compose.yml
    ├── pom.xml
    └── src/
        ├── main/
        │   ├── java/com/example/aula/
        │   │   ├── AulaApplication.java
        │   │   ├── Cliente.java
        │   │   ├── ClienteRepository.java
        │   │   ├── ClienteService.java
        │   │   ├── ClienteController.java
        │   │   ├── RegraDeNome.java
        │   │   ├── NomeObrigatorio.java
        │   │   ├── NomeMinimo.java
        │   │   └── NomeSemNumeros.java
        │   └── resources/
        │       ├── application.properties
        │       └── templates/
        │           └── clientes.html
        └── test/java/com/example/aula/
            ├── RegrasDeNomeTest.java
            ├── ClienteServiceTest.java
            └── ClienteRepositoryTest.java
```

## Como foi realizado

1. **Planejamento:** o trabalho foi feito individualmente. Mesmo assim, seguiu o fluxo do guia: o projeto inicial (tema Dados) foi enviado ao `main`, e cada parte seguinte foi desenvolvida na sua própria branch (`interface`, `padroes`, `testes`) e juntada ao `main` por Pull Request. As partes foram feitas na ordem em que uma depende da outra.
2. **Desenvolvimento:** a entidade escolhida foi `Cliente`, a do guia, por ser simples e cobrir bem os quatro temas. A interface web foi escolhida porque roda no navegador, sem instalar nada. O desenvolvimento foi no GitHub Codespaces, com o MySQL no Docker. Foi incluído um `devcontainer.json` para o Codespace já abrir com Java 17, Maven e Docker, sem configuração manual. As regras de validação saíram do HTML e foram para o serviço, com o padrão Strategy, para valerem em qualquer caminho e poderem ser testadas sem o servidor.
3. **Dificuldades:**
   - Na primeira execução, a aplicação falhou com `Communications link failure`. O MySQL ainda não tinha terminado de subir no Docker. Foi resolvido esperando cerca de 20 segundos depois do `docker compose up -d` antes de rodar a aplicação.
   - Os comandos `./mvnw` davam erro de arquivo não encontrado, porque o terminal abria na raiz do repositório e não na pasta `aula`. Foi resolvido rodando `cd aula` antes.
   - Ao alterar um cliente, o servidor respondeu com erro 400. O campo do formulário estava com um `name` diferente do `@RequestParam`. Foi corrigido usando `name="nome"` nos dois lugares.
   - Depois de criar o `ClienteService`, a aplicação não subia e mostrava `No qualifying bean of type ClienteService`. Faltava a anotação `@Service`. Com ela, o Spring passou a encontrar o serviço.
   - O `ClienteRepositoryTest` falhava quando o banco estava desligado, enquanto os outros testes passavam. Isso ajudou a entender a diferença: os testes das regras e do serviço não dependem do banco (usam `new` e `mock`), e o do repositório depende.
4. **Aprendizados:** mapear uma tabela com JPA e usar o repositório sem escrever SQL. Ver no terminal o SQL que o Hibernate gera (`show-sql`). Ligar controller e página com Spring MVC e Thymeleaf, e usar o Post/Redirect/Get para não salvar duas vezes. Separar o código em camadas e entender por que regra de negócio não deve ficar só na tela. Usar o Strategy para deixar as regras trocáveis sem mexer no serviço. Testar com e sem banco, usando mocks para isolar a lógica.

## Como executar

Ambiente: GitHub Codespaces. O repositório tem um `devcontainer.json`: ao criar o Codespace, ele já instala Java 17, Maven e Docker e gera o Maven Wrapper (`mvnw`). A primeira abertura leva alguns minutos.

No terminal, dentro da pasta `aula`:

```bash
# 0. Entrar na pasta do projeto (o terminal abre na raiz do repositório)
cd aula

# 1. Subir o banco de dados (espere cerca de 20 segundos)
docker compose up -d

# 2. Rodar os testes
./mvnw test

# 3. Executar a aplicação
./mvnw spring-boot:run
```

Se o arquivo `mvnw` não existir, gere com `mvn -N wrapper:wrapper` dentro da pasta `aula`.

Quando aparecer `Started AulaApplication`, abra a aba **PORTS**, porta 8080, e acesse `/clientes` no navegador.

Para conferir as validações:

- Nome em branco: aparece "Informe o nome do cliente.".
- `Al`: aparece "O nome precisa ter pelo menos 3 letras.".
- `Ana2`: aparece "O nome não pode ter números.".
- `Ana` e depois alterar para `Ana Paula`: funciona.

## Demonstração

![Tela principal](aula/docs/tela-principal.png)

![Mensagem de validação](aula/docs/tela-erro.png)

## Próximos passos

- Novos campos para o cliente (e-mail, telefone, CPF), com regras próprias.
- Confirmação antes de excluir.
- Busca e paginação na lista de clientes.
- Autenticação para acessar o cadastro.

## Autores

Emily Beatriz Vailante Ferreira (trabalho individual), turma SIN05NA, Multivix Vitória, 2026/2. Professora: Juliana Rodrigues de Lima Meirelles.
