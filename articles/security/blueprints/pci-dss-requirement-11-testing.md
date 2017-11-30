---
title: Diagrama de processamento de pagamento do Azure - Requisitos de teste
description: Requisito 11 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Requisitos de teste para ambientes em conformidade com o PCI DSS 
## <a name="pci-dss-requirement-11"></a>Requisito 11 de PCI DSS

**Testar regularmente os sistemas e processos de segurança**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Vulnerabilidades estão sendo detectadas continuamente por indivíduos mal-intencionados e pesquisadores, e estão sendo introduzidas por novo softwares. Softwares personalizados, processos e componentes do sistema devem ser testados com frequência para fazer com que os controles de segurança continuem a refletir um ambiente dinâmico.

## <a name="pci-dss-requirement-111"></a>Requisito 11.1 de PCI DSS

**11.1** Implementar processos para testar a presença de pontos de acesso sem fio (802.11) e detectar e identificar todos os pontos de acesso autorizados e não autorizados a cada trimestre.

> [!NOTE]
> Os métodos que podem ser usados no processo incluem, dentre outros, verificações de rede sem fio, inspeções físicas/lógicas de componentes e infraestrutura de sistema, NAC (controle de acesso à rede) ou IDS/IPS sem fio.
Seja qual for o método usado, eles devem ser suficientes para detectar e identificar dispositivos autorizados e não autorizados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Azure não permite conexões sem fio no ambiente de rede do Azure. As equipes de segurança interna verificam regularmente se há sinais sem fio não autorizados trimestralmente e eles são investigados e removidos. Os clientes não podem implantar a tecnologia sem fio no ambiente do Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Esta solução não tem acesso sem fio e SNMP implantados.|



### <a name="pci-dss-requirement-1111"></a>Requisito 11.1.1 de PCI DSS

**11.1.1** Manter um inventário dos pontos de acesso sem fio autorizados, incluindo uma justificativa comercial comprovada.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Esta solução não tem acesso sem fio e SNMP implantados.|



### <a name="pci-dss-requirement-1112"></a>Requisito 11.1.2 de PCI DSS

**11.1.2** Implementar procedimentos de resposta a incidentes caso pontos de acesso sem fio não autorizados sejam detectados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Esta solução não tem acesso sem fio e SNMP implantados.|



## <a name="pci-dss-requirement-112"></a>Requisito 11.2 de PCI DSS

**11.2** Executar verificações de vulnerabilidade de rede interna e externa pelo menos trimestralmente e depois de qualquer alteração significativa na rede (como novas instalações de componente do sistema, alterações na topologia de rede, modificações de regra de firewall, atualizações de produto). 

> [!NOTE]
> Vários relatórios de verificação podem ser combinados para o processo de verificação trimestral a fim de mostrar que todos os sistemas foram examinados e todas as vulnerabilidades aplicáveis foram resolvidas. Outros documentos podem ser necessários para verificar se as vulnerabilidades não corrigidas estão no processo de correção.
> Quanto à conformidade de PCI DSS inicial, não é necessário concluir os quatro trimestres de aprovação se o assistente verifica 1) que o resultado de verificação mais recente foi uma aprovação, 2) que a entidade documentou políticas e procedimentos para exigir a verificação trimestral e 3) que as vulnerabilidades observadas nos resultados da verificação foram corrigidas conforme mostrado em uma nova verificação. Por vários anos após a revisão inicial de PCI DSS, eram necessárias quatro aprovações de verificação.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Azure executa verificações de vulnerabilidade interna e externa trimestralmente. As verificações são executadas pelo pessoal qualificado. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore passou por um teste de penetração e de verificação de vulnerabilidades em uma iniciativa 'no estado em que se encontra'. Os resultados do teste de penetração podem ser duplicados usando ferramentas comuns, como nmap ou pentest-tools.com. Os resultados do teste de penetração fornecerão uma superfície de ataque inconclusivo, sem itens que possam ser explorados. Além disso, a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem informações sobre vulnerabilidades e correções.|



### <a name="pci-dss-requirement-1121"></a>Requisito 11.2.1 de PCI DSS

