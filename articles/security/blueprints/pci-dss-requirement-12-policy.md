---
title: "Diagrama de processamento de pagamento do Azure - Requisitos de política"
description: Requisito 12 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Requisitos de política para ambientes em conformidade com o PCI DSS  
## <a name="pci-dss-requirement-12"></a>Requisito 12 de PCI DSS

**Manter uma política direcionada à segurança de informações para todos os funcionários**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Uma política de segurança forte define o tom de segurança para toda a entidade e informa à equipe o que é esperado deles. Todos os funcionários devem estar atentos à confidencialidade dos dados e a suas responsabilidades para protegê-los. Para fins do Requisito 12, “pessoal” ou “equipe” se refere a funcionários em tempo integral e de meio expediente, funcionários temporários, prestadores de serviço e consultores que tem "base" na instalação da entidade ou que possam acessar o ambiente de dados do titular do cartão.

## <a name="pci-dss-requirement-121"></a>Requisito 12.1 de PCI DSS

**12.1** Estabelecer, publicar, manter e disseminar uma política de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por estabelecer e manter uma política de segurança de informações.|



### <a name="pci-dss-requirement-1211"></a>Requisito 12.1.1 de PCI DSS

**12.1.1** Examinar a política de segurança pelo menos uma vez ao ano e atualizar a política quando o ambiente for alterado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por atualizar sua política de segurança das informações pelo menos uma vez ao ano ou quando houver alterações em seu ambiente de CHD (dados de titulares de cartão).|



## <a name="pci-dss-requirement-122"></a>Requisito 12.2 de PCI DSS

**12.2** Implementar um processo de avaliação de risco que:
- Seja executado pelo menos uma vez ao ano e após alterações significativas no ambiente (por exemplo, aquisição, fusão, realocação, etc.)
- Identifique vulnerabilidades, ameaças e ativos críticos
- Resulte em uma análise de risco formal e documentada.
- > Exemplos de metodologias de avaliação de risco incluem, dentre outras, OCTAVE, ISO 27005 e NIST SP 800-30.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por implementar um processo de avaliação de risco que atende a todas as ameaças listadas no Requisito 12.2.|



## <a name="pci-dss-requirement-123"></a>Requisito 12.3 de PCI DSS

**12.3** Desenvolver políticas de uso de tecnologias importantes e definir o uso apropriado dessas tecnologias.

> [!NOTE]
> Exemplos de tecnologias importantes incluem, dentre outras, tecnologias de acesso remoto e sem fio, laptops, tablets, mídias eletrônicas removíveis, uso de email e Internet.
Verifique se as políticas de uso exigem os itens a seguir.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1231"></a>Requisito 12.3.1 de PCI DSS

**12.3.1** Aprovação explícita por pessoas autorizadas

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1232"></a>Requisito 12.3.2 de PCI DSS

**12.3.2** Autenticação para uso da tecnologia

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1233"></a>Requisito 12.3.3 de PCI DSS

**12.3.3** Uma lista de todos esses dispositivos e do pessoal com acesso

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1234"></a>Requisito 12.3.4 de PCI DSS

**12.3.4** Um método para determinar com precisão e prontidão o proprietário, as informações de contato e o objetivo (por exemplo, identificação, codificação e/ou inventário de dispositivos)

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1235"></a>Requisito 12.3.5 de PCI DSS

**12.3.5** Usos da tecnologia aceitáveis

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1236"></a>Requisito 12.3.6 de PCI DSS

**12.3.6** Locais de rede aceitáveis para as tecnologias

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por determinar os locais de rede aceitáveis para armazenamento, serviços de suporte e VMs com base em nuvem.|



### <a name="pci-dss-requirement-1237"></a>Requisito 12.3.7 de PCI DSS

**12.3.7** Lista de produtos aprovados pela empresa

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por determinar os locais de rede aceitáveis para armazenamento, serviços de suporte e VMs com base em nuvem.|



### <a name="pci-dss-requirement-1238"></a>Requisito 12.3.8 de PCI DSS

**12.3.8** Desconexão automática de sessões para tecnologias de acesso remoto após determinado período de inatividade

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa a funcionalidade de bloqueio de sessão do AD corporativo da Microsoft, que impõe o bloqueio da sessão após um período de inatividade. As conexões de rede são encerradas após 30 minutos de inatividade. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1239"></a>Requisito 12.3.9 de PCI DSS

