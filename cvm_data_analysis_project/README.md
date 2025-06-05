# Análise de Dados Financeiros da CVM

## Introdução e Objetivos

Este projeto demonstra um fluxo de trabalho completo para coleta, processamento e análise de dados financeiros de companhias abertas brasileiras, utilizando informações públicas disponibilizadas pela Comissão de Valores Mobiliários (CVM). O objetivo principal é extrair, tratar e consolidar as Demonstrações Financeiras Padronizadas (DFPs) de diversas empresas ao longo de vários anos, preparando os dados para futuras análises, como cálculo de indicadores financeiros, valuation ou estudos setoriais.

O código apresentado neste repositório visa não apenas cumprir a tarefa de manipulação dos dados, mas também servir como um portfólio prático, evidenciando competências em:

*   **Coleta de Dados:** Automatização do download de arquivos ZIP diretamente do portal de dados abertos da CVM.
*   **Processamento de Dados em Larga Escala:** Leitura e concatenação eficiente de múltiplos arquivos CSV contidos nos ZIPs, utilizando bibliotecas como Pandas e Dask para lidar com o volume de dados.
*   **Limpeza e Transformação de Dados:** Tratamento de inconsistências, valores ausentes e reestruturação das informações para um formato analítico.
*   **Engenharia de Atributos:** Criação de novas colunas e indicadores a partir dos dados brutos (ex: separação de tipo de demonstração).
*   **Boas Práticas de Código:** Organização, clareza e (potencialmente, após refatoração sugerida) modularidade do código Python em um ambiente Jupyter Notebook.

Este projeto é ideal para recrutadores que buscam profissionais com experiência prática na manipulação de dados financeiros complexos e na aplicação de técnicas de ciência de dados no contexto do mercado de capitais brasileiro.



## Fonte dos Dados e Coleta

