---
title: "Plano gráfico de processamento de pagamento do Azure - Requisitos de acesso físico"
description: Requisito 9 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Os requisitos de acesso físico para ambientes em conformidade com PCI DSS 
## <a name="pci-dss-requirement-9"></a>Requisito 9 de PCI DSS

**Restringir o acesso físico aos dados do titular do cartão**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Qualquer acesso físico aos dados ou aos sistemas que hospedam dados do titular de cartão permite que os indivíduos acessem dados ou dispositivos e removam sistemas ou cópias impressas e deve ser adequadamente restrito. Para os fins do Requisito 9, "pessoal no local" significa os funcionários em tempo integral e de meio expediente, temporários funcionários, prestadores de serviço e consultores que estejam fisicamente presentes nas instalações da entidade. Um "visitante" significa um fornecedor, um convidado de qualquer funcionário da instalação, os trabalhadores de serviço ou qualquer pessoa que precise entrar nas instalações por pouco tempo, normalmente não mais de um dia. "Mídia" significa todas as mídias eletrônicas e de papel que contêm dados do titular do cartão.

## <a name="pci-dss-requirement-91"></a>Requisito 9.1 de PCI DSS

**9.1** Use os controles de entrada às instalações apropriados para limitar e monitorar o acesso físico aos sistemas no ambiente de dados do titular do cartão.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por implementar, impor e monitorar a segurança de acesso físico para data centers. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-911"></a>Requisito 9.1.1 de PCI DSS

**9.1.1** Use as câmeras de vídeo ou mecanismos de controle de acesso (ou ambos) para monitorar acesso físico de indivíduos às áreas restritas. Examine os dados coletados e correlacione com outras entradas. Armazene por pelo menos três meses, a menos que haja alguma restrição legal.

> [!NOTE]
> "Áreas restritas" significa qualquer data center, sala de servidor ou qualquer área que hospeda sistemas que armazenam, processam ou transmitam dados do titular do cartão. Isso exclui áreas públicas em que somente os terminais de ponto de venda estão presentes, como as áreas de caixa em uma loja de varejo.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por implementar, impor e monitorar CCTV e mecanismos de controle de acesso biométrico para data centers. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-912"></a>Requisito 9.1.2 de PCI DSS

**9.1.2** Implemente os controles físicos e/ou lógicos para restringir o acesso às tomadas de rede de acesso público. 

Por exemplo, as tomadas de rede localizados em áreas públicas e áreas acessíveis aos visitantes podem ser desabilitadas e habilitadas apenas quando o acesso à rede for autorizado explicitamente. Como alternativa, os processos podem ser implementados para garantir que os visitantes sejam sempre acompanhados nas áreas com tomadas de rede ativas.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não há nenhuma tomada de rede de acesso público na plataforma do Microsoft Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-913"></a>Requisito 9.1.3 de PCI DSS

**9.1.3** Restrinja o acesso físico aos gateways, dispositivos de mão, hardware de rede/comunicações, linhas de telecomunicações e pontos de acesso sem fio.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O acesso físico aos hardwares de rede do Microsoft Azure é rigidamente controlado pelas listas de acesso, várias formas de autenticação, barreiras físicas à entrada e aprovação para que a necessidade comercial tenha acesso ao equipamento. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



## <a name="pci-dss-requirement-92"></a>Requisito 9.2 de PCI DSS

**9.2** Desenvolva procedimentos para distinguir facilmente os funcionários e os visitantes das instalações, por exemplo:
- Identificação de funcionários e visitantes das instalações (por exemplo, atribuindo-se-lhes crachás)
- Alterações dos requisitos de acesso
- Revogação ou encerramento da identificação expirada do visitante ou do funcionário da instalação (como crachás de identificação).

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por implementar, impor e monitorar a segurança do acesso físico e a identificação de empregado ou de prestador de serviço quando da visita a data centers. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



## <a name="pci-dss-requirement-93"></a>Requisito 9.3 de PCI DSS

**9.3** Controle o acesso físico dos funcionários da instalação às áreas restritas da seguinte maneira:
- O acesso deve ser autorizado e baseado na função de trabalho do indivíduo.
- O acesso é revogado imediatamente após o encerramento, e todos os mecanismos de acesso físico, como chaves, cartões de acesso, etc., são devolvidos ou desabilitados.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As autorizações de acesso a data centers da Microsoft são controladas usando-se uma lista de acesso autorizado aprovada pela equipe do Data Center com base no princípio dos privilégios mínimos. A lista de controle de acesso é revisada, verificada e atualizada trimestralmente.<br /><br />Os data centers do Microsoft Azure utilizam dispositivos de acesso físico, como portas de perímetro, leitores de crachá de acesso eletrônico, leitores biométricos, porta dupla de segurança/portais e dispositivos com a função anti-passback. Os dispositivos de crachá de acesso são monitorados continuamente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



