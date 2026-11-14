# Citizen Assist AI — Módulo de Soluções em Energias Renováveis e Sustentáveis

Entrega de Projeto da disciplina de **Soluções em Energias Renováveis e Sustentáveis** | **Citizen Assist AI** (Global Solution — Ciência da Computação / 1CCPH).

---

## Integrantes do Grupo
* **Pedro Andreassa Zamai** — RM: 569318
* **Pedro Yoshikado Garcia** — RM: 570449
* **Thiago Maluf Hofmann** — RM: 569852
* **Curso:** Ciência da Computação — Turma 1CCPH

---

## 1. Definição do Contexto, Público e Necessidade Energética
* **Público-alvo:** Moradores urbanos (especialmente famílias residentes em apartamentos e casas residenciais) que enfrentam faturas de energia elevadas e possuem dúvidas sobre como economizar sem perder conforto.
* **Necessidade Energética:** Falta de clareza sobre o peso individual de cada eletrodoméstico na conta mensal e desconhecimento sobre alternativas renováveis viáveis para quem mora em apartamento (onde a instalação individual de painéis fotovoltaicos no telhado costuma ser inviável).
* **Como o Citizen Assist AI contribui:** O assistente diagnostica o consumo residencial em kWh e reais, compara alternativas de eficiência energética e fontes renováveis para o mesmo período e responde dúvidas do cidadão em linguagem acessível, explicitando premissas, limitações e fontes verificáveis.

---

## 2. Análise de Consumo e Premissas de Cálculo
* **Período de referência:** 30 dias (mensal) e 12 meses (anual).
* **Tarifa residencial aplicada:** `R$ 0,85 / kWh` (tarifa média residencial B1 com tributos — Ref. ANEEL, configurável pelo usuário).
* **Custo de disponibilidade (taxa mínima):** `50 kWh/mês` (padrão bifásico — Ref. Resolução Normativa ANEEL nº 1.000/2021).
* **Fator de emissão de carbono:** `0,0385 kg CO₂ / kWh` (fator médio anual do Sistema Interligado Nacional — Ref. MCTI).
* **Equação base utilizada:**
  $$\text{Consumo (kWh/mês)} = \frac{\text{Potência (W)}}{1000} \times \text{Horas/dia} \times \text{Fator de Uso} \times 30$$

| Equipamento | Potência (W) | Uso Diário | Fator de Uso | Consumo (kWh/mês) | Custo (R$/mês) | Classificação e Origem |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| Ar-Condicionado Split 12.000 BTUs | 1.100 W | 6,0 h | 0,75 (modulação) | **148,5 kWh** | R$ 126,22 | Dado oficial (PROCEL/INMETRO) |
| Geladeira Frost Free Duplex | 150 W | 24,0 h | 0,42 (ciclagem) | **45,4 kWh** | R$ 38,56 | Dado oficial (PROCEL/INMETRO) |
| Chuveiro Elétrico (Modo Inverno) | 5.500 W | 0,5 h | 1,00 | **82,5 kWh** | R$ 70,12 | Potência oficial / Tempo estimado |
| Smart TV LED 50" + Stand-by | 140 W | 6,0 h | 1,00 | **25,2 kWh** | R$ 21,42 | Dado oficial + Stand-by estimado |
| **TOTAL CENÁRIO BASE** | — | — | — | **301,6 kWh** | **R$ 256,33** | **R$ 3.075,91/ano** |

---

## 3. Comparação de Alternativas e Justificativas (Período: 30 dias / 12 meses)

Para atender à mesma demanda energética residencial (**301,6 kWh/mês**), foram avaliadas três alternativas:

| Critério Avaliado | Cenário Base | Alt. A: Eficiência e Hábitos | Alt. B: Solar por Assinatura (Renovável) | Alt. C: Painéis Solares Próprios (Renovável) |
| :--- | :---: | :---: | :---: | :---: |
| **Consumo da Rede Convencional** | 301,6 kWh/mês | 233,2 kWh/mês | 50,0 kWh/mês (taxa mínima) | 50,0 kWh/mês (taxa mínima) |
| **Custo Mensal Estimado** | R$ 256,33 | R$ 198,21 | R$ 224,25 | R$ 42,50 |
| **Economia Anual Estimada** | — | **R$ 697,45/ano** | **R$ 384,89/ano** | **R$ 2.565,91/ano** |
| **Investimento Inicial (CAPEX)** | R$ 0,00 | R$ 0,00 | R$ 0,00 | ~R$ 9.198,00 (2,19 kWp) |
| **Payback Estimado** | — | Imediato | Imediato | ~3,6 anos |
| **CO₂ Evitado (kg/ano)** | 0 kg | **31,6 kg/ano** | **116,2 kg/ano** | **116,2 kg/ano** |
| **Viabilidade (Apartamento)** | — | **Alta** (custo zero) | **Alta** (sem obras/telhado) | **Inviável** individualmente |

### Justificativa Técnica das Recomendações
* **Para moradores de apartamentos:** A instalação individual de painéis no telhado (**Alternativa C**) esbarra na ausência de área privativa e regras condominiais. Assim, o assistente recomenda combinar a **Alternativa A** (ajuste de termostato do ar-condicionado para 23 °C, redução de banho e corte de *stand-by*) com a **Alternativa B** (Energia Solar por Assinatura via geração compartilhada — Lei nº 14.300/2022), gerando economia estimada de até **R$ 1.005,36/ano** sem obras e compensando a energia acima da taxa mínima com fonte renovável.
* **Para moradores de casas próprias:** Recomenda-se aplicar imediatamente a **Alternativa A** e dimensionar a **Alternativa C** (sistema fotovoltaico *on-grid*), cujo retorno sobre o investimento (*payback*) ocorre em aproximadamente **3,6 anos**.

