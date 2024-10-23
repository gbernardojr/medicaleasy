Montar uma estrutura de micro SaaS com API Gateway e serviços de cadastro, como o que você está desenvolvendo, envolve diversas considerações de hardware e arquitetura. Vamos analisar o que você precisará, quantos servidores serão necessários, quais configurações utilizar, e como uma estrutura on-premises se compara a uma solução em nuvem.

### 1. **Estrutura Necessária**

#### 1.1. **Serviços e Componentes**

Com base no que discutimos, você terá os seguintes componentes:

- **API Gateway (Kong)**
- **Serviço de Cadastro de Pacientes**
- **Serviço de Cadastro de Medicamentos**
- **Banco de Dados (MongoDB)**

#### 1.2. **Requisitos de Hardware**

**Mínimos Recomendados:**

1. **API Gateway**:  
   - **Servidor**: 1 servidor
   - **CPU**: 2 vCPUs
   - **RAM**: 4 GB
   - **Armazenamento**: 20 GB SSD

2. **Serviços (Pacientes e Medicamentos)**:  
   - **Servidores**: 2 servidores (um para cada serviço)
   - **CPU**: 2 vCPUs cada
   - **RAM**: 4 GB cada
   - **Armazenamento**: 20 GB SSD cada

3. **Banco de Dados**:  
   - **Servidor**: 1 servidor
   - **CPU**: 4 vCPUs
   - **RAM**: 8 GB
   - **Armazenamento**: 100 GB SSD (dependendo do volume de dados)

### 2. **Total de Servidores e Configurações**

- **Servidores Físicos/Virtualizados**:
  - Você pode optar por **1 servidor físico potente** que virtualize todos os serviços (API Gateway, serviços de cadastro, e banco de dados) usando uma plataforma de virtualização como VMware, Proxmox ou Hyper-V.
  - Ou utilizar **servidores físicos separados** para cada componente, onde:
    - 1 para API Gateway
    - 2 para os serviços (pacientes e medicamentos)
    - 1 para o banco de dados

### 3. **Configurações de Servidores**

**Físicos**:
- Idealmente, se você tiver recursos, use servidores dedicados para o banco de dados para garantir desempenho e evitar contenda com os serviços.

**Virtualizados**:
- Se optar por virtualizar, assegure-se de que o servidor físico tenha recursos suficientes para suportar várias máquinas virtuais (VMs) sem degradação de desempenho.

### 4. **Comparação: On-Premises vs. Cloud**

| Aspecto              | On-Premises                                          | Cloud                                            |
|---------------------|-----------------------------------------------------|-------------------------------------------------|
| **Custo Inicial**   | Alto (compra de hardware e infraestrutura)         | Baixo (pagamento conforme uso)                  |
| **Escalabilidade**  | Difícil e caro (nova compra de hardware necessário)| Fácil (adiciona recursos sob demanda)            |
| **Manutenção**      | Necessário equipe de TI interna                     | Fornecido pelo provedor (menor sobrecarga)     |
| **Desempenho**      | Controlável com hardware de alta performance        | Pode variar dependendo da carga e localização   |
| **Segurança**       | Total controle, mas responsabilidade de gerenciamento| Segurança gerenciada pelo provedor, mas risco compartilhado |
| **Implementação**   | Demora (configuração e instalação)                 | Rápido (pronto para uso em minutos)             |
| **Atualizações**    | Necessário gerenciamento e implementação manuais    | Automáticas e gerenciadas pelo provedor         |

### 5. **Conclusão**

A escolha entre uma infraestrutura on-premises e uma solução em nuvem depende das necessidades específicas do seu projeto, orçamento, e preferências em termos de gerenciamento de TI. 

- **On-Premises** oferece controle total, mas requer investimentos maiores em hardware e manutenção contínua.
- **Cloud** proporciona flexibilidade e escalabilidade, permitindo que você comece pequeno e expanda à medida que seu serviço cresce, além de simplificar a manutenção e as atualizações.

Se você tem uma equipe de TI qualificada e orçamento para hardware, uma solução on-premises pode ser atraente. No entanto, se você busca agilidade e menor investimento inicial, a nuvem é geralmente a melhor opção. Se precisar de mais informações ou detalhes, estou à disposição para ajudar!