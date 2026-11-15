# SEALog 🦭 — Desafio Prático em Grupo · Processo Seletivo SEAL 


Este repositório documenta a nossa participação no **Desafio Prático em Grupo**, segunda etapa do Processo Seletivo 2026.2 da **SEAL — Liga Acadêmica de Engenharia de Software** do Centro de Informática da UFPE (CIn-UFPE).

O objetivo aqui é registrar a dinâmica, o problema apresentado, a mudança de requisitos que aconteceu no meio do caminho e a solução que construímos, incluindo as decisões tomadas e o porquê delas.



---

## 1. O problema: a SEALog

A **SEALog** é uma empresa de logística regional que entrega para lojas de bairro, farmácias independentes, lojas de roupa, papelarias, floriculturas e pequenos e-commerces que vendem por Instagram e WhatsApp.

- **Operação:** segunda a sábado, 7h às 19h.
- **Atendimento:** segunda a sexta, 8h às 18h, e sábados, 8h às 12h.
- **Prazo padrão:** dia útil seguinte à solicitação, com possibilidade de agendar uma data.

### Como a operação funcionava

1. **Solicitação e conferência:** o Atendimento recebe pedidos por WhatsApp (texto, áudio ou planilhas de clientes) e confere nome, telefone, endereço completo, volume e data. Cerca de **1 em cada 5 pedidos chega incompleto**, o que gera um vai e volta com o cliente.
2. **Registro e planejamento:** os pedidos vão para um grupo interno de WhatsApp. Analistas registram cada entrega em planilhas online (cópias divergem entre si). Etiquetas são escritas à mão. Rotas são montadas manualmente.
3. **Execução e atualização:** cerca de 18 motoristas compartilham um único grupo de WhatsApp. Atualizações chegam em lote ou se perdem na conversa. Os analistas precisam caçar mensagens para atualizar planilhas.
4. **Fechamento:** os não entregues voltam ao galpão e o coordenador decide caso a caso, sem regras formais. A planilha fecha às 19h e o financeiro cobra os clientes a partir dela.

### O "cadê minha entrega?"

O fluxo de consulta passa por até quatro pessoas: cliente → atendente → analista → motorista, e a resposta volta pelo mesmo caminho. Isso leva **de 20 minutos a mais de 2 horas**, e em dias de movimento algumas perguntas ficam sem resposta até o dia seguinte.

### Casos que ilustram as dores

| Caso | O que aconteceu |
|---|---|
| Pedido com informações incompletas | Em 3 entregas de uma mensagem, só 1 tinha dados suficientes |
| O buquê de sábado | Motorista avisou às 9h40 que não achava o endereço; a mensagem foi vista às 11h; a loja soube pela consumidora às 11h30; o buquê chegou às 14h |
| O registro incorreto | Entrega marcada como "ok" na linha errada da planilha; 3 dias depois, descobriu-se que o pacote foi deixado no apartamento errado |
| A segunda-feira de chuva | ~40 entregas atrasaram, 120+ mensagens no Atendimento e nenhuma forma rápida de saber quais clientes avisar |

### Causa raiz identificada pela equipe

> **A falta de uma fonte única de verdade.** Cada informação vive numa mensagem de WhatsApp ou numa cópia de planilha.

### Restrições consideradas

- A SEALog não tem equipe de TI.
- Opera de segunda a sábado, 7h às 19h.
- A solução precisa ser implantada **sem parar a operação**.

---

## 2. A mudança de requisitos

No meio do desafio, a Diretoria da SEALog enviou um **Comunicado de Mudança dos Requisitos**: a empresa deixaria de ter frota própria e passaria a ser um **operador logístico**.

### O que mudou

- A SEALog deixa de ter motoristas próprios.
- Todas as entregas passam a ser feitas por **duas empresas parceiras**:
  - **Refactor Express:** organiza entregas por planilhas e grupos de WhatsApp, de forma parecida com o que a SEALog fazia.
  - **Fretes do Seu Zé:** trabalha principalmente por telefone e WhatsApp, com **pouca experiência em tecnologia** e necessidade de ajuda para se adaptar.