---

## 4. Integração ao Citizen Assist AI e Guardrails
* **Arquitetura Híbrida:** O motor determinístico em Python calcula todos os indicadores físicos e financeiros e injeta o diagnóstico estruturado no modelo **Gemini 3.8 Flash** (via Google GenAI SDK) para responder dúvidas abertas do usuário com base exata nos números simulados. Caso o ambiente esteja sem chave de API (`GEMINI_API_KEY`), o sistema aciona automaticamente um motor local com expressões regulares (Regex) sem interrupção.
* **Guardrails de Escopo:** Tanto o prompt de sistema do LLM quanto o motor local possuem travas estritas para responder exclusivamente sobre energia elétrica, eficiência e sustentabilidade, bloqueando perguntas fora de escopo (como receitas, entretenimento ou tentativas de alteração de instrução).

---

## 5. Limitações e Condições
* Todas as projeções são **estimativas técnicas de apoio à decisão**, não constituindo garantia de economia ou faturamento.
* Os resultados reais variam conforme bandeiras tarifárias vigentes da ANEEL, tarifa local da distribuidora, eficiência real (Selo PROCEL) dos aparelhos, hábitos dos moradores e índice de irradiação solar regional.

---

## 6. Como Executar e Testar a Solução

### Via Google Colab
1. Abra o notebook `GSSERS.ipynb` pelo botão **"Open in Colab"**.
2. *(Opcional)* Para habilitar a camada generativa com Gemini 3.8 Flash, instale o SDK (`!pip install -q -U google-genai`) e cadastre sua chave `GEMINI_API_KEY` na aba **Secrets (ícone de chave)** do Colab.
3. Execute a **Célula 1** para rodar o diagnóstico padrão completo.
4. Execute a **Célula 2** (`executar_modo_interativo()`) para testar o fluxo conversacional informando tipo de moradia, tarifa, aparelhos extras e dúvidas livres.

### Via Terminal Local (Python 3.10+)
```bash
git clone [https://github.com/Pazamai674/GS_SOLU-ES-EM-ENERGIAS-RENOV-VEIS-E-SUSTENT-VEIS.git](https://github.com/Pazamai674/GS_SOLU-ES-EM-ENERGIAS-RENOV-VEIS-E-SUSTENT-VEIS.git)
cd GS_SOLU-ES-EM-ENERGIAS-RENOV-VEIS-E-SUSTENT-VEIS
python GSSERS.py
```

---

## 7. Registro de Testes com Usuários

Foram realizados testes de compreensão, usabilidade e coerência das respostas com usuários reais (dados pessoais anonimizados):

* **Teste 1 — Morador de apartamento (8º andar) simulando conta com ar-condicionado e chuveiro**
  * **Feedback:** *"O assistente sugeriu instalar placas solares no meu telhado, mas moro em prédio."*
  * **Ajuste realizado:** Adicionada distinção entre **apartamento** e **casa**, incluindo **Energia Solar por Assinatura (Lei nº 14.300/2022)** como alternativa renovável viável para apartamentos.

* **Teste 2 — Estudante verificando o consumo mensal de geladeira e TV**
  * **Feedback:** *"A geladeira estava gastando mais que o chuveiro elétrico, parecia fora da realidade."*
  * **Ajuste realizado:** Aplicado o **fator de ciclagem do compressor (0,42)** conforme tabela **PROCEL/INMETRO**, ajustando o consumo para ~45,4 kWh/mês.

* **Teste 3 — Usuário preenchendo o modo interativo pela primeira vez**
  * **Comportamento observado:** O usuário digitou o valor total da fatura (`400`) no campo da tarifa unitária do kWh (`0.85`) e minutos no lugar de horas.
  * **Ajuste realizado:** Implementadas validações de faixa na entrada (`R$ 0,10 a R$ 5,00/kWh` e `0h a 24h/dia`) com mensagens orientativas claras.

* **Teste 4 — Perguntas específicas de potência (chuveiro 5000W) e perguntas fora do tema**
  * **Comportamento observado:** Necessidade de garantir respostas precisas para trocas de equipamentos e bloqueio de temas aleatórios.
  * **Ajuste realizado:** Implementados **guardrails de escopo** no Gemini 3.8 Flash e cálculo dinâmico de diferença de potência no motor local.

---

## 8. Referências Verificáveis

- ANEEL — Agência Nacional de Energia Elétrica: Resolução Normativa nº 1.000/2021 (Regras de Prestação do Serviço Público de Distribuição de Energia Elétrica e Custo de Disponibilidade) e Ranking de Tarifas Residenciais.
- BRASIL: Lei nº 14.300, de 6 de janeiro de 2022 (Marco Legal da Microgeração e Minigeração Distribuída e Geração Compartilhada).
- PROCEL / INMETRO: Tabelas de Consumo e Eficiência Energética de Eletrodomésticos (Programa Nacional de Conservação de Energia Elétrica).
- MCTI — Ministério da Ciência, Tecnologia e Inovação: Fatores de Emissão de CO₂ do Sistema Interligado Nacional (SIN).
- ABSOLAR / Greener: Estudo Estratégico de Geração Distribuída — Custos médios de sistemas fotovoltaicos residenciais no Brasil.
