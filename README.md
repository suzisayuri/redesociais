
# 📈 TikTok Shop Analytics: Inteligência de Dados para Afiliados com Neo4j

Este projeto foi desenvolvido como parte do **Bootcamp da DIO: Análise de Dados com Grafos**. O objetivo é simular um ecossistema real de **Social Commerce (TikTok Shop)**, utilizando o Neo4j para mapear a jornada de venda: desde a criação do vídeo até a conversão em comissão para o afiliado.

---

## 🎯 Objetivo Estratégico
Como afiliada, o desafio não é apenas conseguir visualizações, mas identificar o **lucro real**. Este grafo permite:
1.  Identificar quais **tipos de vídeo** (ASMR, Review, Tutorial) geram maior conversão.
2.  Calcular o **lucro líquido** baseado em porcentagens de comissão variadas por produto.
3.  Monitorar o **desempenho de nichos** para decidir onde investir tempo de produção.

---

## 🛠️ Modelo de Dados (Graph Schema)

O grafo foi estruturado para conectar quatro pilares fundamentais:

- **Usuário (Afiliada):** O nó central que gerencia a estratégia (`Suzi Afiliada`).
- **Vídeo:** O motor de tráfego (possui propriedades como `cliques` e `tipo`).
- **Produto:** O item do catálogo (possui `preco` e `% de comissão`).
- **Nicho:** A categoria de mercado (ex: Home Office, Tech).

### Relacionamentos:
- `(:Usuario)-[:POSTOU]->(:Video)`
- `(:Video)-[:PROMOVE]->(:Produto)`
- `(:Produto)-[:CATEGORIA]->(:Nicho)`
- `(:Cliente)-[:COMPROU {nota: Int}]->(:Produto)`



---

## 🔍 Consultas de Negócio (KPIs)

Para validar a inteligência do modelo, foram criadas as seguintes queries Cypher:

### 1. Ranking de Lucro por Vídeo
*Pergunta: Qual formato de conteúdo está colocando mais dinheiro no meu bolso?*
```cypher
MATCH (u:Usuario {nome: "Suzi Afiliada"})-[:POSTOU]->(v:Video)-[:PROMOVE]->(p:Produto)<-[venda:COMPROU]-()
RETURN v.tipo AS Video, p.nome AS Produto, (venda.valor * p.comissao) AS Lucro_Comissao
ORDER BY Lucro_Comissao DESC

```

### 2. Eficiência de Conversão (Cliques vs. Vendas)

_Pergunta: Meu vídeo atrai curiosos ou compradores reais?_

Cypher

```
MATCH (v:Video)-[:PROMOVE]->(p:Produto)
OPTIONAL MATCH (p)<-[venda:COMPROU]-()
RETURN v.tipo AS Video, v.cliques AS Cliques, count(venda) AS Vendas,
       (toFloat(count(venda)) / v.cliques) * 100 AS Taxa_Conversao
ORDER BY Taxa_Conversao DESC

```

----------

## 🩹 Aprendizados e Troubleshooting (DIO Insights)

Durante o desenvolvimento, foram resolvidos desafios técnicos típicos de bancos de dados orientados a grafos:

-   **Case Sensitivity:** Ajuste de queries que retornavam `null` devido à diferenciação entre maiúsculas e minúsculas nos labels (`:Usuario` vs `:usuario`).
    
-   **Data Cleaning:** Implementação de scripts de reset (`DETACH DELETE`) para garantir a integridade dos dados entre diferentes rodadas de teste.
    
-   **Cálculos Agregados:** Uso de funções matemáticas (`AVG`, `SUM`, `round`) para transformar relações brutas em indicadores de performance (KPIs).
    

----------

## 🚀 Como Replicar

1.  Limpe seu banco Neo4j: `MATCH (n) DETACH DELETE n;`
    
2.  Execute o script de carga disponível na pasta `/scripts` deste repositório.
    
3.  Visualize a rede de conversão:
    
    Cypher
    
    ```
    MATCH p=(:Usuario)-[:POSTOU]->(:Video)-[:PROMOVE]->(:Produto) RETURN p
    
    ```
    

----------

**Desenvolvido por Suzi (Sayuri) – 2026**
