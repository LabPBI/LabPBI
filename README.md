# Power BI — Laboratório Prático

>**Fonte de dados:** Azure Databricks. **Ferramenta:** Power BI Desktop + Power BI Service.

Esse laboratório tem como objetivo ensinar o fluxo completo de criação de um relatório no Power BI, desde a conexão com o Databricks, passando pela transformação dos dados, criação de medidas DAX, construção dos visuais e publicação no Power BI Service.

Você irá trabalhar com um dataset financeiro fictício, contendo informações de vendas, lucro, unidades vendidas, entre outros, segmentados por país, produto e tempo.

## 🧠 Visão geral do fluxo

> Boas práticas: siga sempre esta ordem. Não pule etapas.

1. **Connect** — conectar ao Azure Databricks
2. **Transform** — limpar e tipar os dados (Power Query)
3. **Measure** — criar medidas DAX
4. **Visualize** — montar a report page
5. **Publish** — publicar no Power BI Service

📅 **Divisão do lab:**
- **Dia 1:** Connect → Transform → Measure
- **Dia 2:** Visualize → Publish

---

# 📅 DIA 1 — Connect, Transform & Measure

## 🔌 Connect (Azure Databricks)

### Pré-requisitos

Antes de começar, tenha em mãos:

- **Workspace URL** do Databricks (ex.: `adb-1234567890.12.azuredatabricks.net`)
- **HTTP Path** do **SQL Warehouse** ou cluster
- Conta com permissão de leitura na tabela
- Autenticação: **Azure AD** (recomendado) ou **Personal Access Token (PAT)**

> 💡 No Databricks, encontre o HTTP Path em **SQL Warehouses → seu warehouse → Connection details**.

### Passo 1 — Abrir o Power BI Desktop

1. Abra o **Power BI Desktop**.
2. Se aparecer a tela de boas-vindas, clique em **Blank report** (canto inferior direito) ou feche o pop-up com o **X**.
3. Você verá a área principal com 3 ícones na barra lateral esquerda: **Report**, **Data** e **Model**.

### Passo 2 — Iniciar a conexão

1. Na faixa superior, clique em **Home**.
2. Clique em **Get Data** → **More...** (na parte inferior da lista).
3. Na janela que abrir, no campo de busca, digite **`Databricks`**.
4. Selecione **Azure Databricks** e clique em **Connect**.

### Passo 3 — Informar os dados de conexão

1. Cole a **URL do workspace** no campo *Server Hostname*.
2. Cole o **HTTP Path** no campo correspondente.
3. Em **Data Connectivity mode**, selecione **Import**.
   - ✅ Use **Import** para este lab (melhor performance).
   - ⚠️ Use **DirectQuery** apenas se precisar de dados em tempo real.
4. Clique em **OK**.

### Passo 4 — Autenticar

1. No painel à esquerda, escolha **Azure Active Directory**.
2. Clique em **Sign in** e complete o login com sua conta organizacional.
3. Após o login, clique em **Connect**.

### Passo 5 — Selecionar a tabela

1. O **Navigator** será aberto, mostrando catálogos e schemas do Databricks.
2. Expanda os nós até encontrar a tabela do **dataset financeiro**.
3. Marque o checkbox ao lado do nome da tabela.
4. Confira o **preview** que aparece à direita.
5. Clique em **Transform Data** 

---

## 🔧 Transform (Power Query)

Você está no **Power Query Editor**. Aqui acontece a maior parte da qualidade do relatório.

> 💡 No painel direito **Query Settings**, cada transformação aparece como um **Applied Step**. Você pode voltar atrás a qualquer momento.

### Passo 1 — Ajustar os tipos de dados

Para cada coluna abaixo:

1. Clique com botão direito no cabeçalho da coluna → **Change Type** → escolha o tipo correto (ex.: Date, Whole Number, Decimal Number, Text).
2. Selecione o tipo correto da tabela:

