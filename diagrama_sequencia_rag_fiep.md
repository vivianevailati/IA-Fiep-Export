# Assistente de IA para Exportação — Fiep (Grupo 8)

## Diagrama de Sequência — Pipeline RAG

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuário
    participant I as Interface
    participant A as Backend / Orquestrador RAG
    participant E as Serviço de Embeddings
    participant V as Banco Vetorial
    participant R as Repositório de Regras Aduaneiras
    participant L as LLM

    U->>I: Pergunta sobre exportação
    I->>A: Envia pergunta e contexto da sessão
    A->>A: Identifica intenção, produto, NCM e destino

    alt Faltam dados essenciais da operação
        A-->>I: Solicita somente as informações ausentes
        I-->>U: Apresenta pergunta complementar
    else Contexto suficiente
        A->>E: Solicita embedding da consulta
        E-->>A: Vetor semântico da consulta
        A->>V: Busca vetorial por trechos relevantes
        V-->>A: IDs dos trechos e pontuações de similaridade
        A->>R: Recupera textos, fontes, vigência e aplicabilidade
        R-->>A: Regras, requisitos, restrições e referências
        A->>A: Filtra por produto, destino e validade normativa

        alt Não há evidências suficientes ou confiáveis
            A-->>I: Informa limitação e necessidade de validação
            I-->>U: Exibe orientação com ressalvas
        else Evidências suficientes
            A->>L: Envia pergunta, contexto e trechos recuperados
            L-->>A: Resposta orientativa fundamentada
            A->>A: Confere referências e sinaliza incertezas
            A-->>I: Resposta, fontes oficiais e próximos passos
            I-->>U: Exibe orientação com referências
        end
    end
```

**Escopo:** fluxo de consulta RAG, incluindo busca vetorial, recuperação de regras aduaneiras e chamada à LLM. O diagrama é uma proposta lógica baseada na Visão de Domínio e nos Requisitos Funcionais do projeto; a divisão em serviços não implica tecnologias já definidas.
