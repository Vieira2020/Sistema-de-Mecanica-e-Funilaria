# Sistema de Mecânica(Shibuya Motores)
## Cenário
**Shibuya Motores** é uma oficina mecânica localizada em Bragança Paulista, SP, que opera de forma integrada com uma **funilaria** para reparos e restauração de lataria.

A oficina atende veículos que demandam tanto serviços mecânicos (motor, suspensão, elétrica) quanto serviços de funilaria (reparo de carroceria, pintura, restauração). Um caso típico é o de um veículo envolvido em colisão: o cliente precisa reparar a estrutura da carroceria na funilaria **e** resolver danos mecânicos na oficina, tudo dentro de um mesmo processo de atendimento.

Atualmente, o controle é feito de forma manual ou dispersa, o que dificulta o acompanhamento do status dos veículos, a rastreabilidade das peças e a comunicação com o cliente sobre prazos e valores.
## Problema a ser resolvido
  1 | **Risco legal com placas de veículos:** 
  Não há registro formal de consulta/verificação da placa antes do início dos serviços. Exposição a problemas com a polícia (receptação, veículos com restrição).  
 2 | **Falta de rastreabilidade dos serviços:** Não se sabe, em tempo real, em qual setor (mecânica ou funilaria) o veículo está e quem é o responsável técnico.                          Atrasos na entrega, reclamações de clientes e perda de produtividade.      |
3 | **Gestão descentralizada de peças e valores:** Orçamentos de peças são feitos "de cabeça" ou em papéis soltos, sem comparação de preços nem controle do que foi comprado vs. usado. | Perda de margem de lucro e dificuldade para justificar valores ao cliente. |
| 4 | **Comunicação fragmentada com o cliente:** O cliente liga várias vezes para saber se o carro está pronto.                                                                           | Perda de tempo da equipe e insatisfação do cliente.                        |
| 5 | **Ausência de histórico técnico:** Não há registro estruturado do que foi feito em cada veículo em atendimentos anteriores.                                                         | Dificuldade para diagnosticar problemas recorrentes e fidelizar clientes.  |

## Escopo
Desenvolver um **sistema web de gestão de Ordens de Serviço (OS)** para a Shibuya Motores, com foco em:

-   **Rastreabilidade completa** do veículo desde a entrada na oficina até a entrega ao cliente;
    
-   **Controle hierárquico de acesso**, onde o dono tem visão administrativa completa e os mecânicos têm acesso operacional limitado;
    
-   **Gestão de peças** com tabela de preços de referência e vinculação direta às OS;
    
-   **Registro de atualizações técnicas** com identificação de quem fez o quê e quando;
    
-   **Segurança e auditoria** no registro de placas de veículos.
### Requisitos: O que a solução precisa fazer?
### 4.1. Autenticação e Controle de Acesso

-   **RF-001:** O sistema deve possuir login com diferentes perfis de usuário:
    
    -   **Administrador (Dono):** acesso total a todas as funcionalidades e dados.
        
    -   **Mecânico / Funileiro:** acesso limitado às informações operacionais.
        
-   **RF-002:** O perfil Administrador visualiza: nome completo, telefone, CPF, endereço (opcional), placa, modelo do veículo, valores de peças, mão de obra e histórico financeiro.
    
-   **RF-003:** O perfil Mecânico visualiza apenas: nome do cliente, placa do veículo, modelo do veículo, status da OS, descrição dos serviços técnicos e campo para registrar atualizações. **Não visualiza valores, CPF, endereço ou telefone.**
    
-   **RF-004:** Todas as ações de criação, edição ou mudança de status devem registrar automaticamente o **usuário logado** e a **data/hora**.
    

### 4.2. Gestão de Clientes e Veículos

-   **RF-005:** Cadastro de cliente com os dados: nome, telefone, CPF (opcional), endereço (opcional).
    
-   **RF-006:** Cadastro de veículo vinculado ao cliente: placa, modelo, cor, ano (opcional), observações (opcional).
    