| Coluna | Tipo |
|---|---|
| Data | Date |
| Unidades Vendidas, Número do Mês, Ano | Whole Number |
| Vendas, Lucro, CMV, Vendas Brutas, Descontos, Preço de Venda, Preço de Fabricação | Decimal Number |
| País, Produto, Segmento, Faixa de Desconto, Nome do Mês | Text |

3. Se aparecer o aviso **Change Column Type**, escolha **Replace current**.

> 💡 **Regra de ouro:** *"Types drive visuals"*. Tipo errado = visual errado.

### Passo 2 — Limpar os dados

**2.1 Remover espaços extras (Trim)**

1. Clique no cabeçalho da coluna **País**.
2. Vá em **Transform → Format → Trim**.
3. Repita para a coluna **Produto**.

**2.2 Remover linhas em branco**

1. Vá em **Home → Remove Rows → Remove Blank Rows**.

**2.3 Substituir hífens por vazio em colunas numéricas (se houver)**

1. Selecione a coluna numérica.
2. Vá em **Transform → Replace Values**.
3. Em *Value to Find* digite `-` e deixe *Replace With* **vazio**.
4. Clique em **OK**.
5. Repita para cada coluna numérica que contenha `-` representando zero/nulo.

### Passo 3 — Renomear coluna

1. Clique duas vezes no cabeçalho `Nome do Mês`.
2. Digite **Mês** e pressione **Enter**.

### Passo 4 - Criar custom column (opcional)
1. Vá em **Add Column → Custom Column**.
2. Dê o nome **Lucro Unitário**.
3. Na caixa de fórmula, digite: `[Lucro] / [Unidades Vendidas]`.
4. Clique em **OK**.
5. Ajuste o tipo da nova coluna para **Decimal Number**.

### Passo 5 — Aplicar e fechar

1. Vá em **Home → Close & Apply**.
2. Aguarde o Power BI carregar os dados (pode levar alguns segundos).
3. Você voltará ao **Power BI Desktop** e a tabela `Financeiro` aparecerá no painel **Data** à direita.

> 🟢 **Boa prática:** toda transformação acontece no Power Query, **nunca no visual**.

### Passo 6 - Apagar coluna
1. Clique com o botão direito no cabeçalho da coluna **CMV**.
2. Selecione **Remove**.

### Passo 7 - Desfazer transformação (se necessário)
1. No painel **Query Settings** (direita), localize a seção **Applied Steps**.
2. Clique no ícone de **X** ao lado da etapa que deseja desfazer (ex.: `Removed Columns` para trazer de volta a coluna CMV).

### Passo 8 - Fechar e aplicar a transformação

1. Vá em **Home → Close & Apply**.
2. Aguarde o Power BI carregar os dados (pode levar alguns segundos).
3. Você voltará ao **Power BI Desktop** e a tabela `Financeiro` aparecerá no painel **Data** à direita.

> 🟢 **Boa prática:** toda transformação acontece no Power Query, **nunca no visual**.

---

## 📌 Measures (DAX)

Crie as medidas DAX para os KPIs antes de montar os visuais. Medidas são cálculos dinâmicos que se adaptam aos filtros do relatório.

### Passo 1 — Criar a primeira medida

1. Confirme que você está no **Report view** (primeiro ícone na barra lateral esquerda).
2. No painel **Data** (direita), clique com o botão direito sobre a tabela `Financeiro`.
3. Escolha **New measure**.
4. Na **barra de fórmula** que aparece no topo, apague o texto e cole:

   ```DAX
   Total de Vendas = SUM(Financeiro[  Vendas ])
   ```
5. Pressione **Enter** ou clique no ✓ (check) para confirmar.
6. A medida aparece na tabela `Financeiro` com um ícone de calculadora.

### Passo 2 — Criar as demais medidas

Repita o processo (botão direito na tabela → **New measure**) para cada uma:

```DAX
Lucro Total = SUM(Financeiro[ Lucro ])
```

```DAX
Total de Unidades Vendidas = SUM(Financeiro[ Unidades Vendidas ])
```

```DAX
Margem de Lucro = DIVIDE([Lucro Total], [Total de Vendas])
```

