# Defesa de Arquitetura de Software — Pull Request

## Leonardo de Oliveira Figura
- **Nicolas romanichen:**
- **Artefato(s) de Responsabilidade:** [ex: C4 Nível 3, Diagrama de Sequência e ADR 0001]

## O que este PR introduz na Documentação Viva?
Descreva sucintamente as alterações e os novos modelos adicionados à pasta `/docs/`:
- [ ] **Visão Funcional / Estrutural:** Diagrama de Casos de Uso ou Diagrama de Classes e Interfaces.
- [X] **Visão Comportamental:** Diagrama de Sequência, Atividades (com Raias/Fork-Join) ou Máquina de Estados.
- [ ] **Visão Arquitetural Macro:** Modelo C4 (L1, L2 ou L3 em Structurizr DSL, D2 ou PlantUML).
- [ ] **Governança:** Registros de Decisão Arquitetural (ADR).

## Defesa das Decisões de Design (Justificativa Técnica)
Explique a fundamentação técnica das decisões tomadas neste artefato:
1. **Escolha de Abstrações e Desacoplamento:** Por que as interfaces, visibilidades e relacionamentos foram desenhados dessa forma?
  Eu fiz o Backend / Orquestrador RAG ser o "coordenador" do sistema. Só ele sabe a ordem dos passos: ele chama cada parte, recebe a resposta e passa para a próxima. As outras partes (Interface, Serviço de Embeddings, Banco Vetorial, Repositório de Regras Aduaneiras e LLM) só fazem o seu trabalho quando são chamadas e não conversam entre si.
Também separei duas funções que poderiam ficar juntas. O Banco Vetorial só encontra os trechos mais parecidos com a pergunta e devolve os códigos deles. O texto das regras, as fontes e a data de validade ficam guardados no Repositório de Regras Aduaneiras, que é onde está a informação oficial.
Com isso, se um dia eu precisar trocar o Banco Vetorial ou a LLM, as regras aduaneiras e a tela do usuário continuam funcionando do mesmo jeito.

2. **Dinâmica e Resiliência:** Como os fluxos alternativos (`alt/else`), mensagens assíncronas ou guardas de estado tratam falhas no sistema?
   Primeiro alt (faltam dados essenciais): se o usuário não informou o produto, o NCM ou o destino, o sistema não tenta adivinhar. Ele pergunta só o que está faltando. Isso atende o RF03.
Segundo alt (não há informação suficiente ou confiável): se o sistema não encontra regras seguras para responder, ele não inventa. Avisa o usuário, mostra a orientação com ressalvas e diz que é preciso validar com um especialista. Isso atende o RF11.
O orquestrador separa só as regras que valem para aquele produto e destino e que ainda estão em vigor. Depois da resposta pronta, ele confere se as fontes estão corretas. Assim o usuário recebe a resposta com as fontes oficiais, como pede o RF10.

3. **Trade-offs:** Quais prós e contras foram considerados no momento da modelagem?
   Buscar em duas etapas (primeiro o Banco Vetorial, depois o Repositório): o sistema faz mais consultas e demora um pouco mais. Em troca, a resposta sempre mostra de onde a regra veio e se ela ainda vale, o que é muito importante em exportação.
Conferir as fontes antes de responder: diminui o risco de a IA dar uma informação errada, mas as respostas ficam mais cautelosas e podem pedir validação com mais frequência.
Separar o sistema em várias partes (Embeddings, Banco Vetorial etc.): cada parte pode ser melhorada ou trocada sozinha, mas existem mais lugares onde algo pode falhar.
Escopo: este diagrama é uma proposta de como o sistema funciona, baseada na Visão de Domínio e nos Requisitos Funcionais. Ele não define quais tecnologias serão usadas.

## Consistência Cross-Diagramas (Checklist de Autoverificação)
Antes de solicitar a revisão de um colega, confirme o alinhamento entre os modelos:
- [ ] Os nomes de métodos chamados no Diagrama de Sequência existem nas interfaces do Diagrama de Classes.
- [X] Os contêineres e componentes citados correspondem exatamente à nomenclatura do Modelo C4 L2/L3.
- [ ] As transições de estado refletem os gatilhos disparados pelas atividades da aplicação.

## Validação da Sintaxe Diagrams-as-Code / Models-as-Code
- **Ferramenta de Compilação/Renderização Utilizada:** Mermaid Live Editor
- **Status da Compilação:** [X] Código limpo e sem erros de sintaxe ao renderizar.
