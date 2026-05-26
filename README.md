# 🧹 Projeto de Limpeza de Dados — Dataset de Clientes

## 📌 Objetivo

Aplicar técnicas de limpeza e preparação de dados em um dataset de clientes financeiros com problemas reais de qualidade, tornando-o consistente e confiável para análises futuras ou modelagem preditiva.

---

## 📊 Sobre o Dataset

| Característica | Valor |
|---|---|
| Registros | 50 clientes |
| Variáveis | 20 colunas |
| Domínio | Clientes financeiros (crédito, empréstimos, assinaturas) |
| Arquivo | `data/dirty_dataset.csv` |

**Variáveis presentes:** `customer_id`, `age`, `income`, `education`, `marital_status`, `credit_score`, `loan_amount`, `employment_years`, `default_status`, `last_payment_date`, `phone_number`, `email`, `city`, `state`, `zip_code`, `product_type`, `purchase_frequency`, `avg_monthly_spend`, `last_login`, `account_created`

---

## 🔍 Problemas Identificados

| Problema | Colunas Afetadas |
|---|---|
| Valores ausentes | `age`, `education`, `marital_status`, `credit_score`, `loan_amount`, `state`, `avg_monthly_spend`, `last_login`, `account_created`, `last_payment_date` |
| Tipos de dados incorretos | `credit_score`, `employment_years`, `income`, `default_status` (strings com `"ERROR"` ou `"35k"`) |
| Valores negativos inválidos | `age` (−32), `avg_monthly_spend` (−780.5, −920.5) |
| Telefones inválidos | Número negativo (−677), formatos com parênteses e pontos em vez de hífens |
| E-mails malformados | Emails terminando em `@` sem domínio (ex: `charlie.davis@`) |
| Inconsistência textual | `marital_status`: "Divorsed"; `education`: "HighSchool" e "High School" |
| CEPs com formatação errada | Aspas literais `"""97201"""`, CEPs com menos de 5 dígitos |
| `default_status` inconsistente | Mistura de `"0"`, `"1"`, `"y"`, `"n"` e `"ERROR"` para a mesma variável |

---

## 🛠 Etapas Realizadas

### 1. Entendimento Inicial dos Dados
- Análise dimensional (`shape`: 50 × 20), estrutura (`info`) e estatísticas descritivas (`describe`)
- Identificação de variáveis categóricas e numéricas
- Mapeamento de valores ausentes por coluna com `isnull().sum()`

### 2. Conversão de Tipos de Dados
- **Numéricas** — `age`, `credit_score`, `employment_years`, `avg_monthly_spend`, `loan_amount` convertidas com `pd.to_numeric(errors='coerce')`: valores inválidos como `"ERROR"` e `"35k"` viram `NaN`
- **Datas** — `last_login`, `account_created`, `last_payment_date` convertidas com `pd.to_datetime(format='mixed', errors='coerce')`

### 3. Remoção de Registros Inválidos
- Registros com `age < 0` removidos — 1 registro com idade −32

### 4. Tratamento de Valores Ausentes

| Tipo de Coluna | Estratégia | Motivo |
|---|---|---|
| Numéricas (`age`, `credit_score`, etc.) | Mediana | Resistente a outliers |
| Categóricas (`education`, `marital_status`, `state`) | `"unknown"` | Preserva o registro sem criar valor artificial |
| Datas (`last_login`, `account_created`, `last_payment_date`) | Mantidas como `NaT` | Ausência pode indicar comportamento relevante |
| `loan_amount` | `0` | Ausência indica que o cliente não possui empréstimo ativo |

### 5. Padronização das Variáveis Categóricas
- Lowercase + strip aplicados em todas as colunas categóricas
- `marital_status`: `"Divorsed"` → `"divorced"`
- `education`: `"HighSchool"` e `"High School"` → `"high_school"`
- `default_status`: `"y"` / `"1"` → `"yes"`, `"n"` / `"0"` → `"no"`, `"ERROR"` → `NaN`

### 6. Validação e Limpeza de Campos Específicos

**Telefone (`phone_number`)**
- Remoção de caracteres não numéricos (parênteses, pontos, hífens, espaços)
- Valores com comprimento diferente de 7 dígitos marcados como `"unknown"`

**E-mail (`email`)**
- Padronização para lowercase
- Validação via regex `^.+@.+\..+$` — emails inválidos marcados como `"unknown"`

**CEP (`zip_code`)**
- Remoção de aspas literais
- Preenchimento com zeros à esquerda para garantir exatamente 5 dígitos (`zfill(5)`)

### 7. Verificação de Duplicados
- Nenhum registro duplicado encontrado

### 8. Validação de Consistência Lógica
- Verificação de `employment_years > age`: nenhuma inconsistência encontrada

---

## ✅ Resultado

Após a limpeza, o dataset passou de 50 para 49 registros (1 removido por idade inválida) e está sem valores nulos críticos, com tipos de dados corretos e variáveis categóricas padronizadas — pronto para análises exploratórias ou modelagem preditiva.

---

## 📂 Estrutura do Projeto

```
data-cleaning-project/
│
├── data/
│   └── dirty_dataset.csv       ← dataset original (não modificado)
├── notebook/
│   └── data_cleaning.ipynb     ← análise e limpeza completa
├── requirements.txt
└── README.md
```

---

## ▶️ Como Executar

```bash
# Instalar dependências
pip install -r requirements.txt

# Abrir o notebook
jupyter notebook notebook/data_cleaning.ipynb
```

---

## 🛠 Tecnologias Utilizadas

- Python 3
- Pandas
- Jupyter Notebook