### Passo 3 — Formatar Margem de Lucro como percentual

1. Clique na medida **Margem de Lucro** no painel Data.
2. Vá na faixa **Measure tools** (topo).
3. Em **Format**, escolha **Percentage** e defina **2** casas decimais.

> 🟢 **Boas práticas DAX:**
> - Sempre use `DIVIDE` (evita erro de divisão por zero).
> - Reaproveite medidas (`Margem de Lucro` usa `Lucro Total` e `Total de Vendas`).
> - Nomeie medidas de forma descritiva

### ✅ Checkpoint Dia 1
- [ ] Conectado ao Azure Databricks (Import)
- [ ] Tipos de dados corretos
- [ ] Dados limpos no Power Query
- [ ] 4 medidas DAX criadas
- [ ] Arquivo `.pbix` salvo

💾 **Salve o arquivo** como `Lab-PowerBI.pbix`

---

# 📅 DIA 2 — Visualize & Publish

## 🎯 Resultado esperado

![EMBEDDEDIMAGE](placeholder-0)

**Layout da Dashboard**

- **Topo (KPIs):** Total de Vendas · Lucro Total · Margem de Lucro · Total de Unidades Vendidas
- **Meio:** Vendas por País (bar) · Tendência de Lucro por Mês (line)
- **Base:** Vendas vs Lucro (combo) · Produtos com Maior Lucro (table)
- **Filtros:** Slicers de País e Ano

---

## 📈 Módulo 4 — Visualize

### Passo 1 — Preparar a página

1. Abra o `.pbix` salvo no Dia 1.
2. Na barra inferior, clique com o botão direito na aba **Page 1** → **Rename page**.
3. Digite **Visão Geral** e pressione **Enter**.

> 💡 No painel **Visualizations** (direita) ficam os tipos de visuais. No painel **Data** (mais à direita) ficam suas tabelas e medidas.

### Passo 2 — Criar os 4 KPIs (Cards)

Faça uma vez para cada medida (`Total de Vendas`, `Lucro Total`, `Margem de Lucro`, `Total de Unidades Vendidas`):

1. Clique em uma área **vazia** do canvas.
2. No painel **Visualizations**, clique no ícone **Card**.
3. No painel **Data**, marque o checkbox da medida correspondente.
4. Redimensione e posicione o card no **topo** da página.
5. Repita para as outras 3 medidas, alinhando os cards lado a lado.

### Passo 3 — Vendas por País (gráfico de barras)

1. Clique em uma área vazia → ícone **Stacked bar chart** (ou Clustered).
2. Arraste **País** para **Y-axis**.
3. Arraste **Total de Vendas** para **X-axis**.
4. Com o visual selecionado, vá em **Format your visual** (ícone de pincel) → **Data labels** → ative.
5. Clique nos **três pontinhos** (`...`) no canto do visual → **Sort axis** → **Total de Vendas** → **Sort descending**.
6. Em **General → Title**, defina: **Vendas por País**.

### Passo 4 — Tendência de Lucro por Mês (linha)

1. Clique em área vazia → ícone **Line chart**.
2. Arraste **Data** para **X-axis** (o Power BI cria a hierarquia Ano → Trimestre → Mês → Dia).
3. Arraste **Lucro Total** para **Y-axis**.
4. Em **Format → General → Title**, defina: **Tendência de Lucro por Mês**.

> 💡 Use os botões de **drill-down** no canto do visual para navegar entre Ano e Mês.

### Passo 5 — Vendas vs Lucro (Colunas e Linha)

1. Clique em área vazia → ícone **Line and stacked column chart**.
2. Arraste **Data** para **X-axis**.
3. Arraste **Total de Vendas** para **Column y-axis**.
4. Arraste **Lucro Total** para **Line y-axis**.
5. Em **Format → General → Title**, defina: **Vendas (barras) vs Lucro (linha)**.

### Passo 6 — Produtos com Maior Lucro (tabela)