**12.3.9** A ativação de tecnologias de acesso remoto para fornecedores e parceiros comerciais somente quando necessário, com a desativação imediata após o uso

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-12310"></a>Requisito 12.3.10 de PCI DSS

**12.3.10** Para a equipe que acessa os dados do titular do cartão por meio de tecnologias de acesso remoto, proíba a cópia, a movimentação e o armazenamento de dados do titular do cartão em unidades de disco rígido locais e em mídia removível eletrônica, a menos que expressamente autorizados para um objetivo comercial específico.
Onde houver uma necessidade comercial autorizada, as políticas de uso devem exigir que os dados sejam protegidos de acordo com todos os requisitos de PCI DSS aplicáveis.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por fazer com que a equipe que acessa os dados do titular do cartão por meio de tecnologias de acesso remoto sejam proibidos de copiar, movimentar e armazenar dados do titular do cartão em unidades de disco rígido locais e em mídia removível eletrônica, a menos que expressamente autorizados para um objetivo comercial específico.|



## <a name="pci-dss-requirement-124"></a>Requisito 12.4 de PCI DSS

**12.4** Verificar se a política e os procedimentos de segurança definem claramente as responsabilidades de segurança de informações para todos os funcionários.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1241"></a>Requisito 12.4.1 de PCI DSS

**12.4.1** Requisito adicional somente para provedores de serviços: o gerenciamento executivo deverá estabelecer responsabilidade pela proteção de dados do titular do cartão e um programa de conformidade PCI DSS que inclua:
- Responsabilidade geral de manter a conformidade de PCI DSS
- Definindo um estatuto para um programa de conformidade de PCI DSS e comunicação com os executivos 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes que são Provedores de Serviço serão responsáveis por documentar seu programa de conformidade de PCI.|



## <a name="pci-dss-requirement-125"></a>Requisito 12.5 de PCI DSS

**12,5** Atribuir a um indivíduo ou a uma equipe as responsabilidades de gerenciamento de segurança de informações a seguir.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por definir e atribuir responsabilidades de segurança da informação aos seus funcionários.|



### <a name="pci-dss-requirement-1251"></a>Requisito 12.5.1 de PCI DSS

**12.5.1** Estabelecer, documentar e distribuir políticas e procedimentos de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por definir e atribuir responsabilidades de segurança da informação aos seus funcionários.|



### <a name="pci-dss-requirement-1252"></a>Requisito 12.5.2 de PCI DSS

**12.5.2** Monitorar e analisar os alertas e as informações de segurança e distribuir ao pessoal apropriado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por definir e atribuir responsabilidades de segurança da informação aos seus funcionários.|



### <a name="pci-dss-requirement-1253"></a>Requisito 12.5.3 de PCI DSS

**12.5.3** Estabelecer, documentar e distribuir procedimentos de resposta e escalonamento de incidentes de segurança para garantir o tratamento rápido e eficaz de todas as situações.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1254"></a>Requisito 12.5.4 de PCI DSS

**12.5.4** Administrar as contas de usuário, inclusive adições, exclusões e modificações.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



### <a name="pci-dss-requirement-1255"></a>Requisito 12.5.5 de PCI DSS

**12.5.5** Monitorar e controlar todos os acessos a dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas que ditam o uso, a implementação e a autenticação correta de tecnologias essenciais no CDE.|



## <a name="pci-dss-requirement-126"></a>Requisito 12.6 de PCI DSS

**12.6** Implementar um programa de conscientização de segurança formal para mostrar a todos os funcionários a importância da política e dos procedimentos de segurança de dados do titular do cartão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por criar e manter políticas a respeito da conscientização de segurança para equipes com acesso ao CDE.|



### <a name="pci-dss-requirement-1261"></a>Requisito 12.6.1 de PCI DSS

**12.6.1** Treinar a equipe na contratação e pelo menos uma vez ao ano. 

> [!NOTE]
> Os métodos podem variar dependendo da função da equipe e do seu nível de acesso aos dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por fazer com que a equipe receba e confirme o treinamento de segurança da informação e reconhecimento de PCI-DSS pelo menos uma vez ao ano.|



