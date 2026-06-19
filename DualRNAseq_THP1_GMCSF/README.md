# DualRNAseq THP-1 + Candida albicans + GM-CSF

Pipeline de bioinformática modular desenvolvido em **Nextflow (DSL2)** e **R** focado na análise de expressão gênica diferencial de consenso (**DESeq2 ∩ edgeR**) para ensaios multifatoriais de Dual RNA-seq.

## 🧪 Desenho Experimental (4 Grupos)

O pipeline gerencia um modelo de dupla espécie (Hospedeiro-Patógeno) sob tratamento:
1. `Controle`: Macrófagos humanos THP-1 basais.
2. `Candida_Isolada`: *Candida albicans* pura (controle do patógeno).
3. `Infeccao`: Co-cultivo (THP-1 + *C. albicans*).
4. `GMCSF_Infeccao`: Co-cultivo tratado com a citocina GM-CSF.

---

## 📐 Equações de Confronto Automatizadas

O pipeline executa e exporta de forma independente três grandes frentes biológicas em arquivos Excel estruturados com multi-abas:

### 🔹 Opção A: Efeito Direto do GM-CSF na Infecção
Avalia a modulação terapêutica diretamente no ambiente infeccioso do hospedeiro:
$$\text{Efeito Direto} = \text{GMCSF\_Infeccao} - \text{Infeccao}$$

### 🔹 Opção B: Sinergia / Interação Completa
Isola o efeito cooperativo e não-linear da citocina cruzando os deltas do estímulo:
$$\text{Interação} = (\text{GMCSF\_Infeccao} - \text{Infeccao}) - (\text{Infeccao} - \text{Controle})$$

### 🔹 Resposta do Fungo ao Macrófago
Analisa a reprogramação transcricional e ativação de fatores de virulência da *Candida* ao interagir com o sistema imune:
$$\text{Resposta Patógeno} = \text{Infeccao} - \text{Candida\_Isolada}$$

---

## 📊 Estrutura de Validação de Consenso (Critério do Laboratório)

Para mitigar falsos positivos, os genes reportados passam pelo crivo de significância concorrente:

$$\text{DEGs}_{\text{Consenso}} = \{ g \in \text{Genes} \mid P_{\text{adj, DESeq2}}(g) < 0.05 \land FDR_{\text{edgeR}}(g) < 0.05 \}$$

Cada confronto gera um relatório `.xlsx` contendo as seguintes abas padronizadas:
- **Resumo:** Volumetria de genes por grupo.
- **Geral:** Lista completa de concordantes significativos sem nota de corte de Fold Change.
- **FC1_UP / FC1_DOWN:** Genes concordantes com $|\log_2 FC| > 1$.
- **FC2_UP / FC2_DOWN:** Genes concordantes com $|\log_2 FC| > 2$.

### Mapeamento Estrito de Colunas
As planilhas geradas herdam a assinatura analítica exigida:
`Gene` | `ENSEMBL_ID` | `Nome_Completo` | `Cromossomo` | `Regulacao` | `sig_DESeq2` | `sig_edgeR` | `log2FC_DESeq2` | `padj_DESeq2` | `baseMean_DESeq2` | `logFC_edgeR` | `FDR_edgeR` | `logCPM_edgeR`