- A SEALog **não fala mais com os motoristas**: a informação chega pelas parceiras, cada uma de um jeito e com uma frequência.
- A rota passa a ser responsabilidade de cada parceira.
- O Financeiro passa a cobrar lojistas **e** pagar parceiras, então saber exatamente o que foi concluído fica ainda mais importante.

### O que continua igual

- Clientes pedem à SEALog pelos mesmos canais.
- O Atendimento continua conferindo pedidos e falando com clientes.
- Prazos e horários não mudam.
- Quando há problema, o cliente procura a SEALog, não a parceira.

### Pontos que o cliente deixou em aberto

- Como trocar informações com cada parceira e acompanhar tudo de forma organizada.
- Se e como parceiras e entregadores teriam acesso ao sistema.
- Como acompanhar o desempenho das parceiras.
- Como comprovar uma entrega quando parceira e destinatário discordam.

### Restrição nova e importante

A mudança começa no mês seguinte: **a operação precisa funcionar desde o primeiro dia, ainda de forma manual**, e qualquer melhoria precisa acontecer **com tudo rodando**.

---

## 3. Nossa solução

Propomos um **sistema web único** para a SEALog: uma só base de entregas, consultada e atualizada por todos, que substitui as planilhas soltas e o grupo de WhatsApp como fonte de verdade.

O WhatsApp **não é eliminado**: ele continua como canal de entrada e de avisos, mas o dado vive no sistema. Quem preferir seguir pelos canais habituais pode fazê-lo, e a adoção da aplicação é gradual.

### Visão por perfil

Cada perfil vê só o que precisa:

| Perfil | O que faz no sistema |
|---|---|
| **Lojista (portal)** | Solicitar entrega, acompanhar status, repetir entrega anterior |
| **Atendente / Operação** | Cadastrar pedidos e clientes, pedir dados que faltam, consultar status, histórico e comprovante |
| **Coordenador** | Painel do dia, ver entregas atribuídas a cada parceira, decidir exceções e fechar o dia |
| **Financeiro** | Conferir entregas concluídas, relatório por cliente, exportar dados |
| **Parceira** | Receber o lote (por WhatsApp), executar coleta e entrega, informar status e comprovante (por WhatsApp, aplicação, planilha ou telefone) |
| **Consumidor final (sem login)** | Abrir link de rastreio, ver status e janela de entrega, receber avisos |

### Fluxo principal

```
Cliente cria o pedido (portal ou WhatsApp)
        ↓
Atendente cadastra e valida os campos
        ↓
Operadores validam e selecionam a empresa parceira
        ↓
Coordenador gerencia a operação e trata ocorrências
        ↓
Atendimento, lojista e destinatário consultam o status sem passar pela Operação
```

### Ciclo de status de uma entrega

```
Pedido criado → Atribuído à parceira → Coletado → Em rota → Entregue
                                                      ↓
                                                  Ocorrência
                                                      ↓
                          Decisão da SEALog: reagendar · trocar de parceira · devolver
                          ("trocar de parceira" volta para "Atribuído à parceira")
```

Qualquer etapa pode desviar para **Ocorrência**, que volta ao ciclo pela decisão do coordenador.

### Como cada dor do enunciado é atacada

| Dor | Resposta da solução |
|---|---|
| Pedidos incompletos (1 em 5) | Validação de campos obrigatórios no cadastro; pedido incompleto retorna ao cliente |
| Planilhas divergentes | Base única de entregas, com código e histórico de status |
| Atualizações perdidas no grupo | Registro centralizado, com origem de cada atualização |
| "Cadê minha entrega?" levando horas | Atendimento consulta status, histórico e comprovante direto no sistema |
| Segunda-feira de chuva | Filtro de entregas atrasadas e aviso aos clientes afetados de uma vez |
| Exceções sem regra | Ocorrências com motivos padronizados e decisão registrada |
| Registro incorreto / disputas | Comprovante (foto, data/hora, quem recebeu) anexado à entrega |
| Etiquetas à mão | Etiquetas com código geradas pelo sistema |
| Cobrança sobre planilha incompleta | Fechamento por cliente gerado a partir da base única |

### Hipótese adotada

O cliente da SEALog é a **loja**, então o destinatário final **não precisa de conta** no sistema; ele acompanha por um link de rastreio sem login.

---

## 4. Requisitos