### <a name="pci-dss-requirement-1262"></a>Requisito 12.6.2 de PCI DSS

**12.6.2** Exigir que a equipe confirme pelo menos uma vez ao ano que leram e entenderam a política e os procedimentos de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por fazer com que a equipe receba e confirme o treinamento de segurança da informação e reconhecimento de PCI-DSS pelo menos uma vez ao ano.|



## <a name="pci-dss-requirement-127"></a>Requisito 12.7 de PCI DSS

**12,7** Fazer a triagem dos possíveis membros da equipe antes da contratação para minimizar o risco de ataques de fontes internas. (Exemplos de verificação de histórico incluem empregos anteriores, registro criminal, histórico de crédito e verificações de referência.) 

> [!NOTE]
> Para a contratação desses funcionários para determinados cargos, como caixas de loja que só têm acesso ao número de um cartão por vez ao executar uma transação, esse requisito é apenas uma recomendação.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por garantir que a equipe com acesso ao CDE seja submetida a verificações de antecedentes completas.|



## <a name="pci-dss-requirement-128"></a>Requisito 12.8 de PCI DSS

**12,8** Manter e implementar políticas e procedimentos para gerenciar provedores de serviços com quem os dados são compartilhados ou que possam afetar a segurança dos dados do titular do cartão, como mostrado a seguir.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por monitorar a conformidade de PCI em provedores de serviços com os quais os dados do titular do cartão são compartilhados, ou isso poderá afetar a segurança do CDE. Os clientes devem manter uma lista de todos os provedores de serviço usados em seus CDEs.|



### <a name="pci-dss-requirement-1281"></a>Requisito 12.8.1 de PCI DSS

**12.8.1** Manter uma lista de provedores de serviço, incluindo uma descrição do serviço fornecido.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por monitorar a conformidade de PCI em provedores de serviços com os quais os dados do titular do cartão são compartilhados, ou isso poderá afetar a segurança do CDE. Os clientes devem manter uma lista de todos os provedores de serviço usados em seus CDEs.|



### <a name="pci-dss-requirement-1282"></a>Requisito 12.8.2 de PCI DSS

**12.8.2** Firmar um contrato por escrito que inclua a confirmação de que os provedores de serviço são responsáveis pela segurança dos dados do titular do cartão que eles possuem ou armazenem, processem ou transmitam em nome do cliente, ou no limite em que poderiam afetar a segurança do ambiente dos dados do titular do cartão do cliente. 

> [!NOTE]
> A frase exata de uma confirmação dependerá do contrato entre as duas partes, dos detalhes do serviço que está sendo fornecido e das responsabilidades atribuídas a cada parte. A confirmação não precisa incluir as palavras exatas fornecidas neste requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por firmar contratos por escrito com provedores de serviço confirmando a responsabilidade de manter a segurança dos dados do titular do cartão.|



### <a name="pci-dss-requirement-1283"></a>Requisito 12.8.3 de PCI DSS

**12.8.3** Verificar se há um processo estabelecido para contatar provedores de serviços, incluindo a devida auditoria antes da contratação.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por fazer com que haja um processo estabelecido para contatar provedores de serviços, incluindo a devida auditoria antes da contratação.|



### <a name="pci-dss-requirement-1284"></a>Requisito 12.8.4 de PCI DSS

**12.8.4** Manter um programa para monitorar o status de conformidade de PCI DSS dos provedores de serviço pelo menos uma vez ao ano.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por manter um programa para monitorar o status de conformidade de PCI DSS dos provedores de serviço pelo menos uma vez ao ano.|



### <a name="pci-dss-requirement-1285"></a>Requisito 12.8.5 de PCI DSS

