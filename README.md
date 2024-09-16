# Projeto de Integração Apache NiFi com Neo4j

Este projeto utiliza o **Apache NiFi** para processar e integrar dados de arquivos CSV e JSON com um banco de dados Neo4j. Ele inclui fluxos de processamento de dados para inserção de informações de aplicativos e suas avaliações no Neo4j usando queries Cypher.

## Pré-requisitos

1. **Apache Maven**: Para compilar o projeto, é necessário ter o Maven instalado.
   - [Guia de Instalação do Maven](https://maven.apache.org/install.html)

2. **Apache NiFi**: Você precisará da versão mais recente do código-fonte do Apache NiFi.
   - Baixe a última versão do NiFi no site oficial: [Apache NiFi](https://nifi.apache.org/download.html)

3. **Neo4j**: Certifique-se de ter um banco de dados Neo4j rodando. Você pode instalar o Neo4j localmente ou utilizar um servidor remoto.

4. **Conexão com o Neo4j**: O fluxo de NiFi se conecta ao Neo4j via protocolo `bolt://`. Certifique-se de ter as credenciais corretas e a URL do servidor configuradas no serviço de cliente Neo4j do NiFi.

## Passos para Compilação

Após ter os pré-requisitos configurados, siga os passos abaixo para compilar e rodar o projeto:

1. Clone o repositório do NiFi:
   ```bash
   git clone https://github.com/apache/nifi.git
   ```

2. Navegue até o diretório clonado:
   ```bash
   cd nifi
   ```

3. Compile o projeto com o perfil de inclusão de suporte a grafos:
   ```bash
   mvn clean install -P include-graph
   ```

Este comando irá compilar o Apache NiFi e incluir o suporte a processadores de integração com **Neo4j**.

## Fluxos de Processamento

### 1. **Ingestão de Dados de Aplicativos**

- **Arquivo de entrada**: `googleplaystore.csv` (contendo informações sobre aplicativos)
- **Processo**:
  - O arquivo CSV é lido e dividido em blocos de 50 registros.
  - Cada bloco é transformado em um grafo, e os dados são enviados para o Neo4j.
  - Relacionamentos de aplicativos com suas categorias e gêneros são criados no banco de dados.

### 2. **Ingestão de Avaliações de Aplicativos**

- **Arquivo de entrada**: `googleplaystore_user_reviews.csv` (contendo avaliações de usuários)
- **Processo**:
  - O arquivo CSV é lido, e as avaliações são mapeadas para seus respectivos aplicativos no Neo4j.
  - A média das avaliações é calculada, e o relacionamento entre `App` e `Review` é criado.

### 3. **Relatório de Consultas Cypher**

- O fluxo também gera relatórios de consultas Cypher que extraem informações como o número de avaliações e a polaridade média das avaliações para cada aplicativo.

## Configuração dos Processadores

- **Neo4j Client Service**: Configurado para se conectar ao banco de dados Neo4j via `bolt://`. O serviço deve ser configurado com:
  - URL do Neo4j (`neo4j-connection-url`)
  - Nome de usuário (`neo4j-username`)
  - Senha (`neo4j-password`)

- **PutFile**: O processador salva os relatórios gerados como arquivos JSON.

## Como Rodar

1. Inicie o Apache NiFi.
2. Importe o fluxo de dados `Neo4j_Integration.json` para o painel do NiFi.
3. Configure os serviços de controlador, incluindo o **Neo4JCypherClientService**, com as credenciais e o URL corretos do seu banco de dados Neo4j.
4. Inicie os processadores e monitore os fluxos de dados.

## Estrutura do Projeto

- **GetFile**: Lê arquivos CSV contendo dados dos aplicativos e suas avaliações.
- **SplitRecord**: Divide os arquivos CSV em blocos para processamento eficiente.
- **EvaluateJsonPath**: Avalia os registros JSON extraídos e os transforma em atributos para o fluxo.
- **Neo4j Data Ingestion**: Insere dados no Neo4j, criando nós para `App`, `Category`, `Review`, e seus respectivos relacionamentos.

## Licença

Este projeto é distribuído sob a licença Apache 2.0.