Os dados utilizados neste projeto são as Demonstrações Financeiras Padronizadas (DFP) de companhias abertas, disponibilizados publicamente pela CVM em seu [Portal de Dados Abertos](https://dados.cvm.gov.br/). Especificamente, são utilizados os arquivos anuais consolidados no formato ZIP, que contêm múltiplos arquivos CSV com as informações detalhadas das demonstrações (Balanço Patrimonial, Demonstração do Resultado, Fluxo de Caixa, etc.).

O processo de coleta é automatizado no notebook:

1.  Define-se um intervalo de anos de interesse (ex: 2010 a 2025).
2.  Um loop itera sobre cada ano, construindo a URL específica para o arquivo ZIP correspondente.
3.  A biblioteca `requests` é utilizada para fazer o download do conteúdo de cada arquivo ZIP.
4.  Os arquivos ZIP baixados são salvos localmente em um diretório especificado (originalmente `dados_cvm/`, mas recomenda-se parametrizar ou instruir a criação deste diretório no README).

Esta etapa demonstra a capacidade de interagir com fontes de dados externas via API/HTTP e automatizar tarefas de download.



## Metodologia

O núcleo do projeto reside no processamento e consolidação dos dados baixados. A metodologia empregada no notebook envolve os seguintes passos:

1.  **Leitura dos Dados:**
    *   Iteração sobre os arquivos ZIP baixados no diretório de dados.
    *   Para cada ZIP, extração e leitura dos arquivos CSV internos (planilhas das demonstrações) utilizando `zipfile` e `pandas.read_csv`.
    *   Atenção especial à codificação (`encoding='ISO-8859-1'`) e ao tratamento de linhas mal formatadas (`on_bad_lines='skip'`).
    *   Otimização da leitura especificando tipos de dados (`dtype={'ORDEM_EXERC': 'category'}`).
    *   *Oportunidade de Melhoria:* O código original lia todos os CSVs para uma lista e depois concatenava. Uma versão posterior (comentada ou implementada no notebook) utiliza `dask.dataframe` para lidar com a concatenação de forma mais eficiente em termos de memória, especialmente para volumes de dados maiores. Isso demonstra conhecimento em ferramentas para processamento de big data (ou dados que não cabem inteiramente na memória RAM).

2.  **Consolidação:**
    *   Concatenação de todos os DataFrames lidos em um único DataFrame `base_dados`.
    *   Liberação de memória após a concatenação (ex: `del lista_demonstracao`, `gc.collect()`).

3.  **Limpeza e Transformação:**
    *   **Separação de Informações:** Extração das colunas `con_ind` (Consolidado/Individual) e `tipo_dem` (Tipo de Demonstração) a partir da coluna `GRUPO_DFP` utilizando `str.split`.
    *   **Remoção de Ruídos:** Filtragem para remover dados do "PENÚLTIMO" exercício (`ORDEM_EXERC`), mantendo apenas o "ÚLTIMO".
    *   **Tratamento de Nulos:** Remoção de linhas onde o valor da conta (`VL_CONTA`) é nulo, pois são essenciais para a análise.

4.  **Preparação para Análise/Banco de Dados:**
    *   O DataFrame resultante (`base_dados`) contém os dados financeiros de múltiplas empresas e anos, prontos para serem carregados em um banco de dados ou utilizados para análises subsequentes.
    *   *Oportunidade de Melhoria:* O notebook original foca na preparação. Para destacar ainda mais as habilidades, poderiam ser adicionadas etapas como:
        *   Cálculo de indicadores financeiros básicos (Liquidez, Rentabilidade, Endividamento).
        *   Visualizações de dados (evolução de contas, comparação entre empresas).
        *   Implementação da carga dos dados em um banco de dados (SQLAlchemy, SQLite, etc.).

Esta seção demonstra a aplicação prática de técnicas de ETL (Extract, Transform, Load) e manipulação de dados com Pandas e, potencialmente, Dask.



## Como Executar

Para reproduzir este projeto em seu ambiente local, siga os passos abaixo:

1.  **Clone o Repositório:**
    ```bash
    git clone <URL_DO_SEU_REPOSITORIO_AQUI>
    cd cvm_data_analysis_project
    ```

2.  **Crie um Ambiente Virtual (Recomendado):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # Linux/macOS
    # ou
    .\venv\Scripts\activate  # Windows
    ```

3.  **Instale as Dependências:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Crie o Diretório de Dados:**
    O notebook espera que os arquivos ZIP da CVM sejam baixados em um diretório específico. Crie este diretório na raiz do projeto (ou ajuste o caminho no código do notebook):
    ```bash
    mkdir dados_cvm
    ```
    *Observação:* O notebook irá baixar os arquivos automaticamente para este diretório na primeira execução da célula correspondente.

5.  **Execute o Notebook:**
    Inicie o Jupyter Lab ou Jupyter Notebook e abra o arquivo `notebooks/VALUATION2.ipynb`.
    ```bash
    jupyter lab
    # ou
    jupyter notebook
    ```
    Execute as células do notebook sequencialmente. A etapa de download e processamento dos dados pode levar algum tempo, dependendo da sua conexão com a internet e da capacidade do seu computador, especialmente se estiver processando muitos anos de dados.

## Habilidades de Ciência de Dados Demonstradas

Este projeto serve como uma demonstração prática das seguintes habilidades essenciais em Ciência de Dados e Engenharia de Dados:

*   **Web Scraping/Coleta de Dados:** Utilização de `requests` para baixar arquivos sistematicamente de uma fonte web (portal da CVM).
*   **Manipulação de Dados:** Uso extensivo da biblioteca `pandas` para leitura, limpeza, transformação, filtragem e agregação de dados tabulares.
*   **Processamento de Dados em Larga Escala:** Aplicação (ou sugestão de aplicação) de `dask` para lidar com datasets que podem exceder a memória RAM disponível, demonstrando consciência sobre escalabilidade.
*   **Engenharia de Atributos:** Criação de novas variáveis significativas (`con_ind`, `tipo_dem`) a partir de dados existentes.
*   **Gerenciamento de Dependências:** Utilização de `requirements.txt` para garantir a reprodutibilidade do ambiente.
*   **Estruturação de Projetos:** Organização do código e artefatos em uma estrutura de diretórios lógica.
*   **Automação:** Scripting de tarefas repetitivas (download, leitura de múltiplos arquivos).
*   **Conhecimento de Domínio (Finanças):** Compreensão da estrutura e do significado dos dados das Demonstrações Financeiras Padronizadas da CVM.



## Estrutura do Repositório

O projeto está organizado da seguinte forma:

```
/cvm_data_analysis_project
|-- /dados_cvm/             # Diretório para armazenar os arquivos ZIP baixados da CVM (criado manualmente ou pelo script)
|-- /docs/                  # (Opcional) Documentação adicional, relatórios, etc.
|-- /notebooks/
|   |-- VALUATION2.ipynb    # Notebook Jupyter principal com o código de coleta e processamento
|-- /scripts/               # (Opcional) Scripts Python auxiliares (ex: funções modularizadas)
|-- .gitignore              # Arquivo para especificar arquivos não rastreados pelo Git
|-- README.md               # Este arquivo, com a descrição do projeto
|-- requirements.txt        # Lista de dependências Python do projeto
|-- venv/                   # (Opcional) Diretório do ambiente virtual Python
```

## Contato e Licença

*   **Autor:** [Seu Nome ou Usuário GitHub]
*   **Contato:** [Seu Email ou Link do LinkedIn (opcional)]
*   **Licença:** [Escolha uma licença, ex: MIT, Apache 2.0, ou deixe em branco se não aplicável]

---

*Este README foi gerado e estruturado com auxílio de IA para garantir clareza e profissionalismo.*