**12.8.5** Manter informações sobre quais requisitos de PCI DSS são gerenciados por cada provedor de serviço e quais são gerenciados pela entidade.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por manter uma cópia da [Matriz de Resumo de Responsabilidade](https://aka.ms/pciblueprintcrm32), que descreve os requisitos de PCI DSS de responsabilidade do cliente e os de responsabilidade do Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Requisito 12.9 de PCI DSS

**12.9** Requisito adicional somente para provedores de serviço: os provedores de serviço reconhecem por escrito para os clientes que são responsáveis pela segurança dos dados do titular do cartão que eles possuem ou armazenem, processem ou transmitam em nome do cliente, ou no limite em que poderiam afetar a segurança do ambiente dos dados do titular do cartão do cliente. 

> [!NOTE]
> A frase exata de uma confirmação dependerá do contrato entre as duas partes, dos detalhes do serviço que está sendo fornecido e das responsabilidades atribuídas a cada parte. A confirmação não precisa incluir as palavras exatas fornecidas neste requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes que são Provedores de Serviço serão responsáveis por confirmar suas responsabilidades de manter a conformidade de PCI. |



## <a name="pci-dss-requirement-1210"></a>Requisito 12.10 de PCI DSS

**12.10** Implementar um plano de resposta a incidentes. Esteja preparado para responder imediatamente a uma violação de sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12101"></a>Requisito 12.10.1 de PCI DSS

**12.10.1** Criar o plano de resposta a incidentes a ser implementado em caso de falha do sistema. Verifique se o plano trata dos seguintes itens, no mínimo:
- Funções, responsabilidades e estratégias de comunicação e contato em caso de comprometimento, incluindo a notificação das bandeiras de pagamento, no mínimo
- Procedimentos de resposta a incidentes específicos
- Procedimentos de recuperação e continuidade de negócios
- Processos de backup de dados
- Análise dos requisitos legais para relatar comprometimentos
- Cobertura e respostas de todos os componentes essenciais do sistema
- Referência ou inclusão de procedimentos de resposta a incidentes das bandeiras de pagamento

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12102"></a>Requisito 12.10.2 de PCI DSS

**12.10.2** Examinar e testar o plano, incluindo todos os elementos listados no Requisito 12.10.1, pelo menos uma vez ao ano.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12103"></a>Requisito 12.10.3 de PCI DSS

**12.10.3** Designar equipe específica para estar disponível e responder aos alertas 24 horas por dia, 7 dias por semana.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12104"></a>Requisito 12.10.4 de PCI DSS

**12.10.4** Fornecer treinamento apropriado à equipe com responsabilidades de resposta à violação de segurança.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12105"></a>Requisito 12.10.5 de PCI DSS

**12.10.5** Incluir alertas de sistemas de monitoramento de segurança, incluindo, dentre outros, detecção de intrusões, prevenção de intrusões, firewalls e sistemas de monitoramento de integridade do arquivo.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



### <a name="pci-dss-requirement-12106"></a>Requisito 12.10.6 de PCI DSS

**12.10.6** Desenvolver um processo para modificar e desenvolver o plano de resposta a incidentes de acordo com as lições aprendidas e incorporar desenvolvimentos do setor.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por desenvolver planos de IR e testes que levem em conta os controles de cliente relacionados a pontos de contato compartilhados e o aplicativos clientes que aproveitam a infraestrutura do Azure. É responsabilidade do cliente fornecer informações de contato precisas ao Azure para o caso de algum incidente que precise ser informado por poder afetar seu aplicativo ou seus dados.|



## <a name="pci-dss-requirement-1211"></a>Requisito 12.11 de PCI DSS

**12.11** **Requisitos adicionais somente para provedores de serviço:** executar análises pelo menos uma vez por trimestre para confirmar que a está seguindo as políticas de segurança e os procedimentos operacionais.
As revisões devem abranger os seguintes processos:
- Revisões diárias de log
- Revisões de conjunto de regras de firewall
- Aplicação de padrões de configuração a novos sistemas
- Respondendo a alertas de segurança
- Processos de gerenciamento de alterações 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes que são provedores de serviço são responsáveis por documentar suas revisões de processos para confirmar o desempenho de controle de conformidade de PCI.|



### <a name="pci-dss-requirement-12111"></a>Requisito 12.11.1 de PCI DSS

**12.11.1** Requisitos adicionais somente para provedores de serviço: manter a documentação do processo de revisão trimestral com a inclusão de:
- Documentação do resultado das análises
- A revisão e aprovação dos resultados por pessoal com a devida responsabilidade para o programa de conformidade de PCI DSS 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes que são provedores de serviço são responsáveis por documentar suas revisões de processos para confirmar o desempenho de controle de conformidade de PCI.|