Levantamos **10 requisitos funcionais** e **9 não funcionais**, já ajustados ao novo modelo com empresas parceiras.

### 4.1 Requisitos funcionais

| ID | O sistema deve permitir | Dor que resolve |
|---|---|---|
| RF01 | Registrar pedidos com campos obrigatórios validados (destinatário, telefone, endereço completo, volume e data) | Pedido incompleto |
| RF02 | Que o lojista faça pedidos por um portal, fora do horário do Atendimento | Atendimento só até 18h |
| RF03 | Importar planilhas (pedidos de lojistas e atualizações de parceiras) apontando erros linha a linha | Planilhas próprias de cada cliente |
| RF04 | Manter uma base única de entregas, com código e histórico de status | Planilhas divergentes |
| RF05 | Atribuir cada entrega a uma parceira e trocar de parceira quando necessário | Distribuição manual; dias de pico |
| RF06 | Que a parceira atualize o status por um link, sem login | Mensagens perdidas no grupo |
| RF07 | Lançar manualmente uma atualização recebida por telefone, registrando a origem | Parceira sem acesso à tecnologia |
| RF08 | Anexar o comprovante à entrega: foto, data e hora, nome de quem recebeu | Registro incorreto; disputas |
| RF09 | Alertar a Operação assim que uma ocorrência for registrada | O buquê de sábado |
| RF10 | Buscar uma entrega por código ou cliente e ver status, comprovante e histórico | Consulta de até 2 h |

### 4.2 Requisitos não funcionais

| ID | Categoria | Requisito |
|---|---|---|
| RNF01 | Usabilidade | Interface responsiva, usada pelo navegador do celular ou do computador, sem instalação |
| RNF02 | Usabilidade | Link da parceira com botões grandes e no máximo dois toques para informar o status |
| RNF03 | Disponibilidade | Disponível durante toda a operação: segunda a sábado, 7h às 19h |
| RNF04 | Desempenho | Consulta de status respondida em até 2 segundos |
| RNF05 | Rastreabilidade | Toda mudança de status guarda autor, data, hora e origem; nada é apagado |
| RNF06 | Consistência | Um único banco de dados como fonte de verdade |
| RNF07 | Segurança e LGPD | Acesso por perfil; o rastreio do lojista não expõe fotos nem dados de outros clientes |
| RNF08 | Manutenibilidade e custo | Arquitetura simples e tecnologias gratuitas, operáveis sem equipe de TI |
| RNF09 | Implantação | Adoção em fases, sem interromper a operação |

### 4.3 Stakeholders

Identificamos grupos de interesse e um ponto de contato próprio na solução para cada um:

| Stakeholder | Necessidade principal | Como usa a solução |
|---|---|---|
| Lojista | Pedir sem retrabalho e saber onde está a entrega | Portal: pedido validado e rastreio por código |
| Destinatário | Receber no prazo | Link de rastreio; informa seu nome na entrega |
| Atendimento | Responder sem depender da Operação | Painel: busca por código ou cliente e avisos em lote |
| Analistas de Operação | Parar de reconciliar planilhas | Painel: distribuição, importação de planilha, lançamento manual |
| Coordenador | Regras claras e visão do dia | Painel: fila de ocorrências e indicadores |
| Financeiro | Dados fechados e confiáveis | Painel: fechamento por cliente e conciliação por parceira |
| Empresas parceiras | Informar o andamento do jeito que já trabalham | Planilha padrão (Refactor Express) ou link por entrega (Fretes do Seu Zé) |
| Direção da SEALog | Custo baixo, sem parar a operação | Implantação em fases |

---

## 5. Arquitetura

Adotamos um **monólito modular em camadas**: uma única aplicação, que atende várias telas por uma API REST.

**Por que não microsserviços?** A SEALog não tem equipe de TI, nosso prazo era de 4 dias e o problema é falta de fonte única de verdade, não volume. Microsserviços só somariam custo e complexidade.

### 5.1 Modelos combinados

| Modelo | Como aparece na solução | Por quê |
|---|---|---|
| Camadas | Apresentação → regras de negócio → dados | A regra "entrega só fecha com comprovante" vale igual para link, planilha ou telefone |
| Cliente-servidor | As telas pedem; um servidor responde | Todos enxergam o mesmo dado no mesmo instante |
| Baseada em API | Portal, painel e link falam com a mesma API REST (JSON) | Uma tela nova entra sem mexer no resto |

