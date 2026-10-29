<div align="center">

# ⚖️ Lab 05 – Auto Scaling e Elastic Load Balancer

**Laboratório prático: ambiente web altamente disponível e escalável com Launch Template, Auto Scaling Group e Application Load Balancer**

![EC2](https://img.shields.io/badge/Amazon%20EC2-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Auto Scaling](https://img.shields.io/badge/EC2%20Auto%20Scaling-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![ELB](https://img.shields.io/badge/Elastic%20Load%20Balancing-8C4FFF?style=flat-square&logo=amazonaws&logoColor=white) ![Tema](https://img.shields.io/badge/Tema-Alta%20Disponibilidade%20e%20Escalabilidade-2E7D32?style=flat-square) ![Status](https://img.shields.io/badge/Status-Conclu%C3%ADdo-2E7D32?style=flat-square)

![Objetivo](https://img.shields.io/badge/Objetivo-AWS%20Certified%20Solutions%20Architect%20Associate-232F3E?style=flat-square&logo=amazonaws&logoColor=white) ![Origem](https://img.shields.io/badge/Escola%20da%20Nuvem%20%E2%80%93%20Turma%20C10%20OUT%20%E2%80%93%20DPCN%2019-0A66C2?style=flat-square) ![Idioma](https://img.shields.io/badge/Idioma-Portugu%C3%AAs%20(PT--BR)-232F3E?style=flat-square)

</div>

---

## 📑 Sumário

- [📖 Sobre o Projeto](#-sobre-o-projeto)
- [🎯 Objetivos](#-objetivos)
- [🧩 Cenário](#-cenário)
- [🏗️ Arquitetura](#-arquitetura)
- [✅ Pré-requisitos](#-pré-requisitos)
- [📊 Resultado](#-resultado)
- [🔍 Validação](#-validação)
- [💡 Principais Aprendizados](#-principais-aprendizados)
- [🧹 Limpeza dos Recursos](#-limpeza-dos-recursos)
- [☁️ Serviços Abordados](#-serviços-abordados)
- [👤 Autor](#-autor)

---

## 📖 Sobre o Projeto

Este laboratório faz parte da minha preparação para a certificação **AWS Certified Solutions Architect – Associate** e foi realizado no programa **Escola da Nuvem (Turma C10 OUT – DPCN 19)**. A proposta é montar um ambiente web básico que continue no ar mesmo se uma instância falhar ou se o tráfego variar, combinando três peças: um **Launch Template** (a "receita" das instâncias), um **Auto Scaling Group** (quem cria e mantém as instâncias) e um **Application Load Balancer** (quem distribui as requisições entre elas).

> 💬 **Uma única instância EC2 é um ponto único de falha; com Launch Template + ASG + ALB, a aplicação passa a se recuperar sozinha e a espalhar o tráfego.**

---

## 🎯 Objetivos

- 🌐 Usar a VPC padrão da região `us-east-1` e confirmar suas sub-redes públicas.
- 🛡️ Criar um Security Group liberando HTTP e HTTPS.
- 📋 Criar um Launch Template com um script de inicialização (User Data) que sobe um servidor web.
- 📈 Criar um Auto Scaling Group distribuído em mais de uma zona de disponibilidade.
- ⚖️ Criar um Application Load Balancer voltado para a internet e integrá-lo ao ASG.
- 🧪 Testar o balanceamento e conferir a saúde das instâncias.

---

## 🧩 Cenário

Uma aplicação web precisa ficar sempre disponível, mesmo com picos de acesso ou queda de instâncias individuais. A solução é definir o modelo das instâncias uma única vez, deixar o ASG manter a quantidade desejada em execução e colocar o ALB à frente para enviar tráfego apenas às instâncias saudáveis.

| Parâmetro | Valor |
|---|---|
| Região | `us-east-1` (N. Virginia) |
| Rede | VPC padrão, com sub-redes públicas em pelo menos 2 AZs |
| Security Group | Nome sugerido no roteiro: `SG-Lab-<NomeSobrenome>`; entrada HTTP e HTTPS com origem Anywhere-IPv4; saída padrão |
| Launch Template | `LaunchTemplates_LucasBarretto` (versão padrão 1) |
| AMI / tipo de instância | Amazon Linux 2023 (conforme o roteiro) / `t2.micro` |
| Key pair | Não incluído no template (sem acesso SSH neste lab) |
| Armazenamento | Padrão (8 GiB) |
| User Data | Atualiza pacotes, instala e habilita o Apache (httpd) e cria uma página que exibe o hostname da instância |
| Auto Scaling Group | `ASG-LucasBarretto` |
| Capacidade (desejada / mín. / máx.) | 2 / 2 / 2 |
| Políticas de escala | Nenhuma |
| Load Balancer | Application Load Balancer, *internet-facing*, listener HTTP:80, novo Target Group |
| Health checks | Health checks do Elastic Load Balancing ativados no ASG |
| Tag das instâncias | `Name = WebServer<NomeSobrenome>` (conforme o roteiro) |

---

## 🏗️ Arquitetura

```mermaid
flowchart TB
    U["👤 Usuário<br/>(Internet)"] -->|"HTTP :80"| IGW["🚪 Internet Gateway"]
    subgraph VPC["🌐 VPC padrão – us-east-1"]
        IGW --> ALB["⚖️ Application Load Balancer<br/>internet-facing"]
        ALB --> TG["🎯 Target Group<br/>health checks"]
        subgraph AZA["📍 Zona de disponibilidade A"]
            EC1["🖥️ EC2 Web 1<br/>t2.micro + Apache"]
        end
        subgraph AZB["📍 Zona de disponibilidade B"]
            EC2["🖥️ EC2 Web 2<br/>t2.micro + Apache"]
        end
        TG --> EC1
        TG --> EC2
        LT["📋 Launch Template"] -.->|"modelo"| ASG["📈 Auto Scaling Group<br/>desejada 2 / mín. 2 / máx. 2"]
        ASG -.->|"cria e mantém"| EC1
        ASG -.->|"cria e mantém"| EC2
        SG["🛡️ Security Group<br/>HTTP / HTTPS"] -.-> EC1
        SG -.-> EC2
    end
```

O usuário acessa o DNS do Application Load Balancer pela internet. O ALB recebe a requisição no listener HTTP:80 e a encaminha, via Target Group, para uma das instâncias saudáveis. As instâncias são criadas pelo Auto Scaling Group a partir do Launch Template, em sub-redes de zonas de disponibilidade diferentes: se uma instância falhar, o ASG a substitui, e o ALB deixa de enviar tráfego para ela enquanto o health check indicar problema. Cada instância exibe seu próprio hostname, o que permite perceber o revezamento ao atualizar a página.

---

## ✅ Pré-requisitos

- ☁️ Conta AWS ativa.
- 🔑 Permissões IAM: `AmazonEC2FullAccess`, `AmazonEC2AutoScalingFullAccess` e `ElasticLoadBalancingFullAccess`.
- 🌍 Console da AWS com a região **N. Virginia (`us-east-1`)** selecionada.
- 💻 Navegador web.

> ⚠️ **Atenção:** o laboratório foi feito em um ambiente de capacitação, cujas permissões podem ser limitadas. Use apenas os recursos e ações previstos no roteiro. O console da AWS também pode mudar visualmente ao longo do tempo, mas os conceitos permanecem.

---

## 📊 Resultado

### 🌐 VPC e sub-redes

O *Resource map* da VPC usada no laboratório mostra a rede, as sub-redes, a tabela de rotas e a conexão com a internet.

<div align="center">

![Resource map da VPC com 6 sub-redes nas zonas us-east-1a a us-east-1f, 1 tabela de rotas e 1 conexão de rede com um Internet Gateway](docs/images/vpc_diagram.png)

*Figura 1: Resource map da VPC, com 6 sub-redes (us-east-1a a us-east-1f), 1 tabela de rotas e 1 conexão de rede (igw-…).*

</div>

### 🛡️ Security Group

### Security Group

<div align="center">

![Security Group com as regras de entrada para HTTP e HTTPS.](docs/images/SG-Lab-LucasBarretto.png)

*Figura 2: Security Group com as regras de entrada para HTTP e HTTPS*

</div>

### 📋 Launch Template

O Launch Template foi criado com versão 1 como padrão e define a AMI e o tipo de instância usados pelo ASG.

<div align="center">

![Detalhes do Launch Template LaunchTemplates_LucasBarretto, com versão padrão 1, AMI e tipo de instância t2.micro](docs/images/LaunchTemplates_LucasBarretto.png)

*Figura 2: Launch Template `LaunchTemplates_LucasBarretto`, com versão padrão e mais recente 1, AMI definida e tipo de instância `t2.micro`.*

</div>

### 📈 Auto Scaling Group

O ASG foi criado com capacidade desejada 2 e limites de escala 2–2, usando o Launch Template acima.

<div align="center">

![Detalhes do Auto Scaling Group ASG-LucasBarretto com capacidade desejada 2, limites 2 a 2, status Updating capacity e 1 de 1 instância saudável](docs/images/ASG-LucasBarretto.png)

*Figura 3: Auto Scaling Group `ASG-LucasBarretto`, capturado com status "Updating capacity": capacidade desejada 2, limites 2–2 e 1 instância (1/1 saudável) já em execução.*

</div>

### ⚖️ Application Load Balancer

O Load Balancer foi criado junto com o ASG, durante o assistente de criação.

<div align="center">

![Detalhes do Application Load Balancer ASG-LucasBarretto-1, ativo, internet-facing, distribuído em duas zonas de disponibilidade](docs/images/ASG-LucasBarretto-1.png)

*Figura 4: Load Balancer `ASG-LucasBarretto-1`, do tipo Application, internet-facing, com estado Active, IPv4 e duas zonas de disponibilidade (us-east-1f e us-east-1c).*

</div>

### 🧪 Teste de Balanceamento de Carga


<div align="center">

![Navegador acessando o nome DNS do Load Balancer, mostrando a página servida pela primeira instância.](docs/images/LoadBalancing-Test-1.png)

*Navegador acessando o nome DNS do Load Balancer, mostrando a página servida pela primeira instância.*

![Navegador acessando o nome DNS do Load Balancer após atualizar, mostrando a página servida por uma instância diferente.](docs/images/LoadBalancing-Test-2.png)

*Figura 7: Navegador acessando o nome DNS do Load Balancer após atualizar, mostrando a página servida por uma instância diferente.*

</div>

---

## 🔍 Validação

| ✔️ | Item | Resultado esperado |
|---|---|---|
| ✔️ | VPC padrão em `us-east-1` | Sub-redes em várias AZs e rota para um Internet Gateway (Figura 1) |
| ✔️ | Security Group | Entrada HTTP e HTTPS liberada para Anywhere-IPv4 |
| ✔️ | Launch Template | Criado com versão padrão 1 e instância `t2.micro` (Figura 2) |
| ✔️ | Auto Scaling Group | Capacidade desejada, mínima e máxima iguais a 2 (Figura 3) |
| ✔️ | Instâncias | Saudáveis (*healthy*) no ASG e no Target Group, após cerca de 5 minutos |
| ✔️ | Application Load Balancer | Estado Active, *internet-facing*, em 2 AZs (Figura 4) |
| ✔️ | Acesso pelo DNS do ALB | A página "Servidor Web" é exibida e o hostname muda ao atualizar |

---

## 💡 Principais Aprendizados

- 🏠 **Launch Template como "receita":** define AMI, tipo, Security Group e User Data uma única vez, garantindo instâncias idênticas.
- 🔁 **ASG mantém a capacidade:** se uma instância falha, ele cria outra; com mínimo e máximo iguais, o grupo fica fixo, mas pronto para receber políticas de escala.
- 🌍 **Sub-redes em AZs diferentes:** é isso que dá alta disponibilidade, pois a queda de uma zona não derruba tudo.
- ⚖️ **ALB distribui e verifica a saúde:** só envia tráfego a alvos saudáveis, registrados no Target Group.
- 🩺 **Health checks do ELB no ASG:** permitem que o balanceador avise o ASG para substituir instâncias que não respondem.
- 🧰 **User Data automatiza a configuração:** cada instância nova já nasce com o servidor web pronto.
- 🧠 **Relevância para a prova:** tema recorrente no SAA — alta disponibilidade e elasticidade com ASG + ELB em múltiplas AZs, ALB para tráfego HTTP/HTTPS (camada 7) com Target Groups e health checks, e Launch Templates como forma de definir as instâncias do ASG.

---

## 🧹 Limpeza dos Recursos

Load Balancer e instâncias EC2 geram custo enquanto existirem. Ordem recomendada pelo roteiro:

- [ ] Excluir o **Auto Scaling Group** (isso encerra automaticamente as instâncias EC2 gerenciadas por ele)
- [ ] Excluir o **Load Balancer**
- [ ] Excluir o **Target Group**
- [ ] Excluir o **Launch Template**
- [ ] Excluir o **Security Group**

---

## ☁️ Serviços Abordados

![EC2](https://img.shields.io/badge/Amazon%20EC2-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Auto Scaling](https://img.shields.io/badge/EC2%20Auto%20Scaling-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![ELB](https://img.shields.io/badge/Elastic%20Load%20Balancing-8C4FFF?style=flat-square&logo=amazonaws&logoColor=white) ![VPC](https://img.shields.io/badge/Amazon%20VPC-8C4FFF?style=flat-square&logo=amazonaws&logoColor=white)

| Serviço | Papel no laboratório |
|---|---|
| Amazon EC2 | Executa os servidores web e abriga Launch Templates e Security Groups |
| Amazon EC2 Auto Scaling | Cria e mantém o número desejado de instâncias, substituindo as que falham |
| Elastic Load Balancing (ALB) | Distribui o tráfego entre as instâncias saudáveis via Target Group |
| Amazon VPC | Fornece a rede padrão, as sub-redes públicas e a saída para a internet |

---

## 👤 Autor

**Lucas Barretto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Lucas%20Barretto-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucaspimentabarretto) [![GitHub](https://img.shields.io/badge/GitHub-lbarretto-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/lbarretto)

---

<div align="center">

Laboratório realizado como parte do programa Escola da Nuvem, Turma C10 OUT – DPCN 19 🚀

</div>
