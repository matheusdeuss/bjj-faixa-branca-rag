# bjj-faixa-branca-rag — Agente RAG para Posições de BJJ (Faixa Branca)

**Nome do projeto:** `bjj-faixa-branca-rag`

Projeto de **Retrieval-Augmented Generation (RAG)** utilizando **LangChain** para construir um agente conversacional que responde perguntas sobre **posições básicas de Brazilian Jiu-Jitsu (BJJ) para faixas brancas**, com base em um manual especializado em PDF.

---

## Índice

- [Objetivo](#objetivo)
- [Arquitetura do Projeto](#arquitetura-do-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Estrutura de Diretórios](#estrutura-de-diretórios)
- [Pré-requisitos](#pré-requisitos)
- [Instalação e Configuração](#instalação-e-configuração)
- [Como Executar](#como-executar)
- [Conceitos Abordados](#conceitos-abordados)
- [Exemplos de Uso](#exemplos-de-uso)

---

## Objetivo

Construir um agente conversacional confiável que responda perguntas sobre posições de Jiu-Jitsu para faixas brancas, **evitando alucinações** do LLM ao conectar o modelo a documentos especializados.

O agente utiliza a técnica de **RAG (Retrieval-Augmented Generation)**, que recupera trechos relevantes de um manual em PDF e injeta esse contexto no prompt enviado ao modelo de linguagem.

---

## Arquitetura do Projeto

O fluxo de execução segue as etapas padrão de um sistema RAG:

```
1. Carregamento do PDF → 2. Divisão em Chunks → 3. Geração de Embeddings → 4. Armazenamento no ChromaDB (Banco Vetorial) → 5. Recuperação de Contexto (Retriever) → 6. Integração com LLM → 7. Resposta Final
```

1. **Carregamento do PDF**: O manual `manual_posicoes_faixa_branca_jiu_jitsu.pdf` é carregado usando `PyPDFLoader`.
2. **Divisão em Chunks**: O texto é dividido em blocos de 500 caracteres com sobreposição de 120 caracteres usando `RecursiveCharacterTextSplitter`.
3. **Embeddings**: Cada chunk é convertido em um vetor semântico usando o modelo `text-embedding-3-small` da OpenAI.
4. **Banco Vetorial**: Os vetores são armazenados e persistidos no **ChromaDB**.
5. **Retriever**: Dada uma pergunta do usuário, o sistema busca os 3 trechos mais relevantes via similaridade de cosseno.
6. **LLM**: O contexto recuperado é injetado no prompt do `gpt-4o-mini` para gerar uma resposta fundamentada.
7. **Retorno de Fontes**: A resposta inclui também os trechos e páginas utilizados como referência.

---

## Tecnologias Utilizadas

| Tecnologia                        | Versão / Modelo        | Descrição                                     |
| --------------------------------- | ---------------------- | --------------------------------------------- |
| **Python**                        | 3.x                    | Linguagem principal                           |
| **LangChain**                     | última                 | Framework para orquestração de cadeias LLM    |
| **LangChain Community**           | última                 | Integrations (PyPDF, Chroma)                  |
| **LangChain OpenAI**              | última                 | Integração com modelos da OpenAI              |
| **ChromaDB**                      | última                 | Banco de dados vetorial                       |
| **PyPDF**                         | última                 | Carregamento de documentos PDF                |
| **OpenAI GPT-4o-mini**            | gpt-4o-mini            | Modelo de linguagem para geração de respostas |
| **OpenAI text-embedding-3-small** | text-embedding-3-small | Modelo de embeddings para vetorização         |

---

## Estrutura de Diretórios

```
bjj-faixa-branca-rag/
├── Projeto 1.ipynb                          # Notebook principal com toda a implementação
├── manual_posicoes_faixa_branca_jiu_jitsu.pdf  # Base de conhecimento (manual de BJJ)
├── chroma_manual_posicoes_faixa_branca_jiu_jitsu/  # Banco vetorial ChromaDB persistido
│   ├── chroma.sqlite3
│   └── ab22cac0-42bb-4c5d-b650-ca4b60f181e7/
│       ├── data_level0.bin
│       ├── header.bin
│       ├── length.bin
│       └── link_lists.bin
└── README.md
```

---

## Pré-requisitos

- Python 3.8+
- Chave de API da OpenAI (`OPENAI_API_KEY`)

---

## Instalação e Configuração

### 1. Instalar as dependências

```bash
pip install langchain langchain-community langchain-openai chromadb pypdf
```

### 2. Configurar a variável de ambiente da OpenAI

**Windows (PowerShell):**

```powershell
$env:OPENAI_API_KEY = "sua-chave-aqui"
```

**Windows (CMD):**

```cmd
set OPENAI_API_KEY=sua-chave-aqui
```

**Linux / macOS:**

```bash
export OPENAI_API_KEY="sua-chave-aqui"
```

---

## Como Executar

1. Abra o notebook `Projeto 1.ipynb` no Jupyter Notebook, Jupyter Lab ou VS Code.
2. Certifique-se de que a variável `OPENAI_API_KEY` está configurada corretamente no ambiente.
3. Ajuste a variável `CAMINHO_PDF` se necessário (por padrão aponta para `manual_posicoes_faixa_branca_jiu_jitsu.pdf` no mesmo diretório).
4. Execute as células em ordem sequencial.

> **Nota**: Na primeira execução, o banco vetorial será criado e salvo no diretório `chroma_manual_posicoes_faixa_branca_jiu_jitsu/`. Nas execuções seguintes, você pode carregar o banco diretamente em vez de recriá-lo do zero.

---

## Conceitos Abordados

- **Definição do Problema**: Limitações de LLMs (conhecimento estático, alucinações) e a necessidade de respostas baseadas em fontes confiáveis.
- **Seleção da Base de Conhecimento**: Uso de um manual especializado em PDF com informações sobre posições de faixa branca.
- **Preparação dos Documentos (Chunking)**: Divisão do PDF em blocos menores com sobreposição para preservar contexto.
- **Embeddings e Banco Vetorial**: Conversão de texto em vetores semânticos e armazenamento no ChromaDB.
- **Retrieval (Retriever)**: Busca por similaridade para recuperar os trechos mais relevantes.
- **Integração com LLM**: Construção da cadeia RAG com `RetrievalQA`.
- **Testes e Validação**: Verificação das respostas e consulta às fontes originais.

---

## Exemplos de Uso

Abaixo, alguns exemplos de perguntas que podem ser feitas ao agente:

### Exemplo 1

**Pergunta:**

> Qual um dos quatro rolamentos essenciais que um faixa branca deve aprender primeiro?

**Resposta:**

> Um dos quatro rolamentos essenciais que um faixa branca deve aprender primeiro é o rolamento lateral (queda de lado).

---

### Exemplo 2

**Pergunta:**

> Quanto tempo leva, em média, para um faixa branca conquistar o primeiro grau, e quantos graus tem a faixa branca no total?

**Resposta:**

> Em média, leva de 4 a 6 meses de treino constante para um faixa branca conquistar o primeiro grau. A faixa branca tem um total de até 4 graus, que são indicados por fitas pretas na ponteira da faixa.

---

> **Curso**: LangChain — Criando Chatbots com RAG (Foundation)
