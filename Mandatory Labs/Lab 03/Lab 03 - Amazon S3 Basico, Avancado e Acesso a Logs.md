<div align="center">

# 🪣 Amazon S3: Versionamento, Ciclo de Vida e Logs de Acesso

**Laboratório prático: organização, proteção e monitoramento de dados no Amazon S3**

![Amazon S3](https://img.shields.io/badge/Amazon-S3-569A31?style=flat-square&logo=amazons3&logoColor=white) ![S3 Glacier](https://img.shields.io/badge/S3-Glacier%20Instant%20Retrieval-569A31?style=flat-square&logo=amazons3&logoColor=white) ![Tema](https://img.shields.io/badge/Tema-Armazenamento%20e%20Custos-2E7D32?style=flat-square) ![Status](https://img.shields.io/badge/Status-Conclu%C3%ADdo-success?style=flat-square)

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
- [🧹 Limpeza dos Recursos](#-limpeza-dos-recursos)
- [☁️ Serviços Abordados](#️-serviços-abordados)
- [👤 Autor](#-autor)

---

## 📖 Sobre o Projeto

Este projeto documenta um laboratório prático realizado durante a capacitação da **Escola da Nuvem** (**Turma C10 OUT – DPCN 19**), como parte da minha preparação para a certificação **AWS Certified Solutions Architect – Associate**.

O laboratório explora recursos do **Amazon S3** voltados à organização, à segurança e ao gerenciamento eficiente de dados: **versionamento** de objetos, **regras de ciclo de vida** para otimizar custos e **registro em log de acesso ao servidor** para monitorar as requisições feitas ao bucket.

> 💡 No S3, boas práticas de armazenamento não são só sobre onde guardar os dados, mas também sobre **como protegê-los, quanto custam ao longo do tempo e quem os acessa**.

---

## 🎯 Objetivos

- 🪣 Criar um bucket principal e um bucket de destino para logs
- 🗂️ Habilitar o **versionamento** e observar o histórico de versões de um objeto
- ♻️ Recuperar uma versão anterior de um objeto
- ⏳ Criar uma **regra de ciclo de vida** para transição entre classes de armazenamento
- 📜 Ativar o **registro em log de acesso ao servidor** do bucket
- 🧹 Excluir os recursos criados ao final

---

## 🧩 Cenário

Como responsável pela gestão de armazenamento em nuvem de uma empresa, a missão é manter os dados **organizados, seguros e com custo otimizado**, aplicando versionamento, automação de regras de armazenamento e monitoramento de acessos.

| Parâmetro | Valor |
|---|---|
| 🌎 Região | Oeste dos EUA (N. da Califórnia), `us-west-1` |
| 🪣 Bucket principal | `bucket-edn-<seu-nome>` |
| 📜 Bucket de logs | `s3-access-logs-<seu-nome>` |
| 🗂️ Versionamento | **Habilitado** (exclusão com MFA desabilitada) |
| ⏳ Regra de ciclo de vida | `MoverParaGlacierApos30Dias`, escopo: todo o bucket |
| 🧊 Transição | **Glacier Instant Retrieval** após **30 dias** |
| 🗑️ Expiração (versões atuais) | **31 dias**, sempre 1 dia após a transição |

---

## 🏗️ Arquitetura

```mermaid
flowchart LR
    U["👥 Usuários"] -->|requisições| B["🪣 Bucket<br/>bucket-edn"]
    B -->|logs de acesso ao servidor| L["📜 Bucket de logs<br/>s3-access-logs"]
    B -.->|após 30 dias| G["🧊 Glacier Instant<br/>Retrieval"]

    subgraph AWS["☁️ AWS Cloud"]
        subgraph R["🌎 Região"]
            B
            L
            G
        end
    end
```

Os usuários acessam o bucket principal. Cada requisição é registrada em um **bucket separado** de logs, e uma regra de ciclo de vida move os objetos para uma classe de armazenamento mais econômica depois de 30 dias.

---

## ✅ Pré-requisitos

- ☁️ Acesso ao Console de Gerenciamento da AWS com a conta fornecida pela Escola da Nuvem
- 🗒️ Um arquivo de texto simples para testar o versionamento (editado e reenviado para gerar uma segunda versão)
- 💻 Conhecimento básico dos conceitos de buckets e objetos no S3

> ⚠️ No ambiente da capacitação, as permissões são limitadas ao necessário para cada laboratório. Mensagens de erro podem aparecer ao acessar serviços ou recursos não liberados para a conta.

---

## 📊 Resultado

### 🪣 Buckets criados

Os dois buckets do laboratório, criados na mesma região: o principal e o de destino dos logs de acesso.

<div align="center">

![Lista de buckets no console do S3](docs/images/LB_LAB3_Buckets.png)

*Figura 1: `bucket-edn-<seu-nome>` e `s3-access-logs-<seu-nome>` listados no console do S3 (`us-west-1`).*

</div>

### 🗂️ Versionamento habilitado

Aba **Propriedades** do bucket principal, com o versionamento ativo.

<div align="center">

![Versionamento de bucket habilitado](docs/images/LB_LAB3_Versionamento%20de%20bucket%20ativado.png)

*Figura 2: **Versionamento de bucket: Habilitado** (exclusão com MFA: Desabilitado).*

</div>

### ⏳ Regra de ciclo de vida

Aba **Gerenciamento** do bucket, com a regra `MoverParaGlacierApos30Dias` aplicada a todo o bucket.

<div align="center">

![Regra de ciclo de vida criada](docs/images/LB_LAB3_Regras%20de%20ciclo%20de%20vida.png)

*Figura 3: regra de ciclo de vida com status **Enabled**, escopo em todo o bucket e transição para Glacier Instant Retrieval.*

</div>

### 📜 Registro em log de acesso ao servidor

Configuração de logs do bucket principal, apontando para o bucket de destino.

<div align="center">

![Registro em log de acesso ao servidor habilitado](docs/images/LB_LAB3_Registro%20em%20log%20de%20acesso%20ao%20servidor.png)

*Figura 4: **Registro em log de acesso ao servidor: Habilitado**, com o bucket de destino `s3://s3-access-logs-<seu-nome>`.*

</div>

---

## 🔍 Validação

| ✔️ | Item | Esperado |
|---|---|---|
| ☑️ | **Buckets** | Bucket principal e bucket de logs listados na mesma região |
| ☑️ | **Versionamento** | Habilitado no bucket principal |
| ☑️ | **Versões do objeto** | Com "Mostrar versões" ativo, o arquivo exibe a Versão 1 e a Versão 2 |
| ☑️ | **Versão restaurada** | O re-upload da versão antiga cria uma nova versão, mantendo as anteriores |
| ☑️ | **Ciclo de vida** | Regra com status **Enabled** e escopo em todo o bucket |
| ☑️ | **Logs de acesso** | Habilitado, com destino no bucket de logs |
| ☑️ | **Entrega dos logs** | Arquivos de log aparecem no bucket de destino depois de algum tempo |

---

## 💡 Principais Aprendizados

- 🗂️ **Versionamento protege contra sobrescritas e exclusões acidentais:** cada novo upload do mesmo objeto gera uma nova versão, e as anteriores continuam disponíveis.
- ♻️ **Restaurar uma versão é um novo upload:** baixar uma versão antiga não a torna a atual; é preciso reenviá-la, e ela recebe um novo ID de versão.
- ⏳ **Ciclo de vida automatiza a economia:** mover dados menos acessados para classes mais baratas reduz custos sem trabalho manual.
- 💲 **Transições têm custo por requisição**, e por padrão há um tamanho mínimo de objeto (128 KB) para transitar entre classes.
- 🔢 **A expiração precisa ser posterior à transição:** por isso a regra usa 30 dias para a transição e 31 para a expiração.
- 📜 **Logs de acesso dão visibilidade:** registram as requisições ao bucket e são entregues em um bucket separado.
- 🕐 **A entrega dos logs não é imediata:** é eventual e, segundo o laboratório, não dá para configurar ou reduzir esse intervalo.
- 🧠 **Relevância para a prova:** versionamento, classes de armazenamento, regras de ciclo de vida e logs de acesso são temas recorrentes no exame.

---

## 🧹 Limpeza dos Recursos

Para evitar custos desnecessários, os recursos criados no laboratório devem ser removidos ao final:

- [ ] Esvaziar e excluir o **bucket principal** (`bucket-edn-<seu-nome>`), incluindo todas as versões dos objetos
- [ ] Esvaziar e excluir o **bucket de logs** (`s3-access-logs-<seu-nome>`)

> 💡 Em buckets com versionamento, é preciso remover também as versões antigas dos objetos antes de excluir o bucket.

---

## ☁️ Serviços Abordados

![Amazon S3](https://img.shields.io/badge/Amazon-S3-569A31?style=flat-square&logo=amazons3&logoColor=white) ![S3 Versioning](https://img.shields.io/badge/S3-Versioning-569A31?style=flat-square&logo=amazons3&logoColor=white) ![S3 Lifecycle](https://img.shields.io/badge/S3-Lifecycle-569A31?style=flat-square&logo=amazons3&logoColor=white) ![S3 Glacier](https://img.shields.io/badge/S3-Glacier%20Instant%20Retrieval-569A31?style=flat-square&logo=amazons3&logoColor=white) ![S3 Server Access Logging](https://img.shields.io/badge/S3-Server%20Access%20Logging-569A31?style=flat-square&logo=amazons3&logoColor=white)

| Recurso | Papel no laboratório |
|---|---|
| 🪣 **Amazon S3 (buckets)** | Armazenamento dos objetos e dos logs |
| 🗂️ **Versionamento** | Manter o histórico de versões e permitir a recuperação |
| ⏳ **Regras de ciclo de vida** | Transição automática dos objetos para outra classe de armazenamento |
| 🧊 **S3 Glacier Instant Retrieval** | Classe de destino da transição, de menor custo |
| 📜 **Registro em log de acesso ao servidor** | Registro das requisições feitas ao bucket |

---

## 👤 Autor

**Lucas Barretto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Conectar-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucaspimentabarretto)
[![GitHub](https://img.shields.io/badge/GitHub-Seguir-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/lbarretto)

---

<div align="center">

☁️ Laboratório realizado como parte do programa **Escola da Nuvem**, Turma **C10 OUT – DPCN 19** 🚀

</div>