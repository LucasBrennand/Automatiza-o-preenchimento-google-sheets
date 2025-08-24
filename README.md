# Automatiza-o-preenchimento-google-sheets


# Automação de Lançamento de Vendas via Telegram e Google Sheets

## Descrição

Este projeto automatiza o processo manual de lançamento de vendas diárias. A automação é acionada por uma mensagem de texto com formato padronizado, enviada a um bot do Telegram. A plataforma Make.com processa a mensagem, extrai os dados e os insere de forma organizada em uma Planilha Google, eliminando a necessidade de digitação manual e reduzindo erros.

## Funcionalidades

-   **Lançamento automático de vendas:** Transforma uma mensagem de texto em múltiplas linhas em uma planilha.
-   **Estruturação de dados:** Separa as informações em `Data`, `Quiosque`, `Marca`, `Vendedora` e `Valor`.
-   **Custo Zero:** Utiliza exclusivamente os planos gratuitos do Google Sheets, Telegram e Make.com.
-   **Eficiência:** Reduz o processo diário de lançamento de dados para menos de 10 segundos.

## Pré-requisitos:

1.  Uma **Conta Google** (para o Google Sheets).
2.  Uma conta no **Telegram**.
3.  Uma conta no **Make.com** (antigo Integromat).

---


### 1. Preparar a Planilha Google (Google Sheets)

-   Crie uma nova planilha.
-   Na primeira linha, defina os seguintes cabeçalhos, um em cada coluna:
    -   `Data`
    -   `Quiosque`
    -   `Marca`
    -   `Vendedora`
    -   `Valor`

### 2. Criar o Bot no Telegram

-   Abra o Telegram e procure pelo bot verificado **`BotFather`**.
-   Inicie a conversa e envie o comando `/newbot`.
-   Siga as instruções para definir um nome e um `username` para o seu bot (o `username` precisa terminar em `bot`).
-   O `BotFather` fornecerá um **token de API**. Copie e guarde este token.

### 3. Definir o Formato da Mensagem de Texto

-   Este é o formato que deve ser copiado do WhatsApp e colado no bot do Telegram.
DD/MM/AAAA - [Nome do Quiosque]
[Marca] - [Nome da Vendedora]: R$ [Valor]
[Marca] - [Nome da Vendedora]: R$ [Valor]



### 4. Construir o Cenário no Make.com

-   Faça login no Make.com e crie um novo cenário. Adicione os seguintes módulos na ordem:

    **1. Gatilho: `Telegram Bot > Watch Updates`**
    -   Conecte seu bot usando o **token de API**.
    -   Escolha a opção de rodar **"Immediately as data arrives"**.

    **2. Ação: `Text Parser > Match Pattern`**
    -   **Pattern:**
        ```
        (?<data>\d{2}\/\d{2}\/\d{4})\s-\s(?<quiosque>.+)\n(?<vendas>(?:.|\n)+)
        ```
    -   **Text:** Mapeie a variável `Text` do módulo do Telegram.

    **3. Controle: `Flow Control > Iterator`**
    -   **Array:** Use a fórmula abaixo para separar cada linha de venda.
        ```
        split({{2.vendas}}; newline)
        ```

    **4. Ação: `Text Parser > Match Pattern`**
    -   **Pattern:**
        ```
        (?<marca>.+?)\s-\s(?<nome>.+?):\sR\$\s(?<valor>[\d\.,]+)
        ```
    -   **Text:** Mapeie a variável `Value` que vem do `Iterator`.

    **5. Ação Final: `Google Sheets > Add a Row`**
    -   Conecte sua Conta Google e selecione a planilha correta.
    -   Mapeie as variáveis extraídas para as colunas correspondentes:
        -   **Data:** `{{2.data}}`
        -   **Quiosque:** `{{2.quiosque}}`
        -   **Marca:** `{{4.marca}}`
        -   **Vendedora:** `{{4.nome}}`
        -   **Valor:** `{{4.valor}}`

### 5. Testar e Ativar

1.  Salve o cenário.
2.  Clique em **"Run once"** para iniciar um teste.
3.  Envie uma mensagem de exemplo para o seu bot no Telegram.
4.  Verifique se a linha foi adicionada corretamente na planilha.
5.  Se tudo estiver OK, ative o interruptor do cenário para **ON**.

---

## Modo de Uso (Processo Diário)

1.  Receba a mensagem de vendas no WhatsApp.
2.  **Copie** o texto completo da mensagem.
3.  Abra o Telegram, encontre a conversa com seu bot e **cole** a mensagem.
4.  Envie. A planilha será atualizada em segundos.
