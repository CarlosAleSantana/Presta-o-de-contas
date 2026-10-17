# 📊 Prestação de Contas Financeira

Painel em HTML para apresentar a prestação de contas semanal de um setor financeiro: o que estava **previsto** pagar, o que foi **pago**, o que foi pago **fora da previsão** e o que **não foi pago**.

> ⚠️ **Esta é uma versão demonstrativa.** Os nomes e códigos de fornecedores são fictícios. Os valores e as datas são adaptados e não representam nenhuma empresa real.

---

## 🎯 Objetivo

Responder, a cada semana, quatro perguntas:

| Pergunta | Status no projeto |
|---|---|
| O que estava previsto para pagar e foi pago? | ✅ `PAGO` |
| O que estava previsto e ainda não foi pago? | ⏳ `NÃO PAGO` |
| O que foi pago, mas não estava na previsão? | ⚠️ `FORA DA PREVISÃO` |
| Em quais casos não foi possível cruzar o título? | 🔎 `VERIFICAR` |

---

## 🧩 Como funciona

O projeto tem duas camadas, cada uma com uma responsabilidade.

```
Relatórios do sistema  ──►  PLANILHA (ETL)  ──►  HTML (apresentação)
 previsto + quitado          tratamento,           leitura e exibição
                             chave e status        do resultado
```

### 1. Planilha: todo o ETL

Toda a regra de negócio fica em fórmulas na planilha. Os relatórios de títulos a pagar (previsão) e de títulos quitados entram em abas separadas, uma dupla para cada fluxo:

| Fluxo | Aba de previsão | Aba de quitados |
|---|---|---|
| Fornecedores | `Fornecedores` | `Quitados DUPP` |
| Despesas | `Despesa` | `quitado Despesa` |
| Validação | `CONFERENCIA` | |

O tratamento segue estas etapas:

1. **Normalização do título.** O número do título é padronizado. Na aba de quitados, quando necessário, ele é extraído da observação da operação.
2. **Chave única.** Cada título recebe uma `CHAVE` composta por: código do fornecedor, empresa, espécie, título, série e parcela.
3. **Cruzamento.** A chave de cada aba é procurada na aba correspondente.
4. **Classificação de status.**
   - Na previsão: encontrou na aba de quitados → `PAGO`; não encontrou → `NÃO PAGO`.
   - Nos quitados: encontrou na previsão → `PAGO`; não encontrou → `FORA DA PREVISÃO`.
   - Chave que não pôde ser montada → `VERIFICAR`.
5. **Comparação de valores.** Calcula a diferença entre o valor previsto e o valor nominal quitado.
6. **Conferência.** A aba `CONFERENCIA` mostra, por aba, o total de linhas com chave, a contagem de cada status, as chaves repetidas, as linhas com valor divergente e se a soma dos status fecha com o total.

### 2. HTML: apenas a apresentação

O HTML **não trata dados**. Ele lê a planilha já tratada e exibe o resultado em seções:

- **Resumo consolidado:** totais de previsto, pago, fora da previsão e não pago.
- **Análises:** visão por semana e por fornecedor.
- **Insights do período:** destaques do que foi apurado.
- **Verificação:** consulta título a título, com filtros.
- **Integridade da base:** resultado das conferências.

---

## 🔒 Segurança da informação

- O HTML é um arquivo único e funciona offline. Não usa servidor, banco de dados nem CDN, e a biblioteca de leitura de Excel (SheetJS) vai embutida.
- Os dados reais ficam na planilha, que não é publicada.
- A leitura da planilha acontece no navegador de quem abre o arquivo. Nenhum dado é enviado para a internet.

> Esta versão pública traz um conjunto de dados fictício embutido no HTML, apenas para demonstração. Em uso real, **não publique** o HTML com dados reais nem a planilha de produção. Mantenha-os em um repositório privado ou em um local controlado.

---

## 🚀 Como usar

1. Baixe ou clone este repositório.
2. Abra o `index.html` no navegador (Chrome ou Edge são recomendados).
3. A página abre com os dados de demonstração.
4. Para usar a sua própria planilha, clique no botão de atualizar/selecionar arquivo e escolha um `.xlsx` com a mesma estrutura de abas e colunas descrita acima.

Se publicar a página via servidor (GitHub Pages, SharePoint, IIS), o arquivo definido em `CONFIG.DATA_URL` pode ser lido automaticamente a cada abertura. Os nomes das abas também são configuráveis no início do script (`CONFIG.SHEETS`).

---

## 🗂️ Estrutura do repositório

```
.
├── index.html           # apresentação (versão demo, dados fictícios)
├── dados_exemplo.xlsx   # planilha de exemplo com dados fictícios
├── README.md
└── .gitignore           # ignora planilhas reais (*.xlsx, exceto o exemplo)
```

Sugestão de `.gitignore`:

```
*.xlsx
!dados_exemplo.xlsx
```

---

## 🛠️ Tecnologias

- HTML, CSS e JavaScript puro
- [SheetJS](https://sheetjs.com) para leitura de arquivos Excel
- Fórmulas do Excel para o ETL (`SE`, `PROCV`/`CORRESP`, `ÍNDICE`, `CONT.SE`, `TEXTO`, `ARRUMAR`)

---

## 📸 Capturas de tela

<!-- Adicione prints da versão demo, nunca da base real --><img width="1334" height="504" alt="1" src="https://github.com/user-attachments/assets/f2dd4281-15d0-4091-ad1f-f45f2eb07dd9" />

<!-- ![Resumo consolidado](docs/resumo.png) -->

---

## 📚 Aprendizados

- Uma boa chave de cruzamento vale mais que um gráfico bonito.
- Mostrar o que fugiu da previsão é tão importante quanto mostrar o que foi cumprido.
- Uma aba de conferência evita muito retrabalho.
- Separar o tratamento (planilha) da apresentação (HTML) deixa as regras auditáveis e a reunião mais limpa.

---

## 👤 Autor

**Carlos Alexandre de Santana**
Analista de BI · [LinkedIn](www.linkedin.com/in/carlosalesantana) · 

---