## <a name="pci-dss-requirement-94"></a>Requisito 9.4 de PCI DSS

**9.4** Implementar procedimentos para identificar e autorizar os visitantes. Os procedimentos devem incluir o seguinte.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por impor que as entregas pré-aprovadas sejam recebidas em um compartimento de carregamento seguro isolado fisicamente de recursos de processamento de informações e sejam monitorados pelo pessoal autorizado. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-941"></a>Requisito 9.4.1 de PCI DSS

**9.4.1** Os visitantes são autorizados antes de entrar e são sempre acompanhados nas áreas em que os dados do titular do cartão são processados ou mantidos.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por impor que as entregas pré-aprovadas sejam recebidas em um compartimento de carregamento seguro isolado fisicamente de recursos de processamento de informações e sejam monitorados pelo pessoal autorizado. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-942"></a>Requisito 9.4.2 de PCI DSS

**9.4.2** Os visitantes são identificados e recebem um crachá ou outra identificação que expira e que distingue visivelmente os visitantes e os funcionários.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O acesso ao data center da Microsoft deve ser pré-aprovado e os visitantes autorizados precisam se identificar à segurança física no ponto de entrada e fornecer uma prova válida da ID antes de entrada. Os selos indicam claramente os funcionários. Os prestadores de serviço e visitantes recebem crachás temporários que devem ser devolvidos após a saída das instalações. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-943"></a>Requisito 9.4.3 de PCI DSS

**9.4.3** Os visitantes terão de devolver o crachá ou a identificação antes de sair da instalação ou na data de expiração.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os visitantes terão de devolver os crachás após a saída de qualquer instalação da Microsoft. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-944"></a>Requisito 9.4.4 de PCI DSS

**9.4.4** Um log do visitante é usado para manter uma trilha de auditoria física da atividade do visitante na instalação, bem como nas salas de computadores e data centers onde dados do titular do cartão estão armazenados ou são transmitidos.
Registre no logo o nome do visitante, a empresa representada e o funcionário que autorizou o acesso físico.
Mantenha esse log por pelo menos três meses, a menos que haja alguma restrição legal.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | É responsabilidade do Microsoft Azure manter um log dos visitantes como uma trilha de auditoria física da atividade do visitante na instalação, bem como nas salas de computadores e data centers onde dados do titular do cartão estão armazenados ou são transmitidos. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



## <a name="pci-dss-requirement-95"></a>Requisito 9.5 de PCI DSS

**9.5** Proteja fisicamente todas as mídias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>Requisito 9.5.1 de PCI DSS

**9.5.1** Armazene os backups de mídia em um local seguro, preferencialmente em instalações fora da empresa, como um local alternativo ou de backup ou uma instalação de armazenamento comercial. Analise a segurança dos locais pelo menos uma vez por ano.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>Requisito 9.6 de PCI DSS

**9.6** Mantenha o controle restrito sobre a distribuição interna ou externa de qualquer tipo de mídia, incluindo o seguinte.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>Requisito 9.6.1 de PCI DSS

**9.6.1** Classifique as mídias para determinar a confidencialidade dos dados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>Requisito 9.6.2 de PCI DSS

**9.6.2** Envie as mídias por uma empresa de correio segura ou por outro método de entrega que possa ser acompanhado com precisão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>Requisito 9.6.3 de PCI DSS

**9.6.3** Garanta que a gestão aprove todo e qualquer tipo de mídia saindo da área protegida (inclusive quando as mídias são distribuídas para pessoas físicas).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>Requisito 9.7 de PCI DSS

**9.7** Mantenha um controle restrito sobre o armazenamento e a acessibilidade das mídias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>Requisito 9.7.1 de PCI DSS

**9.7.1** Mantenha da forma adequada os logs de inventário de todas as mídias e realize os inventários de mídia pelo menos uma vez por ano.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>Requisito 9.8 de PCI DSS

**9.8** Destrua as mídias quando não forem mais necessárias por motivos legais ou comerciais da seguinte maneira.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>Requisito 9.8.1 de PCI DSS

