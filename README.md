# Ponderada Programação - MVC

Link para vizualização do [Diagrama MVC](https://drive.google.com/file/d/1pvLNwJUjeK7nTDSjc4CFsrHDL5wQitQN/view?usp=drive_link).

# Arquitetura MVC - DIVE

## Descrição
Este projeto implementa a Arquitetura MVC (Model-View-Controller) para a aplicação web DIVE (Dell Interactive Virtual Environment), que serve como uma plataforma de treinamento interativo para os funcionários da Dell.

**Ferramenta de Diagramação:**
- Diagramas criados com [draw.io](https://draw.io).

# Models
## Entidades:

- `User`: 
Representa os usuários da plataforma.
    - **Atributos:**
        - **id:** Identificador único para cada usuário, utilizado como chave primária.
        - **nome:** Nome completo do usuário.
        - **email:** Endereço de e-mail do usuário, usado para login e comunicação.
        - **senha:** Senha do usuário para autenticação. 
        - **linha_de_producao:** Identificação da linha de produção na qual o funcionário trabalha.
        - **funcao:** Função do usuário dentro da empresa.

- `Manual`: 
Representa um _card_ de manual de treinamento na plataforma.
  - **Atributos:**
    - **id:** Identificador único para cada manual, utilizado com chave primária.
    - **nome:** Título do manual.
    - **conteudo:** Conteúdo do Manual, podendo ser arquivos ou links.
    - **descricao:** Resumo do manual, ou da alteração presente em uma atualização
    - **confirmacao_de_leitura:** Indicador de se o manual foi lido pelo usuário.

- **Relações:** 
**User X Manual**
- Um `User` pode ter acesso a vários `Manuais`, para distribuir o treinamento.
- Um `Manual` pode ser atribuído a vários `Users`, para ajudar na distribuição do treinamento.
- Relação de muitos para muitos, n:n.

# Controlller

## `Funcionário Dashboard`
Gerencia o painel dos funcionários, permitindo a visualização e interação com os cards de manuais.

- **Ações (Métodos):**
  - `visualizarManual`:
    - **Entrada:** ID do manual a ser visualizado.
    - **Saída:** Exibe o conteúdo do manual selecionado.
    - **Interação:** Consulta o `Manual Model` para obter os dados e os renderiza na `Dashboard Funcionário View`.
  - `confirmarLeitura`:
    - **Entrada:** ID do manual cuja leitura foi concluída pelo funcionário.
    - **Saída:** Atualiza o status de leitura do manual.
    - **Interação:** Atualiza a relação de leitura no `Manual Model` e reflete a confirmação na `Dashboard Funcionário View`.
  - `pesquisarManuais`:
    - **Entrada:** Termos de pesquisa inseridos pelo funcionário.
    - **Saída:** Lista de manuais correspondentes à pesquisa.
    - **Interação:** Busca no `Manual Model` usando os termos fornecidos e exibe os resultados na `Dashboard Funcionário View`.

## `Admin Dashboard`
Gerencia as operações efetuadas por administradores no DIVE, como adição e edição de manuais, e controle de funcionários e estatísticas.

- **Ações (Métodos):**
  - `editarManual`:
    - **Entrada:** ID do manual e os novos dados para atualização em algum dos atributos do `Manual Model`.
    - **Saída:** Manual atualizado com as novas informações.
    - **Interação:** Modifica os dados do manual no `Manual Model` e atualiza a `Dashboard Admin`.
  - `deletarManual`:
    - **Entrada:** ID do manual a ser excluído.
    - **Saída:** Confirmação da exclusão do manual.
    - **Interação:** Remove o manual do `Manual Model` e atualiza a `Dashboard Admin`.
  - `adicionarManual`:
    - **Entrada:** Dados do novo manual a ser criado.
    - **Saída:** Criação do novo manual com atribuição de ID.
    - **Interação:** Insere um novo manual no `Manual Model` e atualiza a `Dashboard Admin`.
  - `visualizarFuncionarios`:
    - **Entrada:** Buscar funcionários específicos.
    - **Saída:** Lista de funcionários.
    - **Interação:** Consulta o `User Model` e exibe os dados na `Dashboard Admin`.
  - `visualizarEstatisticas`:
    - **Entrada:** Nenhuma entrada necessária, ou parâmetros de filtragem por data/período.
    - **Saída:** Dados estatísticos relevantes.
    - **Interação:** Agrega os dados feitos no  `User Model` e `Manual Model` para exibir estatísticas.

## `Autenticação`
Gerencia o processo de autenticação de usuários na plataforma.

- **Ações (Métodos):**
  - `identificarUsuario`:
    - **Entrada:** Credenciais fornecidas pelo usuário (geralmente email e senha).
    - **Saída:** Validação do usuário e progressão para o painel adequado.
    - **Interação:** Verifica as credenciais contra o `User Model` e direciona para a `Dashboard Funcionário View` ou `Dashboard Admin View` conforme o tipo de usuário.
  - `autorizarAcesso`:
    - **Entrada:** utenticação do usuário.
    - **Saída:** Verificação de permissões de acesso.
    - **Interação:** Confirma se o usuário tem permissão para acessar determinadas áreas ou funções da plataforma e permite ou nega o acesso com base nisso.

 Os controladores atuam como intermediários entre os modelos e as views. Eles recebem as entradas dos usuários através das views, processam essas entradas utilizando os modelos, e então direcionam as saídas apropriadas de volta para as views para serem apresentadas. Por exemplo, os controladores User Dashboard e Admin Dashboard interagem com o modelo Manual para atualizar informações e então reflete essas mudanças nas views User Dashboard e Admin Dashboard.

# Views (Views)
- `Login`: Interface para autenticação do usuário.
- `Dashboard Admin`: Painel administrativo para gestão de manuais e visualização de estatísticas.
- `Dashboard User`: Painel do usuário para acesso a manuais atribuídos e confirmação de leitura.

# Infraestrutura
## Banco de Dados PostgreSQL
O PostgreSQL, um sistema de banco de dados relacional, armazena todos os dados da aplicação. Ele armazena as informações dos `Models`, incluindo `Users` e `Manuais`.

### Integração com MVC:
- **Models**:
  - Comunicam-se diretamente com o banco de dados PostgreSQL para persistir e recuperar dados, atuando como a camada de acesso a dados da aplicação.
- **Controllers**:
  - Acessam os `Models` para realizar trasmições no banco de dados.

### Justificativa:
- O PostgreSQL é escolhido por sua robustez, confiabilidade e suporte a recursos avançados de SQL, essencial para manter a integridade dos dados e suportar consultas complexas. Esta escolha traz benefícios significativos em termos de desempenho, segurança e escalabilidade.

## Servidor Render

O Servidor Render é responsável por compilar e apresentar as `Views` ao usuário, processando templates e dados fornecidos pelos `Controllers`.

### Integração com MVC:
- **Views**:
  - São renderizadas no servidor, onde os templates são preenchidos com os dados obtidos dos `Models` através dos `Controllers`.
- **Controllers**:
  - Solicitam ao servidor renderizar as `Views` com os dados necessários, resultando em uma página HTML pronta para ser enviada ao cliente.

### Justificativa:
- A renderização no servidor é escolhida pela eficiência em gerar páginas completas antes de chegar ao cliente, reduzindo a carga de processamento do lado do cliente e melhorando o tempo de carregamento das páginas.

## Implicações da Arquitetura

A adoção da arquitetura MVC traz várias implicações significativas para a aplicação DIVE, influenciando positivamente desde o desenvolvimento até a operação diária.

### Escalabilidade:
- A arquitetura permite que a aplicação gerencie um aumento no número de usuários e na carga de trabalho sem comprometer o desempenho, possibilitando a expansão dos `Models`, `Views`, e `Controllers` de forma independente conforme necessário.

### Manutenção:
- A separação clara entre a lógica do negócio, a interface do usuário e a camada de dados facilita atualizações e alterações, permitindo que os desenvolvedores trabalhem em diferentes partes do sistema sem conflitos.

### Testabilidade:
- Com cada parte da aplicação isolada, é possível implementar testes unitários e de integração de maneira eficiente, melhorando a qualidade do software e reduzindo a probabilidade de bugs.

### Segurança:
- Os `Controllers` funcionam como uma camada de proteção, onde a autenticação e autorização são gerenciadas antes de permitir o acesso aos dados, reforçando a segurança de informações.

---------------------------------------

Recursos Adicionais:
Documentação do Sails.js: https://github.com/balderdashy/sails Tutorial do draw.io: https://m.youtube.com/watch?v=w3zm-wbmlpc Exemplos de diagramas MVC: https://www.lucidchart.com/pages/templates