### 5.2 Estrutura

```
FRONT-END WEB                BACK-END (uma aplicação, um deploy)        DADOS E SERVIÇOS
┌──────────────────┐         ┌──────────────────────────────────┐       ┌────────────────┐
│ Login            │         │ COMUNICAÇÃO                      │       │ PostgreSQL     │
│ Painel interno   │         │ API REST, JSON e adaptadores     │       │ (ID único,     │
│ Portal do lojista│ ──────▶ │ de entrada                       │ ────▶ │ administração) │
│ Rastreio (sem    │         ├──────────────────────────────────┤       ├────────────────┤
│ login)           │         │ NEGÓCIO: módulos + notificações  │       │ WhatsApp       │
│ Planilha de      │         │ Auth · Pedidos · Entregas        │       │ (entrada e     │
│ entrega (parceira)│        │ Financeiro · Distribuição ·      │       │ avisos)        │
└──────────────────┘         │ Ocorrências                      │       └────────────────┘
                             ├──────────────────────────────────┤
                             │ ACESSO A DADOS                   │
                             │ Repositórios sobre o banco       │
                             └──────────────────────────────────┘
```

### 5.3 Módulos

| Módulo | Responsabilidade |
|---|---|
| Auth e perfis | Login e permissões por perfil |
| Pedidos | Recebe e valida pedidos; só vira entrega com todos os campos obrigatórios |
| Entregas | Dono do ciclo de status e do histórico; é a fonte única de verdade |
| Distribuição | Atribui cada entrega a uma parceira por região e capacidade |
| Parceiras | Cadastro, canal de contato e indicadores |
| Ocorrências | Registra falhas e a decisão do coordenador |
| Notificações | Avisa a Operação e os lojistas sobre mudanças e atrasos |
| Financeiro | Fechamento por cliente e conciliação por parceira |

### 5.4 Tecnologias

| Escolha | Justificativa |
|---|---|
| Aplicação web responsiva | Funciona em qualquer celular sem instalar nada |
| API REST com JSON | Padrão amplamente usado; facilita integrações futuras |
| PostgreSQL | Dados muito relacionados (pedido, entrega, parceira, pagamento) que precisam ser consistentes; gratuito |
| Armazenamento de arquivos separado | Fotos ficam fora do banco, referenciadas pelo código da entrega |
| Link comum enviado pelo WhatsApp | Não depende da API paga do WhatsApp Business |
| Framework do backend em aberto | Depende do que o time de desenvolvimento domina; a arquitetura não muda |

---

## 6. Backlog e priorização

O backlog tem **17 histórias de usuário**, **5 tarefas técnicas** e **3 melhorias futuras**.

### 6.1 Histórias de usuário

| ID | História | Requisitos | Prioridade |
|---|---|---|---|
| HU01 | Como atendente, quero registrar um pedido com campos obrigatórios validados, para não encaminhar pedido incompleto | RF01 | Must |
| HU02 | Como analista, quero uma base única de entregas com status, para parar de reconciliar planilhas | RF04 | Must |
| HU03 | Como analista, quero atribuir cada entrega a uma parceira e poder trocá-la | RF05, RF16 | Must |
| HU04 | Como analista, quero importar a planilha padrão da parceira e ver as linhas com erro | RF03 | Must |
| HU05 | Como analista, quero lançar uma atualização recebida por telefone, com a origem registrada | RF07 | Must |
| HU06 | Como analista, quero anexar o comprovante à entrega, para resolver disputas | RF08 | Must |
| HU07 | Como analista, quero ser alertado na hora de uma ocorrência, para agir antes de o cliente reclamar | RF09 | Must |
| HU08 | Como atendente, quero buscar uma entrega e ver status, comprovante e histórico, sem perguntar à Operação | RF10 | Must |
| HU09 | Como coordenador, quero decidir cada ocorrência a partir de motivos padronizados | RF13 | Must |
| HU10 | Como financeiro, quero exportar o fechamento do dia por cliente | RF14 | Must |

### 6.2 Tarefas técnicas