**9.8.1** Retalhe, queime ou triture os materiais impressos de forma que os dados do titular do cartão não podem ser reconstruídos. Proteja os contêineres de armazenamento usados para os materiais a serem destruídos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore armazena todos os dados no banco de dados do SQL Azure. Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>Requisito 9.8.2 de PCI DSS

**9.8.2** Torne os dados do titular do cartão na mídia eletrônica irrecuperáveis para que os dados do titular do cartão não podem ser reconstruídos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As técnicas de destruição de dados variam dependendo do tipo de objeto de dados que está sendo destruído, seja em assinaturas, armazenamento, máquinas virtuais ou bancos de dados. No ambiente de multilocatário do Microsoft Azure, tenha atenção especial para garantir que os dados de um cliente não possam "vazar" para os dados de outro cliente, ou para que quando um cliente exclui dados nenhum outro cliente (incluindo, na maioria dos casos, o cliente ao qual pertenciam os dados) possa obter acesso aos dados excluídos.<br /><br />O Microsoft Azure segue as Diretrizes NIST 800-88 sobre Limpeza de mídia, que atendem à principal preocupação: assegurar que os dados não sejam liberados acidentalmente. Essas diretrizes abrangem a limpeza eletrônica e física. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore pode ser excluída inteiramente excluindo-se o Grupo de recursos usado durante a implantação.|



## <a name="pci-dss-requirement-99"></a>Requisito 9.9 de PCI DSS

**9.9** Proteja contra violação e substituição os dispositivos que capturam dados de cartão de pagamento por meio de interação física direta com o cartão.

> [!NOTE]
> Esses requisitos se aplicam a dispositivos de leitura de cartão usados em transações com cartão (ou seja, passar ou inserir o cartão) no ponto de venda. Esse requisito não se destina à aplicação de componentes de entrada de chave manuais como teclados de computador e teclados de POS. 

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore utiliza a OMS para registrar em log todas as alterações no sistema.<br /><br />A [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece um amplo registro em log das alterações. A precisão das alterações pode ser examinada e verificada. Para obter instruções mais específicas, consulte [Diretriz de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>Requisito 9.9.1 de PCI DSS

**9.9.1** Manter uma lista atualizada de dispositivos. A lista deve incluir o seguinte:
- Marca, modelo do dispositivo
- Local do dispositivo (por exemplo, o endereço do local ou instalações onde se encontra o dispositivo)
- Número de série do dispositivo ou outro método de identificação exclusiva

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece uma arquitetura de referência e uma lista de todos os serviços usados na sua documentação de implantação.|



### <a name="pci-dss-requirement-992"></a>Requisito 9.9.2 de PCI DSS

**9.9.2** Inspecione periodicamente as superfícies de dispositivo para detectar adulteração (por exemplo, adição de aparelhos de clonagem de cartão aos dispositivos) ou a substituição (por exemplo, verificando o número de série ou outras características do dispositivo para verificar se ele não foi trocado por um dispositivo fraudulento).

> [!NOTE]
> Podemos citar os seguintes exemplos de sinais de que um dispositivo foi adulterado ou substituído: anexos ou cabos estranhos conectados ao dispositivo, rótulos de segurança ausentes ou corrompidos, revestimentos quebrados ou de cores diferentes ou alterações no número de série ou outras marcações externas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-993"></a>Requisito 9.9.3 de PCI DSS

**9.9.3** Treine os funcionários para que estejam cientes das tentativas de adulteração e substituição de dispositivos. O treinamento deve incluir o seguinte:
- Verifique a identidade de quaisquer terceiros alegando ser funcionários de reparo ou manutenção, antes de conceder-lhes acesso para a modificação ou a solução de problemas de dispositivos.
- Não instale, substitua nem retorne dispositivos sem verificação.
- Tenha em mente o comportamento suspeito em torno de dispositivos (por exemplo, tentativas por pessoas desconhecidas para desconectar ou abrir dispositivos).
- Informe aos funcionários pertinentes o comportamento suspeito e as indicações de substituição ou adulteração de dispositivo (por exemplo, a um gerente ou analista de segurança).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



## <a name="pci-dss-requirement-910"></a>Requisito 9.10 de PCI DSS

**9.10** Garantir que políticas de segurança e procedimentos operacionais para restringir o acesso físico a dados do titular do cartão estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|




