# Trabalho Final de FIA: Raciocínio Espacial Neuro-Simbólico com LTNtorch

**Disciplina:** Fundamentos de Inteligência Artificial
**Ferramenta:** Logic Tensor Networks (LTNtorch)

---

## 1. Descrição Teórica

### 1.1. Inteligência Artificial Neuro-Simbólica (NeSy)
A **IA Neuro-Simbólica (NeSy)** combina o aprendizado robusto de redes neurais (Deep Learning) com a capacidade de raciocínio e explicabilidade da lógica simbólica. Enquanto as redes neurais são excelentes para lidar com dados sensoriais e incerteza, a lógica fornece a estrutura para raciocinar sobre esses dados, permitindo sistemas que aprendem com menos dados e respeitam regras de conhecimento prévio.

### 1.2. Logic Tensor Networks (LTN)
O projeto utiliza **Logic Tensor Networks (LTN)**, uma estrutura que integra lógica de primeira ordem em redes profundas.
* **Predicados** (ex: `IsCircle(x)`) são redes neurais que retornam um grau de verdade $[0,1]$.
* **Axiomas** são fórmulas lógicas usadas na função de perda (Loss). O treinamento busca maximizar a satisfatibilidade ($\text{SatAgg}$) dessas fórmulas.

### 1.3. O Dataset CLEVR Simplificado
Foi simulado um ambiente com objetos definidos por vetores de características de 11 posições:
* **Posição [0-1]:** Coordenadas x, y.
* **Cor [2-4]:** One-hot encoding (RGB).
* **Forma [5-9]:** One-hot encoding (Círculo, Quadrado, Cilindro, Cone, Triângulo).
* **Tamanho [10]:** Valor contínuo (0.0 a 1.0).

---

## 2. Experimentos e Métricas

O sistema foi avaliado em **5 execuções independentes**, gerando novos datasets aleatórios a cada rodada. O objetivo foi medir a capacidade do modelo de satisfazer regras lógicas e responder a consultas complexas.

### 2.1. Fórmulas Avaliadas (Passo 3.3)

1.  **Q1 (Filtragem Composta):** $\exists x(IsSmall(x)\wedge\exists y(IsCylinder(y)\wedge Below(x,y))\wedge\exists z(IsSquare(z)\wedge LeftOf(x,z)))$
    * *Significado:* Busca se existe um objeto pequeno, abaixo de um cilindro e à esquerda de um quadrado.
2.  **Q2 (Dedução Espacial):** $\exists x,y,z(IsCone(x)\wedge IsGreen(x)\wedge InBetween(x,y,z))$
    * *Significado:* Pergunta se existe um Cone Verde posicionado entre dois outros objetos. A relação `InBetween` é deduzida logicamente das relações `LeftOf`/`RightOf`.
3.  **Regra de Proximidade:** $\forall x,y((IsTriangle(x)\wedge IsTriangle(y)\wedge CloseTo(x,y))\Rightarrow SameSize(x,y))$
    * *Significado:* Regra universal que impõe que dois triângulos próximos devem ter o mesmo tamanho.

---

## 3. Resultados Consolidados

Abaixo estão as médias e desvios padrão obtidos após as 5 execuções do experimento.

| Métrica | Média | Desvio Padrão ($\sigma$) | Análise |
| :--- | :--- | :--- | :--- |
| **Satisfação Q1** | 0.0002 | 0.0000 | Baixa satisfação esperada, pois a configuração específica raramente ocorre em dados aleatórios. |
| **Satisfação Q2** | 0.0002 | 0.0000 | Similar à Q1, a combinação "Cone Verde" + "Posição Entre" é rara estocasticamente. |
| **Sat. Regra Proximidade** | **0.9181** | 0.0244 | **Alta.** O modelo aprendeu a correlacionar a proximidade espacial com a igualdade de tamanho para triângulos, mantendo consistência acima de 91%. |
| **Acurácia (Formas)** | **1.0000** | 0.0000 | **Perfeita.** O modelo identificou corretamente todas as formas geométricas. |
| **Precisão** | **1.0000** | 0.0000 | Nenhuma predição falso-positiva foi observada nas formas. |
| **Recall** | **1.0000** | 0.0000 | O modelo recuperou todas as instâncias corretas de cada classe. |
| **F1 Score** | **1.0000** | 0.0000 | Desempenho máximo na classificação. |

### 3.1. Explicação do Raciocínio (Ponto Extra)
O experimento demonstra que o LTN é capaz de:
1.  Realizar **Grounding Simbólico** perfeito (F1 Score = 1.0), mapeando características numéricas para conceitos lógicos sem erro.
2.  Incorporar **Conhecimento Abstrato** (`sat_regra_prox` > 0.9), impondo restrições lógicas no aprendizado.
3.  Avaliar **Consultas Existenciais** (Q1/Q2) de forma difusa (fuzzy), retornando valores consistentes com a aleatoriedade dos dados.

---

## 4. Como Executar

1. Instalar dependências: `pip install git+https://github.com/logictensornetworks/LTNtorch`
2. Executar o script `main.py` (ou notebook).