| ID | Tarefa | Prioridade |
|---|---|---|
| TT01 | Modelar o banco: pedidos, entregas, histórico de status, parceiras | Must |
| TT02 | Definir o modelo de planilha padrão para as parceiras | Must |
| TT03 | Configurar o armazenamento de fotos ligado ao código da entrega | Must |
| TT04 | Criar perfis de acesso: Atendimento, Operação, Coordenação, Financeiro, Lojista | Must |
| TT05 | Preparar o ambiente de hospedagem da aplicação | Must |

### 6.3 Melhorias futuras

| ID | Melhoria | Prioridade | Motivo |
|---|---|---|---|
| M01 | Envio automático pela API do WhatsApp Business | Could | O link comum já resolve, sem custo nem aprovação da Meta |
| M02 | Otimização de rotas | Won't | A rota passou a ser responsabilidade da parceira |
| M03 | Chat com entregador e GPS em tempo real | Won't | Recriaria o grupo de WhatsApp; nenhuma dor do enunciado pede isso |

### 6.4 Método de priorização

- **MoSCoW** classificou cada item: Must, Should, Could e Won't.
- **Valor vs. esforço** desempatou dentro de cada grupo: entrou primeiro o que ataca a causa raiz (base única e consulta) com menor esforço.
- **Critério de corte:** item sem dor descrita no enunciado não entra no MVP.

### 6.5 MVP

> Um pedido válido, atribuído a uma parceira, entregue com comprovante e consultável pelo Atendimento sem perguntar a ninguém.

O MVP corresponde às histórias **Must (HU01 a HU10)** e às **5 tarefas técnicas**.

### 6.6 Implantação em fases (sem parar a operação)

| Fase | O que entra | Itens | Resultado esperado |
|---|---|---|---|
| **0** | Planilha única padronizada, com código por entrega e modelo para as parceiras | TT02 | Fim das cópias divergentes, ainda sem sistema |
| **1 (MVP)** | Painel interno, base única, distribuição, importação, lançamento manual, ocorrências e fechamento | HU01–HU10, TT01, TT03–TT05 | "Cadê minha entrega?" respondido no painel |
| **2** | Link para as parceiras, portal do lojista, avisos em lote e etiquetas | HU11–HU15 | Status chega sem digitação; lojista consulta sozinho |
| **3** | Indicadores e conciliação das parceiras | HU16, HU17 | Base para pagar e avaliar as parceiras |

---

## 7. Modelagem e protótipo

Usamos três modelos: **fluxo de atividades (BPMN)** para entender o processo, **wireframes** para a interface e **diagrama de blocos** para a estrutura.

### 7.1 Fluxo de atividades

Modelamos o processo atual (as-is) em BPMN, com duas visões: o ciclo completo de uma entrega e a consulta "cadê minha entrega?". Os pontos de dor ficaram marcados no próprio diagrama e deram origem aos requisitos. No fluxo proposto (to-be), a SEALog e cada parceira aparecem em raias separadas, trocando informações pelo sistema em vez do grupo de WhatsApp.

### 7.2 Protótipo (Figma)

Fizemos uma tela para cada stakeholder que usa o sistema:

| Tela | Quem usa | Decisão de design | Por quê |
|---|---|---|---|
| Login | Todos | Acesso por perfil, com opção de acompanhar uma entrega | Entrada única, rastreio sem conta |
| Painel interno | Atendimento e Operação | Busca no topo; status colorido em cada linha | Responder "cadê minha entrega?" num relance |
| Fila de ocorrências / painel do coordenador | Coordenador | Visão geral do dia, status das parceiras e itens que precisam de atenção | Torna a regra de exceção visível |
| Link de atualização | Entregador da parceira | Sem login; botões grandes "Entregue" e "Não entregue" | Uso rápido no celular, inclusive por quem tem pouca familiaridade com tecnologia |
| Portal do lojista | Loja cliente | Formulário com campos obrigatórios e rastreio por código | Acaba com o pedido incompleto |
| Área do financeiro | Financeiro | Cobranças por cliente e repasses às parceiras | Entrega confirmada vale dinheiro dos dois lados |

Prints e arquivos do protótipo estão na pasta de materiais e no Notion.

### 7.3 Identidade visual