**11.2.1** Executar verificações de vulnerabilidade interna trimestralmente. Tratar Vulnerabilidades e executar novas verificações para confirmar se todas as vulnerabilidades de "alto risco" foram resolvidas de acordo com a classificação de vulnerabilidade da entidade (conforme Requisito 6.1). As verificações devem ser realizadas por pessoal qualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure executa verificações quanto a vulnerabilidades na infraestrutura subjacente no escopo. O Microsoft Azure implementa a verificação de vulnerabilidade em sistemas operacionais de servidor, bancos de dados e dispositivos de rede com ferramentas de verificação de vulnerabilidade apropriadas. Os aplicativos Web do Azure são verificados com verificação de soluções apropriadas do setor. As verificações de vulnerabilidade são executadas a cada trimestre.<br /><br />Novas verificações são executadas conforme a necessidade em todos os sistemas, até que as vulnerabilidades de “alto risco” (conforme identificadas no Requisito 6.1) sejam resolvidas. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore passou por um teste de penetração e de verificação de vulnerabilidades em uma iniciativa 'no estado em que se encontra'. Os resultados do teste de penetração podem ser duplicados usando ferramentas comuns, como nmap ou pentest-tools.com. Os resultados do teste de penetração fornecerão uma superfície de ataque inconclusivo, sem itens que possam ser explorados. Além disso, a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem informações sobre vulnerabilidades e correções.|



### <a name="pci-dss-requirement-1122"></a>Requisito 11.2.2 de PCI DSS

**11.2.2** Executar verificações de vulnerabilidade externa trimestral, por meio de um ASV (Fornecedor de Verificação Aprovado) aprovado pelo PCI SSC (Payment Card Industry Security Standards Council). Executar novas verificações conforme a necessidade, até que as verificações sejam aprovadas. 

> [!NOTE]
> Verificações de vulnerabilidade externa devem ser realizadas trimestralmente por meio de um ASV (Fornecedor de Verificação Aprovado) aprovado pelo PCI SSC (Payment Card Industry Security Standards Council).
> Consulte o Guia do Programa ASV publicado no site do PCI SSC para conhecer as responsabilidades do cliente de verificação, a preparação da verificação, etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure executa verificações externas quanto a vulnerabilidades na infraestrutura subjacente no escopo que pode ser acessada externamente. As verificações são executadas por um ASV (Fornecedor de Verificação Aprovado).<br /><br />O Microsoft Azure se inscreve para notificações de patches MSRC/OSSC mensais e verificações quanto a vulnerabilidades no mínimo trimestrais. As vulnerabilidades identificadas são avaliadas e corrigidas pela linha do tempo estabelecida com base no nível de risco.<br /><br />A cada trimestre, uma verificação de vulnerabilidade de segurança abrangente e direcionada em componentes priorizados do ambiente do Microsoft Azure é realizada para identificar vulnerabilidades de segurança. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Ao implantar a Contoso Webstore, os clientes da demonstração são responsáveis por executar verificações e novas verificações de vulnerabilidade externa trimestralmente, conforme a necessidade, em todas as instâncias PaaS em seu CDE (ambiente de dados do titular do cartão), usando um ASV (Fornecedor de Verificação Aprovado) aprovado pelo Payment Card Industry Security Standards Council.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Requisito 11.2.3 de PCI DSS

**11.2.3** Executar verificações e novas verificações internas e externas, conforme a necessidade, depois de qualquer alteração significativa. As verificações devem ser realizadas por pessoal qualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os resultados são informados aos participantes e a correção é rastreada pela Equipe de Segurança do Azure até o fechamento. Os resultados de teste do Azure podem ser compartilhados com os clientes mediante um NDA. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por executar verificações e novas verificações de vulnerabilidade externas e internas, conforme a necessidade, em todas as instâncias de PaaS em seu CDE. As verificações devem ser executadas após alterações significativas no ambiente no escopo.<br /><br />As verificações devem ser executadas por um ASV ou pessoal com independência organizacional.|



## <a name="pci-dss-requirement-113"></a>Requisito 11.3 de PCI DSS