-   **RF-007:** **Registro de verificação de placa:** antes de iniciar qualquer serviço, o sistema deve exigir o registro da placa do veículo, com campo de "consulta realizada" (data, responsável e observação). Esse registro é visível **apenas para o perfil Administrador** e serve como trilha de auditoria interna.
    

### 4.3. Ordem de Serviço (OS)

-   **RF-008:** Criação de OS vinculada a um cliente e a um veículo.
    
-   **RF-009:** A OS deve conter: data de entrada, previsão de entrega, mecânico/funileiro responsável, lista de serviços, lista de peças, status atual e valor total.
    
-   **RF-010:** A OS deve permitir serviços de **mecânica**, **funilaria** ou **ambos**, de forma simultânea.
    
-   **RF-011:** Fluxo de status da OS:
    
    1.  **Recebido** — veículo entregue na oficina;
        
    2.  **Em Diagnóstico** — inspeção técnica inicial;
        
    3.  **Aguardando Aprovação** — orçamento enviado ao cliente;
        
    4.  **Aguardando Peça(s)** — compra de componentes pendentes;
        
    5.  **Em Funilaria** — serviço de lataria em andamento;
        
    6.  **Em Mecânica** — serviço mecânico em andamento;
        
    7.  **Teste / Qualidade** — verificação final dos reparos;
        
    8.  **Pronto para Entrega** — serviços concluídos;
        
    9.  **Entregue** — veículo devolvido ao cliente.
        
-   **RF-012:** Cada mudança de status deve exigir uma observação opcional e gerar automaticamente um registro no **histórico de atualizações** da OS.
    

### 4.4. Controle de Atualizações

-   **RF-013:** Toda OS deve possuir um **feed de atualizações** (linha do tempo) mostrando: quem fez a alteração, data/hora, status anterior → status novo, e observação técnica.
    
-   **RF-014:** Mecânicos podem adicionar atualizações técnicas mesmo sem mudar o status (ex: _"Motor desmontado, identificado problema no cabeçote"_).
    
-   **RF-015:** O histórico de atualizações é **imutável** — não pode ser editado ou apagado.
    

### 4.5. Gestão de Peças e Valores

-   **RF-016:** Catálogo de peças com: nome, código interno, modelo compatível, preço de referência (mínimo, máximo, médio de mercado) e data da última atualização.
    
-   **RF-017:** Vinculação de peças do catálogo à OS, com quantidade, preço unitário naquele momento e fornecedor (quando houver).
    
-   **RF-018:** Controle simplificado de estoque: registrar peças compradas e peças consumidas por OS, permitindo saber o saldo disponível.
    
-   **RF-019:** Tabela de preços de referência para consulta rápida durante o orçamento, servindo como base enquanto o volume de fornecedores cadastrados é baixo.
    
-   **RF-020:** Cálculo automático do valor total da OS (soma de peças + mão de obra).
    

### 4.6. Relatórios e Visões

-   **RF-021:** Dashboard do Administrador com: OS em aberto, OS atrasadas (passou da previsão), faturamento do dia/semana/mês e peças mais utilizadas.
    
-   **RF-022:** Visão em lista dos veículos por status (ex: _"Quais carros estão em funilaria agora?"_).
    
-   **RF-023:** Busca por placa, nome do cliente ou número da OS.
### Ñ Requisitos: O que a solução não precisa fazer?
-   **RNF-001:** O sistema deve ser acessível via navegador web (responsive), podendo ser usado em computadores da oficina e tablets.
    
-   **RNF-002:** Senhas devem ser armazenadas com criptografia (hash).
    
-   **RNF-003:** Backup automático diário do banco de dados.
    
-   **RNF-004:** Interface simples e direta, pensada para uso em ambiente de oficina (mãos possivelmente sujas, pouco tempo para navegação complexa).
    
-   **RNF-005:** O sistema deve funcionar com baixa conectividade (ou ter cache local) caso a internet da oficina oscile.