1. Clique em área vazia → ícone **Table**.
2. No painel **Data**, marque (nesta ordem): **Produto**, **Total de Vendas**, **Lucro Total**.
3. Clique no cabeçalho **Lucro Total** dentro da tabela para ordenar **descendente** (seta para baixo).

### Passo 7 — Slicers (filtros)

**Slicer de País**

1. Clique em área vazia → ícone **Slicer**.
2. Arraste **País** para o campo **Field**.
3. Posicione no topo ou lateral da página.

**Slicer de Ano (Dropdown)**

1. Clique em área vazia → ícone **Slicer**.
2. Arraste **Ano** para **Field**.
3. No canto superior do slicer, clique na **seta** → escolha **Dropdown**.

### Passo 8 — Validar a interatividade

1. Clique em uma barra de **Vendas por País** → todos os outros visuais devem refletir o filtro.
2. Use os slicers para filtrar por país e ano e veja os KPIs mudarem.

> 🟢 **Boa prática de visual:** menos é mais. KPIs no topo, tendências no meio, detalhe na base.

💾 Salve novamente o `.pbix` (**Ctrl + S**).

---

## 🚀 Módulo 5 — Publish (Power BI Service)

### Passo 1 — Publicar o relatório

1. Salve o arquivo (**Ctrl + S**) — o Power BI exige o `.pbix` salvo antes de publicar.
2. Na faixa **Home**, clique em **Publish**.
3. Se solicitado, faça login com sua conta organizacional.
4. Na lista de workspaces, selecione um workspace de **DEV** (ex.: `Lab - DEV`).
5. Clique em **Select**.
6. Aguarde a mensagem **Success!** e clique em **Open '<nome>.pbix' in Power BI**.

### Passo 2 — Criar o Dashboard no Service

1. No navegador, o relatório abrirá no **Power BI Service**.
2. Passe o mouse sobre um **KPI Card** → clique no ícone de **📌 Pin** (alfinete) no canto superior.
3. Na janela que abrir, escolha **New dashboard**.
4. Dê o nome **Lab Financial Overview** e clique em **Pin**.
5. Repita para os outros 3 KPIs e para o gráfico **Vendas por País** — desta vez escolhendo **Existing dashboard → Lab Financial Overview**.
6. No menu lateral esquerdo do Service, clique em **Dashboards** → abra **Lab Financial Overview** para ver o resultado.

### Passo 3 — Configurar atualização agendada

1. No menu lateral, vá no seu **workspace** de DEV.
2. Encontre o **Semantic model** (dataset) com o nome do seu `.pbix`.
3. Clique nos **três pontinhos** (`...`) → **Settings**.
4. Em **Data source credentials**, clique em **Edit credentials** e autentique-se no Databricks.
5. Expanda **Refresh** → **Scheduled refresh**.
6. Ative o toggle **Keep your data up to date**.
7. Defina **frequência** (ex.: Daily) e **horário** (ex.: 06:00).
8. Clique em **Apply**.

### Passo 4 — Validar

1. Clique em **Refresh now** (`...` do dataset → **Refresh now**).
2. Verifique em **Refresh history** se a execução terminou com **Status: Completed**.

> 🟢 **Boa prática:** publique sempre em **DEV** primeiro. Promova para PROD só após validar com o usuário.

---

## ✅ Checkpoint Dia 2

- [ ] KPIs criados (4 cards)
- [ ] Bar, Line, Combo e Table criados
- [ ] Slicers de País e Ano funcionando
- [ ] Relatório publicado no Service
- [ ] Dashboard criado com visuais fixados
- [ ] Scheduled Refresh configurado

---

## 🧾 Boas práticas — Resumo

- **Ingestão única**: conecte direto à fonte (Databricks), sem cópias manuais
- **Import > DirectQuery** quando latência não é crítica
- **Toda limpeza no Power Query**, nunca no visual
- **Tipos de dados corretos** sempre
- **Medidas DAX antes dos visuais**
- Use `DIVIDE` em vez de `/`
- Layout: **KPIs → Tendências → Detalhe**
- Publique em **DEV antes de PROD**