**11.3** Implementar uma metodologia de teste de penetração que inclua o seguinte:
- Baseada em abordagens de teste de penetração aceitas pelo setor (por exemplo, NIST SP800-115)
- Inclui a abordagem de todo o perímetro do CDE e sistemas críticos
- Inclui teste interno e fora da rede
- Inclui testes para validar controles de segmentação e de redução de escopo
- Define testes de penetração de camada de aplicativo para incluir, no mínimo, as vulnerabilidades relacionadas no Requisito 6.5
- Define os testes de penetração de camada de rede para incluir componentes que dão suporte a funções de rede, bem como sistemas operacionais
- Inclui a revisão e a consideração de ameaças e vulnerabilidades ocorridas nos últimos 12 meses
- Especifica a retenção dos resultados dos testes e dos resultados das atividades de correção

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure valida serviços usando teste de penetração de terceiros com base nos 10 principais do OWASP (Open Web Application Security Project) que usam testadores certificados por CREST. Os resultados de teste são controlados por meio de um registro de risco, que é auditado e revisado regularmente para garantir a conformidade com as práticas de segurança. <br /><br />A Microsoft também usa Red Teaming em relação à infraestrutura, aos serviços e aos aplicativos gerenciados pela Microsoft. Nenhum dado de cliente final é deliberadamente visado durante o uso de Equipes Vermelhas e os testes de penetração de sites ativos. Os testes são feitos na infraestrutura e nas plataformas do Microsoft Azure, bem como nos aplicativos e nos dados da Microsoft. Os locatários, os aplicativos e os dados do cliente hospedados no Azure nunca são visados.<br /><br />A Microsoft Azure contratou um assessor independente para desenvolver um plano de avaliação do sistema e realizar uma avaliação de controles. As avaliações de controles são executadas anualmente e os resultados são informados às partes relevantes. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore passou por um teste de penetração e de verificação de vulnerabilidades em uma iniciativa 'no estado em que se encontra'. Os resultados do teste de penetração podem ser duplicados usando ferramentas comuns, como nmap ou pentest-tools.com. Os resultados do teste de penetração fornecerão uma superfície de ataque inconclusivo, sem itens que possam ser explorados. Além disso, a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem informações sobre vulnerabilidades e correções.|



### <a name="pci-dss-requirement-1131"></a>Requisito 11.3.1 de PCI DSS

**11.3.1** Executar teste de penetração *externo* pelo menos uma vez ao ano e depois de alterações relevantes na infraestrutura, da atualização ou modificação do aplicativo (por exemplo, uma atualização do sistema operacional, uma sub-rede adicionada ao ambiente ou um servidor Web adicionado ao ambiente).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 11.3](#pci-dss-requirement-11-3). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore passou por um teste de penetração e de verificação de vulnerabilidades em uma iniciativa 'no estado em que se encontra'. Os resultados do teste de penetração podem ser duplicados usando ferramentas comuns, como nmap ou pentest-tools.com. Os resultados do teste de penetração fornecerão uma superfície de ataque inconclusivo, sem itens que possam ser explorados. Além disso, a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem informações sobre vulnerabilidades e correções.|



### <a name="pci-dss-requirement-1132"></a>Requisito 11.3.2 de PCI DSS

**11.3.1** Executar teste de penetração *externo* pelo menos uma vez ao ano e depois de alterações relevantes na infraestrutura, da atualização ou modificação do aplicativo (por exemplo, uma atualização do sistema operacional, uma sub-rede adicionada ao ambiente ou um servidor Web adicionado ao ambiente).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Contratos do Microsoft Azure com avaliadores independentes para executar o teste de penetração dos limites do Microsoft Azure. Os exercícios de Red-Team também são executados rotineiramente e os resultados são usados para fazer aperfeiçoamentos de segurança. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore passou por um teste de penetração e de verificação de vulnerabilidades em uma iniciativa 'no estado em que se encontra'. Os resultados do teste de penetração podem ser duplicados usando ferramentas comuns, como nmap ou pentest-tools.com. Os resultados do teste de penetração fornecerão uma superfície de ataque inconclusivo, sem itens que possam ser explorados. Além disso, a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem informações sobre vulnerabilidades e correções.|



### <a name="pci-dss-requirement-1133"></a>Requisito 11.3.3 de PCI DSS

**11.3.3** As vulnerabilidades exploráveis encontradas durante o teste de penetração são corrigidas e o teste é repetido para verificar as correções.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os procedimentos foram estabelecidos para monitorar os componentes da plataforma Microsoft Azure em relação a vulnerabilidades de segurança conhecidas. <br /><br /><br /><br />A cada trimestre, uma verificação de vulnerabilidade de segurança abrangente e direcionada em componentes priorizados do ambiente de produção do Azure é realizada para identificar vulnerabilidades de segurança. Os resultados são informados aos participantes e a correção é rastreada pela equipe até o fechamento. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations), que fornecem informações sobre vulnerabilidade e correção, foram usados para fazer com que todos os problemas pendentes fossem corrigidos no CDE de demonstração da Contoso Webstore.|



### <a name="pci-dss-requirement-1134"></a>Requisito 11.3.4 de PCI DSS

**11.3.4** Se segmentação for usada para isolar o CDE de outras redes, executar testes de penetração pelo menos uma vez ao ano e após as alterações nos controles/métodos de segmentação para verificar se os métodos de segmentação estão funcionando de forma eficaz, e isolar todos os sistemas fora do escopo dos sistemas no CDE.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os procedimentos foram estabelecidos para monitorar os componentes da plataforma Microsoft Azure em relação a vulnerabilidades de segurança conhecidas. <br /><br /><br /><br />A cada trimestre, uma verificação de vulnerabilidade de segurança abrangente e direcionada em componentes priorizados do ambiente de produção do Azure é realizada para identificar vulnerabilidades de segurança. Os resultados são informados aos participantes e a correção é rastreada pela equipe até o fechamento. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations), que fornecem informações sobre vulnerabilidade e correção, foram usados para fazer com que todos os problemas pendentes fossem corrigidos no CDE de demonstração da Contoso Webstore.|



