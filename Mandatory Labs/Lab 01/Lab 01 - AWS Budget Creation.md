<div align="center">

# 💰 AWS Budgets: Controle de Custos com Alertas por E-mail

**Laboratório prático: criando um orçamento de custos e alertas automáticos na AWS**

![AWS](https://img.shields.io/badge/AWS-Budgets-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Amazon SNS](https://img.shields.io/badge/Amazon-SNS-E7157B?style=flat-square&logo=amazonaws&logoColor=white) ![FinOps](https://img.shields.io/badge/Tema-Gest%C3%A3o%20de%20Custos-2E7D32?style=flat-square) ![Status](https://img.shields.io/badge/Status-Conclu%C3%ADdo-success?style=flat-square)

![Certificação](https://img.shields.io/badge/Objetivo-AWS%20Certified%20Solutions%20Architect%20Associate-232F3E?style=flat-square&logo=amazonaws&logoColor=white) ![Capacitação](https://img.shields.io/badge/Escola%20da%20Nuvem-Turma%20C10%20OUT%20--%20DPCN%2019-0A66C2?style=flat-square) ![Idioma](https://img.shields.io/badge/Idioma-Portugu%C3%AAs%20(BR)-green?style=flat-square)

</div>

---

## 📑 Sumário

- [📖 Sobre o Projeto](#-sobre-o-projeto)
- [🎯 Objetivos](#-objetivos)
- [🧩 Cenário](#-cenário)
- [🏗️ Arquitetura](#️-arquitetura)
- [✅ Pré-requisitos](#-pré-requisitos)
- [📊 Resultado](#-resultado)
- [🔍 Validação](#-validação)
- [💡 Principais Aprendizados](#-principais-aprendizados)
- [🧹 Limpeza e Observações de Custo](#-limpeza-e-observações-de-custo)
- [☁️ Serviços Abordados](#️-serviços-abordados)
- [👤 Autor](#-autor)

---

## 📖 Sobre o Projeto

Este projeto documenta um laboratório prático realizado durante a capacitação da **Escola da Nuvem** (**Turma C10 OUT – DPCN 19**), como parte da minha preparação para a certificação **AWS Certified Solutions Architect – Associate**.

O laboratório mostra como configurar um orçamento de gastos com o **AWS Budgets**, incluindo um **alerta por e-mail** disparado por um limite de uso, e como verificar a configuração criada.

> 💡 Visibilidade de custos é parte essencial de cargas de trabalho bem arquitetadas. Um orçamento com alertas transforma o gerenciamento de custos de **reativo** (descobrir no fim do mês) em **proativo** (agir enquanto o gasto acontece).

---

## 🎯 Objetivos

- 🔎 Acessar o **AWS Budgets** pelo Console de Gerenciamento da AWS
- 🛠️ Criar um orçamento com limite personalizado
- 📧 Configurar alertas por e-mail com base em percentual de uso
- ✅ Validar a criação do orçamento e a configuração das notificações

---

## 🧩 Cenário

Como estudante que utiliza a AWS para aprender e testar, o objetivo é evitar surpresas na fatura e manter os gastos sob controle.

| Parâmetro | Valor |
|---|---|
| 💵 Valor do orçamento | **US$ 10,00** |
| 🔔 Limite do alerta | **10%** do valor orçado (**US$ 1,00**) |
| 📊 Tipo de acionador | Custo **real** |
| 📬 Notificação | E-mail |

---

## 🏗️ Arquitetura

```mermaid
flowchart LR
    A["📊 AWS Budgets"] -->|Notificação| B["📣 Tópico SNS<br/>de alerta de cobrança"]
    B -->|E-mail| C["👥 Usuários"]

    subgraph AWS["☁️ AWS Cloud"]
        A
        B
    end
```

Quando o custo real ultrapassa o limite configurado, o AWS Budgets envia uma notificação por meio de um tópico SNS, que entrega o e-mail ao destinatário cadastrado.

---

## ✅ Pré-requisitos

- ☁️ Conta AWS fornecida pela Escola da Nuvem, com o primeiro acesso já configurado
- 📧 Um endereço de e-mail válido para receber os alertas
- 🔐 Login no console com a conta correta (sempre confira a conta exibida no canto superior direito)

> ⚠️ No ambiente da capacitação, as permissões são limitadas ao necessário para cada laboratório. Mensagens de erro podem aparecer ao acessar serviços ou recursos não liberados para a conta.

---

## 📊 Resultado

<div align="center">

<div align="center">

![Output of aws sts get-caller-identity in CloudShell](docs/images/budget_created.png)

*Figura 1: Orçamento listado no console do AWS Budgets.*

</div>

---

## 🔍 Validação

Na lista de orçamentos, verifique se:

| ✔️ | Item | Esperado |
|---|---|---|
| ☑️ | **Nome** | `meu-budget-<nome-sobrenome>` |
| ☑️ | **Orçamento** | **US$ 10,00** |
| ☑️ | **Valor usado** | Compatível com o gasto atual (geralmente próximo de US$ 0,00 em conta nova) |
| ☑️ | **Limites** | Status **OK** enquanto estiver abaixo do limite |

---

## 💡 Principais Aprendizados

- 🔔 **Alertas antes do estouro:** limites dão tempo para reagir antes que os custos ultrapassem o planejado.
- 🕵️ **Investigar cedo:** a notificação é o sinal para descobrir de onde vem o gasto.
- 🧯 **Ação corretiva:** desligar recursos sem uso e otimizar serviços a tempo.
- 📉 **Evitar surpresas:** acompanhar os gastos durante todo o ciclo de faturamento, e não só no final.
- 🧠 **Relevância para a prova:** ferramentas de gestão e monitoramento de custos, como o AWS Budgets, fazem parte do projeto de arquiteturas com custo otimizado.

---

## 🧹 Limpeza e Observações de Custo

- ✅ A criação do orçamento neste laboratório faz parte da configuração do curso, e **o orçamento é mantido** para monitorar recursos durante a capacitação.
- 🔌 Ao receber um alerta, verifique se há **recursos ligados** para evitar cobranças desnecessárias.
- 💲 Consulte sempre os preços atuais da AWS e as condições do nível gratuito da sua conta.

---

## ☁️ Serviços Abordados

![AWS Budgets](https://img.shields.io/badge/AWS-Budgets-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Amazon SNS](https://img.shields.io/badge/Amazon-SNS-E7157B?style=flat-square&logo=amazonaws&logoColor=white) ![AWS Management Console](https://img.shields.io/badge/AWS-Management%20Console-232F3E?style=flat-square&logo=amazonaws&logoColor=white)

| Serviço | Papel no laboratório |
|---|---|
| 📊 **AWS Budgets** | Criação do orçamento de US$ 10,00 e definição do limite de alerta em 10% |
| 📣 **Amazon SNS** | Tópico de alerta de cobrança que entrega a notificação por e-mail |
| 🖥️ **AWS Management Console** | Interface utilizada para acessar e configurar os serviços |

---

## 👤 Autor

**Lucas Barretto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Conectar-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucaspimentabarretto)
[![GitHub](https://img.shields.io/badge/GitHub-Seguir-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/lbarretto)

---

<div align="center">

☁️ Laboratório realizado como parte do programa **Escola da Nuvem**, Turma **C10 OUT – DPCN 19** 🚀

</div>