A paleta segue a identidade da SEALog. As cores de status são fixas em todas as telas, e cada status também tem rótulo escrito para não depender só da cor.

| Cor | Uso | Hex |
|---|---|---|
| Primária | Cabeçalhos, botões principais, marca | `#131936` |
| Secundária | Menus e elementos de apoio | `#13283F` |
| Fundo e texto | Superfícies neutras e texto de alto contraste | `#141B2D` / `#FFFFFF` |
| Entregue | Entrega concluída | `#086239` |
| Em andamento | Atribuída, coletada ou em rota | `#FF8D28` |
| Ocorrência | Aguardando decisão | `#F3EFD6` |
| Atraso ou falha | Não entregue ou atrasada | `#FF383C` |

---

##8. Como nos adaptamos à mudança

Como a arquitetura já era modular, os módulos **Pedidos** e **Entregas** ficaram intactos. O que mudou foi a **forma como o status entra no sistema**.

| Antes | Depois | Por quê |
|---|---|---|
| App do motorista como peça central | Um canal por parceira: planilha padrão, link por entrega ou lançamento manual | Cada parceira informa o andamento do seu jeito |
| Módulo Rotas | Módulo Distribuição, que escolhe a parceira de cada entrega | A rota passou a ser da parceira |
| Hipótese "os motoristas têm smartphone" | Retirada | Os entregadores agora são terceirizados |
| Comprovante tirado pelo nosso motorista | Comprovação em caso de disputa como requisito explícito (RF08) | Ponto em aberto do comunicado |
| Financeiro só cobra os lojistas | Financeiro também concilia o pagamento das parceiras (RF17) | A entrega confirmada vale dinheiro dos dois lados |

**Evolução ao longo do desafio** (visto no pitch):

| Dimensão | Antes | Depois |
|---|---|---|
| Arquitetura | Gateway, banco de dados e storage de fotos | Monólito modular com um único banco relacional (PostgreSQL) |
| Escopo | Sistema completo, com app do motorista, roteiro e rotas | Menos telas, mais foco: centralizar o dado e deixar a automação para as próximas fases |
| Atores | Motorista atualiza o status direto no app | Parceira informa por WhatsApp, telefone, aplicação ou planilha; a SEALog registra e centraliza |

Antes mesmo da mudança, já tínhamos feito uma **revisão interna de escopo**: cortamos funções sem dor no enunciado (chat com motorista, GPS em tempo real, botão de perigo), unificamos duas versões conflitantes de disponibilidade e subimos o fechamento financeiro para Must.

Também levamos em conta o ponto de atenção do cliente sobre a **Fretes do Seu Zé**: o caminho de menor atrito para ela é telefone e WhatsApp, com o analista da SEALog lançando a atualização no sistema com a origem registrada.

---

## 9. Registro de decisões

| ID | Decisão | Origem |
|---|---|---|
| D01 | Notion como quadro único; Discord para registrar as chamadas | Equipe |
| D02 | Modelar o fluxo atual antes de propor a solução | Equipe |
| D03 | Cortar funções sem dor no enunciado | Revisão de escopo |
| D04 | Disponibilidade no horário de operação, não 24/7 | Revisão de escopo |
| D05 | Fechamento financeiro como Must | Revisão de escopo |
| D06 | Monólito modular em camadas, com API REST | Equipe |
| D07 | Aplicação web responsiva em vez de app nativo | Equipe |
| D08 | Destinatário sem conta no sistema | Hipótese |
| D09 | Canais de entrada por parceira no lugar do app do motorista | Mudança do cliente |
| D10 | Rotas vira Distribuição | Mudança do cliente |
| D11 | Implantação em fases, sem parar a operação | Mudança do cliente |
| D12 | Paleta derivada da identidade visual da SEALog, com cores fixas por status | Equipe |

---

## 10. Organização do trabalho e ferramentas

| Ferramenta | Uso |
|---|---|
| **Discord** | Chamadas, contato com o cliente e registro das reuniões |
| **Notion** | Tarefas, decisões, perguntas e backlog |
| **Figma** | Protótipo das telas |
| **Canva** | Slides do pitch |
| **draw.io** | BPMN |
| **Claude (IA)** | Apoio (ver seção seguinte) |

**Como trabalhamos:**