### <a name="pci-dss-requirement-11341"></a>Requisito 11.3.4.1 de PCI DSS

**11.3.4.1** *Requisitos adicionais somente para provedores de serviço:* se segmentação for usada, confirme o escopo de PCI DSS executando testes de penetração nos controles de segmentação pelo menos a cada seis meses e depois de alterações nos controles/métodos de segmentação.

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 11.3.4](#pci-dss-requirement-11-3-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations), que fornecem informações sobre vulnerabilidade e correção, foram usados para fazer com que todos os problemas pendentes fossem corrigidos no CDE de demonstração da Contoso Webstore.|



## <a name="pci-dss-requirement-114"></a>Requisito 11.4 de PCI DSS

**11.4** Usar técnicas de detecção de intrusão e/ou prevenção contra intrusão para detectar e/ou evitar invasões na rede. Monitorar todo o tráfego no perímetro do ambiente de dados do titular do cartão, bem como em pontos críticos no ambiente de dados do titular do cartão e alertar a equipe em caso de suspeita de comprometimento.
Manter todas as detecções de intrusão, mecanismos de prevenção, linhas de base e assinaturas atualizadas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure realiza uma análise em tempo real de eventos em seu ambiente operacional e os sistemas de IDS geram quase em tempo real alertas sobre eventos que podem comprometer o sistema. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore é um serviço de PaaS, e a prevenção e detecção de intrusão de rede é de responsabilidade do Azure. A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecem alertas de intrusão e a correção.|



## <a name="pci-dss-requirement-115"></a>Requisito 11.5 de PCI DSS

**11.5** Implantar um mecanismo de detecção de alteração (por exemplo, ferramentas de monitoramento de integridade de arquivos) para alertar a equipe sobre modificação não autorizada (incluindo alterações, adições e exclusões) de arquivos críticos do sistema, de arquivos de configuração ou de arquivos de conteúdo; e configurar o software para executar comparações de arquivos críticos pelo menos semanalmente. 

> [!NOTE]
> Para fins de detecção de alteração, os arquivos críticos normalmente são aqueles que não são alterados regularmente, mas cuja modificação pode indicar comprometimento do sistema ou risco de comprometimento. Mecanismos de detecção de alteração, como os produtos de monitoramento de integridade de arquivo, normalmente são pré-configurados com arquivos críticos do sistema operacional relacionado. Outros arquivos críticos, como aqueles para aplicativos personalizados, devem ser avaliados e definidos pela entidade (ou seja, o provedor de serviço ou distribuidor).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure mantém e notifica os clientes sobre possíveis alterações e eventos que podem afetar a segurança ou a disponibilidade dos serviços por meio de um Painel de Serviço online. As alterações nos compromissos e nas obrigações de segurança de clientes do Microsoft Azure são atualizadas no site do Microsoft Azure de maneira oportuna.<br /><br />A instalação ou alterações de software no ambiente de produção do Microsoft Azure são restritas à equipe autorizada e seguem procedimentos de gerenciamento de alterações. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A demonstração da Contoso Webstore é um serviço PaaS e a detecção de alterações foi implementada usando o OMS. Para saber mais, confira [Diretriz de PCI - Soluções do OMS pré-instaladas](payment-processing-blueprint.md#oms-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Requisito 11.5.1 de PCI DSS

**11.5.1** Implementar um processo para responder a todos os alertas gerados pela solução de detecção de alteração.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As regras de evento de monitoramento do Azure fornecem um nível maior de monitoramento para ativos e operações de alto risco. Os dispositivos de rede gerenciados pelo Azure são monitorados em relação à conformidade com padrões de segurança estabelecidos. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os alertas de alterações da Contoso Webstore são fornecidos pela implementação do OMS. Para saber mais, confira [Diretriz de PCI - Soluções do OMS pré-instaladas](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Requisito 11.6 de PCI DSS

**11.6** Garanta que políticas de segurança e procedimentos operacionais para o monitoramento e teste de segurança estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os alertas de alterações da Contoso Webstore são fornecidos pela implementação do OMS. Para saber mais, confira [Diretriz de PCI - Soluções do OMS pré-instaladas](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|