- Entendimento do problema, requisitos e ideias de solução em **chamadas conjuntas** com toda a equipe.
- Demais entregas divididas por frentes (processo, arquitetura, protótipo, apresentação).
- Após cada chamada sem a presença da SEAL, registramos no chat da equipe os pontos discutidos, as decisões e os responsáveis, como orientava o enunciado.
- Quadro de tarefas no Notion com frente, prazo e status (investigação, requisitos, modelagem, wireframes, arquitetura, apresentação e organização).

### Linha do tempo

| Data | O que aconteceu |
|---|---|
| 26/10 | Leitura do enunciado e material de apoio; combinação de papéis e canal de registro |
| 27/10 | Primeiras perguntas ao cliente; fechamento de requisitos e backlog em MoSCoW |
| 28/10 | Modelagem do fluxo atual e proposto; wireframes; diagrama de blocos da arquitetura |
| 28–29/10 | Recebimento do comunicado de mudança dos requisitos e revisão da proposta |
| 29/10 | Slides com fala para os 6; ensaio do pitch de 12 min; simulação de perguntas; envio do material no Discord (até 23h59) |
| 30/10 | Apresentação online: 12 min de pitch + até 8 min de perguntas |
| 02/11 | Divulgação do resultado da segunda etapa |

---

## 11. Uso de inteligência artificial

Usamos IA (Claude) como **apoio**, nunca como fonte das decisões. Ela ajudou a:

- organizar o enunciado num ponto de partida;
- gerar primeiras versões de diagramas e documentos;
- revisar a proposta em busca de contradições e excesso de escopo;
- simular perguntas da banca;
- apoiar o entendimento de conceitos técnicos.

A equipe revisou cada resultado, cortou o que não tinha base no enunciado e decidiu as prioridades. Qualquer integrante consegue explicar as escolhas registradas aqui.

---

## 12. Materiais produzidos

| Material | Descrição |
|---|---|
| Documento de entrega | Documento completo com entendimento, requisitos, arquitetura, backlog, modelagem, evolução e decisões |
| Slides do pitch | Apresentação de 12 slides usada na apresentação final |
| Protótipo (Figma) | Telas de login, cliente, atendente, operação, coordenador, financeiro e parceiras |
| BPMN | Fluxo as-is (ciclo da entrega e consulta "cadê minha entrega?") e proposta to-be |
| Notion | Tarefas, backlog, decisões e perguntas ao cliente |
| Enunciado e comunicado de mudança | Documentos originais fornecidos pela SEAL |

> 📎 **Notion do projeto:** https://app.notion.com/p/Processo-Seletivo-SEAL-2026-2-405448b2a4ac81bea01be359bec22ad9
>
> 🎨 **Protótipo no Figma:** https://www.figma.com/design/NieBXnNWGp6fu9skt9SvR5/Design-SeaLog-Equipe-3?node-id=0-1&p=f&t=uls3WHNbB89N49fS-0
>
> 📊 **Slides:** https://www.canva.com/design/DAHZQrPr8Po/rBeCjOA3Vp4t3dQuRsEHrw/edit

---

## 13. Aprendizados

- **Antes de propor solução, entender o problema.** Modelar o fluxo atual deixou claro que a causa raiz era a falta de fonte única de verdade, e isso orientou todo o resto.
- **Cortar escopo também é decisão de engenharia.** Remover funções sem dor no enunciado manteve a proposta coerente e viável.
- **Arquitetura modular facilita mudança.** Quando o cliente mudou o modelo de operação, os módulos centrais continuaram válidos; trocamos apenas a forma de entrada dos dados.
- **Mudança de requisito em cima da hora é normal.** O importante é revisar, explicar o que continua fazendo sentido, o que não faz mais e registrar o porquê.
- **Pensar em quem vai usar.** A Fretes do Seu Zé, com pouca familiaridade tecnológica, mostrou que a solução precisa aceitar canais diferentes, e não impor um único.
- **Registrar decisões e contribuições** em equipe torna o trabalho rastreável e facilita explicar as escolhas durante as perguntas da banca.

---

*Desafio Prático em Grupo · Processo Seletivo SEAL 2026.2 · Liga Acadêmica de Engenharia de Software · CIn-UFPE*
