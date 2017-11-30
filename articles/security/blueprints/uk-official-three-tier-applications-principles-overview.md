---
title: "Automação da Especificação Técnica do Azure – visão geral dos princípios de segurança na nuvem do National Cyber Security Centre"
description: "Automação da Especificação Técnica do Azure – visão geral dos princípios de segurança na nuvem do National Cyber Security Centre"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: jomolesk
ms.openlocfilehash: 0be18e2c2354ea8f766eb48db793c906e565a201
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Visão geral dos princípios de segurança na nuvem do National Cyber Security Centre


> [!NOTE]
> Esses princípios de segurança são definidos pelo NCSC (National Cyber Security Centre) do Reino Unido. Veja a [documentação do NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para obter informações sobre procedimentos de teste e diretrizes para cada princípio de segurança.



## <a name="ncsc-cloud-security-principle-1"></a>Princípio 1 de segurança na nuvem do NCSC
### <a name="data-in-transit-protection"></a>Proteção de dados em trânsito
Dados do usuário transmitidos em redes devem ser protegidos adequadamente contra violação e espionagem.

Para isso, é necessária a combinação de:

- proteção de rede – negar ao invasor a capacidade de interceptar dados
- criptografia – negar ao invasor a capacidade de ler dados


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure configura recursos para se comunicar usando somente protocolos seguros. O componente WAF do Gateway de Aplicativo está configurado para aceitar comunicações de usos externos por HTTPS/TLS e comunicar-se com o pool de back-end somente por HTTPS/TLS. Serviços de Área de Trabalho Remota são configurados para usar conexões seguras. VPN é usada para proteger o tráfego da Web entre AppGateway e o Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure usa o protocolo TLS 1.2 padrão do setor com chaves de criptografia RSA/SHA256 de 2048 bits, conforme recomendado pelo CESG/NCSC, para criptografar as comunicações entre o cliente e a nuvem, e internamente entre sistemas do Azure e datacenters. Por exemplo, quando os administradores usam o Portal do Microsoft Azure para gerenciar o serviço para sua organização, os dados transmitidos entre o portal e o dispositivo do administrador são enviados por um canal criptografado do TLS. Quando um usuário de email se conecta ao Outlook.com usando um navegador da Web padrão, a conexão HTTPS fornece um canal seguro para receber e enviar email.<br /> <br /> O Azure oferece uma variedade de opções para proteger os dados e o tráfego de seus clientes. Os recursos de gerenciamento de certificado criados no Azure propiciam ao administradores flexibilidade para configurar certificados e chaves de criptografia para sistemas de gerenciamento, serviços individuais, sessões do SSH (Secure Shell), conexões de VPN (rede virtual privada), conexões de RDP (área de trabalho remota) e outras funções. <br /><br /> Os desenvolvedores podem usar os CSPs (provedores de serviços de criptografia) embutidos no Microsoft .NET Framework para acessar algoritmos de criptografia AES (Advanced Encryption Standard), junto com a funcionalidade de algoritmo SHA-2 (Secure Hash Algorithm) para lidar com tarefas como validar assinaturas digitais. O Azure Key Vault ajuda os clientes a proteger segredos e chaves de criptografia, armazenando-os em HSMs (módulos de segurança de hardware). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Princípio 2 de segurança na nuvem do NCSC
### <a name="asset-protection-and-resilience"></a>Resiliência e proteção de ativos
Dados do usuário, e os ativos que armazenam ou processam esses dados, devem ser protegidos contra violação física, perda, danos ou a tomada de controle.

Considere os seguintes aspectos:

1. Local físico e jurisdição legal
2. Segurança do datacenter
3. Proteção de dados em repouso
4. Limpeza de dados
5. Descarte de equipamentos
6. Disponibilidade e resiliência físicas


 ## <a name="ncsc-cloud-security-principle-21"></a>Princípio 2.1 de segurança na nuvem do NCSC
### <a name="physical-location-and-legal-jurisdiction"></a>Local físico e jurisdição legal
Para entender as circunstâncias legais sob as quais seus dados podem ser acessados sem o seu consentimento, você deve identificar os locais em que eles são armazenados, processados e gerenciados.
Será necessário também entender como são impostos os controles de manipulação de dados dentro do serviço em relação à legislação do Reino Unido. Proteção inadequada de dados do usuário pode resultar em sanção legal e normativa ou danos à reputação.


**Responsabilidades:** `Customer`

> [!NOTE]
> Os serviços do Azure são implantados de forma regional, e os clientes podem configurar determinados serviços do Azure para armazenar dados de clientes somente em uma única região. O Microsoft Azure fornece uma lista de datacenters disponíveis em todo o mundo para oferecer disponibilidade e confiabilidade em escala global. Todos os datacenters do Azure são certificados em relação a ISO/IEC 27001:2013. A área geográfica do Reino Unido consiste em 2 regiões: Sul do Reino Unido e Oeste do Reino Unido.

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure solicita ao administrador em qual região implantar recursos do Azure. As regiões recomendadas para implantação são Sul do Reino Unido ou Oeste do Reino Unido. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | Não aplicável |


 ## <a name="ncsc-cloud-security-principle-22"></a>Princípio 2.2 de segurança na nuvem do NCSC
### <a name="datacentre-security"></a>Segurança do datacenter
Locais usados para oferecer serviços de nuvem precisam de proteção física contra acesso não autorizado, violação, roubo ou reconfiguração dos sistemas. Proteções inadequadas podem resultar na divulgação, alteração ou perda de dados.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a recursos do sistema nos datacenters do Azure; medidas de proteção à segurança do datacenter são implementadas e gerenciadas pelo Microsoft Azure. Esse princípio é herdado do Microsoft Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Azure implementa esse princípio em nome dos clientes. O Microsoft Azure é executado em instalações da Microsoft distribuídas geograficamente, compartilhando espaço e utilitários com outros serviços online da Microsoft. Cada instalação foi projetada para funcionar 24 horas por dia, 7 dias por semana, 365 dias do ano e emprega várias medidas padrão do setor para ajudar a proteger as operações contra falha de energia, invasão física e interrupções de rede. Esses datacenters estão em conformidade com padrões do setor (como ISO 27001) para segurança física e disponibilidade. Eles são gerenciados, monitorados e administrados pela equipe de operações da Microsoft. <br /> <br /> Clientes do Azure podem estar certos de que os controles de segurança física estão em vigor em todos os Azure datacenters, pois o Azure mantém certificados em todos os datacenters para p padrão ISO/IEC 27001:2013. A área geográfica do Reino Unido consiste em 2 regiões: Sul do Reino Unido e Oeste do Reino Unido. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Princípio 2.3 de segurança na nuvem do NCSC
### <a name="data-at-rest-protection"></a>Proteção de dados em repouso
Para garantir que os dados não fiquem disponíveis a pessoas não autorizadas com acesso físico à infraestrutura, os dados do usuário mantidos dentro do serviço devem ser protegidos independentemente da mídia de armazenamento na qual ele é mantido. Sem as medidas apropriadas em vigor, os dados podem ser divulgados por engano na mídia descartada, perdida ou roubada.


**Responsabilidades:** `Shared`

> [!NOTE]
> Todas as soluções de criptografia do Microsoft Azure oferecida aos clientes integram-se facilmente com o Azure Key Vault, que permite facilitar o gerenciamento das chaves de criptografia.

|||
|---|---|
| **Cliente** | A confidencialidade e a integridade de todo o armazenamento de blobs implantado por essa solução de projeto do Azure estão protegidas por meio da SSE (Criptografia do Serviço de Armazenamento) do Azure. A SSE protege dados em repouso em contas de armazenamento do Azure usando a criptografia AES de 256 bits. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure oferece uma ampla variedade de recursos de criptografia, dando aos clientes a flexibilidade para escolher a solução que melhor atende às suas necessidades. O Azure Key Vault ajuda os clientes a manter com economia e facilidade o controle das chaves usadas por aplicativos e serviços de nuvem para criptografar os dados. O Azure Disk Encryption permite criptografar as máquinas virtuais. A Criptografia do Serviço de Armazenamento do Microsoft Azure permite criptografar todos os dados colocados na conta de armazenamento de um cliente. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Princípio 2.4 de segurança na nuvem do NCSC
### <a name="data-sanitisation"></a>Limpeza de dados
O processo de provisionamento, migração e desprovisionamento de recursos não deve resultar no acesso não autorizado aos dados do usuário.

A limpeza de dados inadequada pode resultar em:

- Retenção de dados do usuário pelo provedor de serviço indefinidamente
- Acessibilidade aos dados do usuário a outros usuários do serviço, pois os recursos são reutilizados
- Perda ou divulgação dos dados do usuário na mídia descartada, perdida ou roubada.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | O Microsoft Azure fornece garantias contratuais sobre exclusão permanente de dados no Azure. Sendo assim, esse princípio é herdado do Microsoft Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Azure segue o processo de descarte do NIST SP800-88r1 com a classificação de dados alinhada ao FIPS-199 moderado. O NIST fornece uma abordagem de fácil eliminação (por meio do firmware de disco rígido) para as unidades que dão suporte a ele. A Microsoft destrói as unidades de disco rígido que não podem ser apagadas e renderiza a recuperação de informações impossível (por exemplo, desintegrar, despedaçar, pulverizar ou incinerar). O meio apropriado de descarte é determinado pelo tipo de ativo. Registros da destruição são retidos. Todos os serviços do Microsoft Azure usam os serviços aprovados de gerenciamento de armazenamento e descarte de mídia. <br />  <br /> Após a expiração ou o término de uma assinatura do serviço, o cliente pode contatar a Microsoft e pedir para: <br /><br /> (1) Desabilitar a conta do cliente e excluir os dados do cliente; ou <br /> (2) Manter os dados armazenados no serviço online em uma conta de função limitada por pelo menos 90 dias após a expiração ou o término da assinatura do cliente (o "período de retenção"). Dessa maneira, o cliente pode extrair os dados. Ao final do período de retenção, a Microsoft desabilita a conta do cliente e exclui todos os dados. Cópias armazenadas em cache ou de backup serão limpas dentro de 30 dias do final do período de retenção. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Princípio 2.5 de segurança na nuvem do NCSC
### <a name="equipment-disposal"></a>Descarte de equipamentos
Quando o equipamento usado para fornecer um serviço chega ao fim da vida útil, ele deve ser descartado de maneira a não comprometer a segurança do serviço nem os dados do usuário armazenados no serviço.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a recursos do sistema nos datacenters do Azure; procedimentos de descarte de equipamentos são implementados e gerenciados pelo Microsoft Azure. Esse princípio é herdado do Microsoft Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Azure implementa esse princípio em nome dos clientes. Após o fim da vida útil do sistema, a equipe operacional da Microsoft segue rigorosos procedimentos de manuseio de dados e processos de descarte de hardware para ajudar a garantir que nenhum hardware que possa conter dados de cliente seja disponibilizado a pessoas não confiáveis. O Microsoft Azure segue o processo de descarte do NIST SP800-88r1 com a classificação de dados alinhada ao FIPS-199 moderado. O NIST fornece uma abordagem de fácil eliminação (por meio do firmware de disco rígido) para as unidades que dão suporte a ele. A Microsoft destrói as unidades de disco rígido que não podem ser apagadas e renderiza a recuperação de informações impossível (por exemplo, desintegrar, despedaçar, pulverizar ou incinerar). O meio apropriado de descarte é determinado pelo tipo de ativo. Registros da destruição são retidos. Todos os serviços do Microsoft Azure usam os serviços aprovados de gerenciamento de armazenamento e descarte de mídia. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Princípio 2.6 de segurança na nuvem do NCSC
### <a name="physical-resilience-and-availability"></a>Disponibilidade e resiliência físicas
Serviços têm vários níveis de resiliência, que afetarão a capacidade de operar normalmente no caso de falhas, incidentes ou ataques. Um serviço sem garantias de disponibilidade pode ficar indisponível, possivelmente por períodos prolongados, independentemente do impacto nos negócios.


**Responsabilidades:** `Shared`

> [!NOTE]
> Quando o cliente configura o Microsoft Azure adequadamente, habilitando o Azure Site Recovery e armazenamento alternativo de dados em outro datacenter localizado em área geográfica, o Microsoft Azure pode dar suporte à operação contínua dos recursos implantados pelo cliente.

|||
|---|---|
| **Cliente** | O cliente é responsável por estabelecer um local de armazenamento alternativo. A instrução de implementação de controle do cliente deve resolver a capacidade de operar do cliente no caso de um incidente. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Azure tem datacenters aprovados pelo NCSC do Reino Unido em diferentes localizações geográficas (Sul do Reino Unido e Oeste do Reino Unido) para fornecer disponibilidade e resiliência. É responsabilidade do cliente reservar capacidade em uma região alternativa usando o serviço Site Recovery do Azure. Depois que o cliente configurar o Azure Site Recovery, o Azure iniciará e interromperá os serviços do cliente em uma transição suave para o local alternativo de processamento. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Princípio 3 de segurança na nuvem do NCSC
### <a name="separation-between-users"></a>Separação entre os usuários
Um usuário mal-intencionado ou comprometido do serviço não deve poder afetar o serviço ou os dados de outra pessoa.

Fatores que afetam a separação de usuários incluem:

- onde os controles de separação são implementados – isso é amplamente influenciado pelo modelo de serviço (por exemplo, IaaS, PaaS, SaaS)
- com quem você compartilha o serviço – isso é determinado pelo modelo de implantação (por exemplo, nuvem pública, privada ou de comunidade)
- o nível de garantia disponível na implementação de controles de separação

> [!NOTE]
> Em um serviço de IaaS, considere a separação fornecida pela computação, pelo armazenamento e pelos componentes de rede. Além disso, serviços de SaaS e PaaS baseados em IaaS podem herdar algumas das propriedades de separação da infraestrutura de IaaS subjacente.

**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | O Microsoft Azure garante o isolamento para cada usuário a fim de evitar que um usuário mal-intencionado ou comprometido afete o serviço ou os dados de outro. Sendo assim, esse princípio é herdado do Microsoft Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | Os servidores de nuvem do cliente são virtuais, por isso o paradigma de separação física já não se aplica. O Microsoft Azure foi projetado para ajudar a identificar e calcular os riscos inerentes em um ambiente multilocatário. O processamento e o armazenamento de dados são logicamente separados entre usuários do Azure usando o Active Directory e funcionalidade desenvolvida especificamente para serviços multilocatários, que têm como objetivo garantir que os dados do usuário armazenados nos datacenters compartilhados do Azure não fiquem acessíveis a outra organização. <br /> <br /> Fundamental para toda arquitetura de nuvem compartilhada é o isolamento para cada usuário a fim de evitar que um usuário mal-intencionado ou comprometido afete o serviço ou os dados de outro. <br /> <br /> Para obter mais informações sobre a separação de locatário da Microsoft, veja a descrição completa na [Especificação Técnica do Azure – Princípios de segurança na nuvem do NCSC – Matriz de responsabilidades do cliente](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Princípio 4 de segurança na nuvem do NCSC
### <a name="governance-framework"></a>Estrutura de governança
O provedor de serviços deve ter uma estrutura de governança de segurança que coordena e direciona o gerenciamento do serviço e das informações dentro dela. Os controles técnicos implantados fora dessa estrutura serão prejudicados significativamente.
Ter uma estrutura de governança efetiva garantirá que o procedimento, a equipe, os controles físicos e técnicos continuem a trabalhar ao longo do tempo de vida de um serviço. Ela também deve responder a mudanças no serviço, avanços tecnológicos e ao surgimento de novas ameaças.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | O Microsoft Azure mantém uma estrutura de governança de segurança documentada para serviços do Azure. Sendo assim, esse princípio é herdado do Microsoft Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A estrutura de conformidade da Microsoft inclui uma metodologia padrão para definir domínios de conformidade, determinando quais objetivos se aplicam a determinada equipe ou ativo e captura como objetivos de controle de domínio são resolvidos em detalhes suficientes que se aplicam a determinado conjunto de padrões do setor, regulamentações ou requisitos de negócios. A estrutura mapeia controles para vários padrões regulatórios. Com isso, a Microsoft pode projetar e criar serviços usando um conjunto comum de controles, simplificando a conformidade em uma variedade de regulamentações hoje e à medida que evoluem no futuro. <br /> <br /> Com os processos de conformidade da Microsoft, os clientes atingem com facilidade a conformidade em vários serviços e atendem às suas necessidades com eficiência. Juntos, a tecnologia de reforço da segurança e os processos de conformidade efetiva permitem que a Microsoft mantenha e expanda um conjunto avançado de certificados de terceiros. Essas certificações ajudam os clientes a demonstrar a preparação de conformidade para seus clientes, auditores e reguladores. <br /> <br />  O Azure está em conformidade com um amplo conjunto de padrões de conformidade internacionais e regionais específicos do setor, como ISO 27001, FedRAMP (Federal Risk and Authorization Management Program), SOC 1 e SOC 2. A conformidade com os rígidos controles de segurança contidos nesses padrões é verificada por rigorosas auditorias de terceiros que demonstram que os serviços do Azure funcionam e atendem a padrões do setor, certificações, atestados e autorizações de nível mundial. <br /> <br /> O Azure foi criado com uma estratégia de conformidade que ajuda os clientes a conquistar seus objetivos de negócios e cumprir padrões e regulamentações do setor. A estrutura de conformidade de segurança inclui fases de teste e auditoria, análise de segurança, melhores práticas de gerenciamento de riscos e análise de parâmetro de comparação de segurança para obter certificados e atestados. <br /> <br /> Para obter mais informações sobre a adesão da Microsoft a estruturas de conformidade, veja a descrição completa na [Especificação Técnica do Azure – Princípios de segurança na nuvem do NCSC – Matriz de responsabilidades do cliente](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Princípio 5 de segurança na nuvem do NCSC
### <a name="operational-security"></a>Segurança operacional
O serviço precisa ser operado e gerenciado de modo seguro para impedir, detectar ou prevenir ataques. Uma boa segurança operacional não deve exigir processos complexos, burocráticos, demorados nem caros.

Quatro elementos devem ser considerados:

- Configuração e gerenciamento de alterações – garanta que as alterações no sistema foram testadas e autorizadas corretamente. As alterações não devem alterar as propriedades de segurança inesperadamente
- Gerenciamento de vulnerabilidades – identifique e atenue os problemas de segurança nos componentes constituintes
- Monitoramento de proteção – coloque medidas em vigor para detectar ataques e atividade não autorizada no serviço
- Gerenciamento de incidentes – verifique se você pode responder a incidentes e recuperar um serviço seguro, disponível




 ## <a name="ncsc-cloud-security-principle-51"></a>Princípio 5.1 de segurança na nuvem do NCSC
### <a name="configuration-and-change-management"></a>Configuração e gerenciamento de alterações
Tenha uma imagem precisa dos ativos que compõem o serviço, junto com suas configurações e dependências.
As alterações que poderiam afetar a segurança do serviço devem ser identificadas e gerenciadas. Alterações não autorizadas devem ser detectadas.
Quando a alteração não é gerenciada com eficiência, vulnerabilidades de segurança podem ser introduzidas inadvertidamente a um serviço. E, mesmo quando há reconhecimento da vulnerabilidade, talvez ela não possa ser totalmente reduzida.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Os modelos do Azure Resource Manager e os recursos que compõem essa Especificação Técnica do Azure representam uma linha de base de "configuração como código" para a arquitetura implantada. A solução é fornecida pelo GitHub, que pode ser usado para controle de configuração. <br /> <br /> Os privilégios da conta do Azure Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a funções com controle estrito sobre quais usuários podem exibir e controlar os recursos implantados. Os privilégios de conta do Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a grupos de segurança. Esses grupos de segurança controlam as ações que os usuários podem executar em relação à configuração do sistema operacional. Esses esquemas baseados em função podem ser estendidos pelo cliente para atender às necessidades da tarefa. <br /> <br /> Para ser compatível com esse princípio, são necessárias outras configurações do cliente para uso em produção. Assim, essas configurações deverão fazer parte do processo de gerenciamento de alterações do cliente. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Azure faz revisões e atualizações de definições de configuração e configurações de linha de base de dispositivos de hardware, software e rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrar no ambiente de produção de um ambiente de desenvolvimento e/ou teste. <br /> <br />O Microsoft Azure aplica as configurações de linha de base usando o processo de alteração e versão para componentes de software do Microsoft Azure (por exemplo, sistema operacional, malha, RDFE, XStore etc.) e o processo de configuração de inicialização para componentes de dispositivo de hardware e de rede entrando no ambiente de produção do Microsoft Azure conforme descrito abaixo. <br /> <br /> As configurações de linha de base necessárias para os serviços baseados no Azure são revisadas pela equipe de conformidade e segurança do Azure e pelas equipes de serviço como parte dos testes antes da implantação de seu serviço de produção. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Princípio 5.2 de segurança na nuvem do NCSC
### <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades
Provedores de serviço devem ter um processo de gerenciamento em vigor para identificar, fazer triagem e atenuar vulnerabilidades. Caso contrário, eles se tornarão rapidamente vulneráveis a ataques usando ferramentas e métodos publicamente conhecidos.


**Responsabilidades:** `Customer`


|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de vulnerabilidades nos recursos por ele implantados (para incluir aplicativos, sistemas operacionais, bancos de dados e software). A instrução de implementação do cliente deve tratar as ferramentas usadas para executar as verificações de vulnerabilidade. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O gerenciamento de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. O Azure usa sistemas de implantação integrados para gerenciar a distribuição e a instalação de atualizações de segurança para software da Microsoft. O Azure também pode desenhar com base nos recursos do MSRC (Microsoft Security Response Centre), que identifica, monitora, responde e resolve incidentes de segurança e vulnerabilidades na nuvem a qualquer hora, todos os dias do ano. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Princípio 5.3 de segurança na nuvem do NCSC
### <a name="protective-monitoring"></a>Monitoramento de proteção
Um serviço que não monitora com eficácia quanto a ataque, uso indevido e mau funcionamento provavelmente não detecta ataques (bem-sucedidos ou não). Como resultado, ele não poderá responder rapidamente aos possíveis dados em seus ambientes e dados.


**Responsabilidades:** `Customer`


|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar os recursos por ele implantados (para incluir aplicativos, sistemas operacionais, bancos de dados e software). A instrução de implementação de controle do cliente deve resolver os mecanismos para monitorar, detectar e responder a ataques, uso indevido e mal funcionamento. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A segurança do Microsoft Azure definiu os requisitos para monitoramento ativo. As equipes de serviço configuram ferramentas de monitoramento ativo de acordo com esses requisitos. As ferramentas de monitoramento ativo incluem o MA (Agente de Monitoramento) e o SCOM (System Centre Operations Manager) que são configurados para fornecer alertas em tempo real para a equipe de segurança do Microsoft Azure em situações que exigem ação imediata. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Princípio 5.4 de segurança na nuvem do NCSC
### <a name="incident-management"></a>Gerenciamento de incidentes
A menos que processos de gerenciamento de incidentes previamente planejado com cuidado estejam em vigor, más decisões podem ser tomadas quando ocorrem incidentes, potencialmente agravando ainda mais o impacto geral sobre usuários.
Esses processos não precisam ser complexos nem exigem grandes quantidades de descrição, mas o bom gerenciamento de incidentes minimiza o impacto para os usuários de problemas segurança, confiabilidade e questões ambientais com um serviço.


**Responsabilidades:** `Shared`

> [!NOTE]
> Nos casos em que os incidentes de segurança do cliente podem afetar o status de segurança do Microsoft Azure, o cliente é responsável por notificar o Microsoft Azure.

|||
|---|---|
| **Cliente** | O cliente é responsável por estabelecer um processo de gerenciamento de incidentes para recursos implantados por clientes (para incluir aplicativos, sistemas operacionais, bancos de dados e software). A instrução de implementação do cliente deve resolver incidentes de emissão de relatórios e alertas, com suporte a respostas a incidentes em tempo hábil e encaminhamento de informações para o PGA e a outras organizações HMG conforme apropriado. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft implementou um processo de gerenciamento de incidentes de segurança para facilitar uma resposta coordenada a incidentes caso eles ocorram. <br /> <br /> Se a Microsoft souber de algum acesso não autorizado aos dados armazenados em seu equipamento ou em suas instalações, ou acesso não autorizado a tais equipamentos ou instalações resultando em perda, divulgação ou alteração de dados do cliente, a Microsoft tomará as seguintes medidas: <br /> <br />   – Notificará imediatamente o cliente sobre o incidente de segurança; <br /> – Investigará o incidente de segurança imediatamente e fornecerá ao cliente informações detalhadas sobre o ocorrido; e <br /> – Tomará medidas razoáveis e imediatas para reduzir os efeitos e minimizar algum dano resultante do incidente de segurança.  <br />  <br /> Foi estabelecida uma estrutura de gerenciamento de incidentes com funções definidas e responsabilidades alocadas. A equipe do WASIM (Gerenciamento de Incidentes de Segurança do Microsoft Azure) é responsável por gerenciar incidentes de segurança, incluindo o escalonamento e garantindo o envolvimento das equipes de especialistas quando necessário. Os gerentes de operações do Azure são responsáveis por supervisionar a investigação e a resolução de incidentes de segurança e privacidade com suporte das outras funções. <br /> <br /> Para obter mais informações sobre os processos de resposta a incidentes da Microsoft, veja a descrição completa na [Especificação Técnica do Azure – Princípios de segurança na nuvem do NCSC – Matriz de responsabilidades do cliente](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Princípio 6 de segurança na nuvem do NCSC
### <a name="personnel-security"></a>Segurança de pessoal
Quando o pessoal do provedor de serviço tem acesso aos seus dados e sistemas, é necessário um alto grau de confiança em sua credibilidade. Triagem completa, com suporte pelo treinamento adequado, reduz a probabilidade de comprometimento acidental ou mal-intencionado pela equipe do provedor de serviço.
O provedor do serviço deve sujeitar a equipe à triagem de segurança e de treinamento de segurança regular. A equipe nessas funções deve compreender suas responsabilidades. Os provedores devem deixar claro como eles fazem a triagem e gerenciam a equipe em funções com privilégios.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por fazer a triagem dos indivíduos e oferecer treinamento de segurança regular às pessoas com acesso aos recursos implantados pelo cliente. A instrução de implementação do cliente deve tratar os critérios de triagem para as funções e a frequência do treinamento de segurança. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A equipe do Microsoft Azure que opera os serviços do Azure e dá suporte ao cliente (ou prestadores de serviços da Microsoft que auxiliam em operações de plataforma, solução de problemas e suporte técnico) passa por uma verificação de histórico (ou equivalente) padrão da Microsoft para avaliar formação, emprego e antecedente crimina do funcionário. As verificações de histórico estão bem alinhadas com os requisitos da norma BPSS/BS7858 do governo do Reino Unido. Especificamente, elas não incluem uma verificação de identidade formal.  <br /> <br /> A Microsoft inclui disposições de confidencialidade em seus contratos com funcionários e prestadores de serviços. Todos os funcionários e prestadores de serviços da Microsoft participam de um programa de treinamento de segurança patrocinado pelo Microsoft Azure que informa à equipe suas responsabilidades quanto à segurança de informações. <br /> <br /> A equipe de serviços ou os prestadores de serviços do Microsoft Azure suspeitos de cometer violações de segurança de confirmação e/ou violar a política de segurança de informações estão sujeitos a um processo de investigação e uma ação disciplinar adequada, que pode chegar à rescisão. Se as circunstâncias justificarem, a Microsoft poderá recorrer a um processo por um órgão de aplicação da lei. <br /> <br /> Para complementar esse sistema de verificações de histórico e treinamento de segurança, a Microsoft implanta combinações de controles preventivos, defensivos e reativos para ajudar a proteger contra atividades administrativas e/ou do desenvolvedor não autorizadas, incluindo os seguintes mecanismos: <br />  <br /> – Rígidos controles de acesso em dados confidenciais, incluindo um requisito para autenticação de dois fatores com base em cartão inteligente para executar operações confidenciais. <br /> – Combinações de controles que melhoram a detecção independente de atividades mal-intencionadas. <br /> – Vários níveis de monitoramento, registro e relatórios. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Princípio 7 de segurança na nuvem do NCSC
### <a name="secure-development"></a>Desenvolvimento seguro
Serviços devem ser criados e desenvolvidos para identificar e reduzir as ameaças à segurança. Serviços desprotegidos talvez fiquem vulneráveis a problemas de segurança que podem comprometer seus dados, causar perda de serviço ou permitir outras atividades mal-intencionadas.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows fornece validação de integridade de arquivo em tempo real, proteção e recuperação de arquivos do sistema de núcleo que são instalados como parte do Windows ou de atualizações do sistema Windows autorizadas pela funcionalidade WRP (Proteção de Recursos do Windows), que permite a verificação de integridade em tempo real. <br /> <br /> O Windows tem proteções em vigor para impedir a execução de código em locais de memória restrito: NX (No Execute), ASLR (Address Space Layout Randomization) e DEP (Prevenção de Execução de Dados). <br /> <br /> Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando o Microsoft Windows Defender. O Windows Defender está configurado para atualizar automaticamente as duas assinaturas de proteção e o mecanismo antimalware quando a versão ficar disponível. <br /> <br /> Para ser compatível com esse princípio, são necessárias outras configurações do cliente para uso em produção. Assim, essas configurações deverão fazer parte do processo de desenvolvimento seguro do cliente. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O SDL (Microsoft Security Development Lifecycle) fornece um processo de modelagem de ameaças eficaz para identificar ameaças e vulnerabilidades em software e serviços. A modelagem de ameaça é um exercício de equipe, abrangendo o gerente de operações, os gerentes de programa/projeto, os desenvolvedores e testadores, e representa uma tarefa de análise de segurança importante executada para o design da solução. Os membros da equipe usam a ferramenta de modelagem de ameaças do SDL para modelar todos os serviços e projetos quando eles são criados e atualizados com novos recursos e funcionalidades. Modelos de ameaças abrangem todo o código exposto na superfície de ataque e todo o código desenvolvido por ou licenciado de terceiros e considera todos os limites de confiança. O sistema STRIDE (falsificação, violação, repúdio, divulgação de informações, negação de serviço e elevação de privilégio) é usado para ajudar a identificar e resolver as ameaças de segurança no início do processo de design, antes que elas possam afetar os clientes. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Princípio 8 de segurança na nuvem do NCSC
### <a name="supply-chain-security"></a>Segurança da cadeia de fornecedores
O provedor de serviços deve garantir que sua cadeia de fornecimento dê suporte satisfatoriamente a todos os princípios de segurança que o serviço reivindica para implementação.
Serviços de nuvem geralmente dependem de serviços e produtos de terceiros. Consequentemente, se não for implementado esse princípio, o comprometimento de cadeia de fornecimento poderá prejudicar a segurança do serviço e afetar a implementação dos outros princípios de segurança.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer a documentação de segurança da cadeia de fornecimento para todo software e sistemas operacionais de terceiros usados em sua assinatura do Azure. A instrução de implementação do cliente deve tratar a exceção para seguir processos identificados por essa documentação da cadeia de fornecimento. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O grupo MCSC (Cadeia de Fornecimento do Microsoft Cloud) consiste em seis equipes exclusivas, sendo que cada uma contribui para a proteção do Azure contra ameaças à cadeia de fornecimento.  <br />  <br /> – Compras <br /> – Operações do cliente <br /> – Qualidade da implantação <br /> – Gerenciamento de relacionamento com o fornecedor <br /> – Peças de reposição <br />  <br /> Para obter mais informações sobre o grupo MCSC da Microsoft, veja a descrição completa na [Especificação Técnica do Azure – Princípios de segurança na nuvem do NCSC – Matriz de responsabilidades do cliente](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Princípio 9 de segurança na nuvem do NCSC
### <a name="secure-user-management"></a>Gerenciamento seguro de usuários
Seu provedor deve disponibilizar as ferramentas para gerenciar com segurança o uso do seu serviço. Procedimentos e interfaces de gerenciamento são vitais para a barreira de segurança, impedindo o acesso não autorizado e a alteração de seus recursos, aplicativos e dados.

Considere os seguintes aspectos:

- Autenticação de usuários para interfaces de gerenciamento e canais de suporte
- Separação e controle de acesso em interfaces de gerenciamento



 ## <a name="ncsc-cloud-security-principle-91"></a>Princípio 9.1 de segurança na nuvem do NCSC
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Autenticação de usuários para interfaces de gerenciamento e em canais de suporte
Para manter um serviço seguro, os usuários precisam ser autenticados corretamente antes de terem permissão para executar atividades de gerenciamento, relatar falhas ou solicitar alterações no serviço.
Essas atividades podem ser realizadas por meio de um portal da Web de gerenciamento de serviços, ou por outros canais, como telefone ou email. É provável que existam funções como provisionamento de novos elementos de serviço, gerenciamento de contas de usuário e de dados de usuário.
Provedores de serviço precisam garantir que todas as solicitações de gerenciamento que podem afetar a segurança sejam executadas por canais seguros e autenticados. Sem autenticação forte dos usuários, um impostor pode executar com êxito as ações privilegiadas, prejudicando a segurança do serviço ou dados.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Quando os administradores acessam o Portal do Microsoft Azure para gerenciar recursos do Azure para sua organização, os dados transmitidos entre o portal e o dispositivo do administrador são enviados por um canal criptografado TLS usando as chaves de criptografia RSA/SHA256 de 2048 bits, como recomendado pelo CESG/NCSC.  <br /> <br /> Esta Especificação Técnica do Azure emprega a autenticação do Windows, a área de trabalho remota e o BitLocker. Esses componentes podem ser configurados para confiar em módulos criptográficos validados do padrão FIPS 140. <br /> <br /> Esta Especificação Técnica do Azure impõe autorizações de acesso lógico usando controle de acesso baseado em função imposto pelo Azure Active Directory, atribuindo usuários a funções, pelo Active Directory atribuindo usuários a grupos de segurança e pelos controles no nível do sistema operacional Windows. Funções do Azure Active Directory atribuídas a usuários ou grupos controlam o acesso lógico a recursos no Azure no nível do recurso, do grupo ou da assinatura. Grupos de segurança do Active Directory controlam o acesso lógico a funções e recursos no nível do sistema operacional. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | Os clientes administram seus recursos do Azure por meio do portal do Azure, que fornece acesso a todas as máquinas virtuais, bancos de dados, serviços de nuvem e outros recursos configurados para a conta do cliente. O acesso via Web para o portal do Azure é protegido por conexões TLS 1.2 padrão do setor usando chaves de criptografia RSA/SHA256 de 2048 bits, como recomendado pelo CESG/NCSC. Controles de acesso baseado em função são fornecidos para permitir que os clientes forneçam acesso limitado a recursos de gerenciamento do Azure para usuários e grupos específicos. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Princípio 9.2 de segurança na nuvem do NCSC
### <a name="separation-and-access-control-within-management-interfaces"></a>Separação e controle de acesso em interfaces de gerenciamento
Muitos serviços de nuvem são gerenciados por meio de APIs ou aplicativos Web. Essas interfaces são uma parte importante da segurança do serviço. Se os usuários não são separados adequadamente em interfaces de gerenciamento, um usuário pode afetar o serviço ou modificar os dados de outra pessoa.
Suas contas administrativas privilegiadas provavelmente têm acesso a grandes volumes de dados. Restringir as permissões de usuários individuais para o absolutamente necessário pode ajudar a limitar os danos causados por usuários mal-intencionados, credenciais ou dispositivos comprometidos.
O controle de acesso baseado em função fornece um mecanismo para fazer isso e provavelmente será uma funcionalidade importante para usuários que gerenciam implantações maiores.
Expor interfaces de gerenciamento a redes menos acessíveis (por exemplo, comunidade, em vez de redes públicas) dificulta o acesso de invasores, pois primeiro será necessário obter acesso a uma dessas redes. No Princípio 11, há orientação sobre como avaliar os riscos de expor interfaces a tipos diferentes de redes.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta um Gateway de Aplicativo, um balanceador de carga e configura as regras de grupo de segurança de rede para controlar as trocas em limites externos e entre sub-redes internas. A funcionalidade do usuário é separada da funcionalidade de gerenciamento do sistema por meio da aplicação de controles de acesso lógico e a da arquitetura do sistema. As interfaces para a funcionalidade de gerenciamento do sistema são independentes das interfaces do usuário. Toda a conectividade de gerenciamento é executada por meio de um host bastião seguro (jumpbox) localizado em uma sub-rede de gerenciamento com regras do grupo de segurança de rede para limitar o acesso aos recursos de produção conforme adequado. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | Conforme descrito, a separação entre usuários é criada no Azure em sua essência. O Azure AD ou AAD (Azure Active Directory) pode ser usado para fornecer a cada usuário que se autentica no portal do Azure acesso aos recursos que eles podem ver e gerenciar. Como resultado, as contas de clientes diferentes são estritamente separadas umas das outras quando gerenciadas por meio do portal comum do Azure. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Princípio 10 de segurança na nuvem do NCSC
### <a name="identity-and-authentication"></a>Identidade e autenticação
Todo o acesso a interfaces de serviço deve ser restrito a indivíduos autenticados e autorizados.
Autenticação fraca a essas interfaces pode permitir o acesso não autorizado aos seus sistemas, resultando em roubo ou modificação de dados, alterações no serviço ou negação de serviço.
É importante que a autenticação ocorra pode meio de canais seguros. Email, HTTP ou telefone são vulneráveis a ataques de interceptação e engenharia social.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure emprega o Active Directory para gerenciamento de conta. O acesso aos recursos implantados por esta Especificação Técnica do Azure é protegido contra ataques de repetição pela funcionalidade interna Kerberos do Azure Active Directory, pelo Active Directory e pelo sistema operacional Windows. Na autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, como uma lista de IP criptografada, carimbos de data/hora do cliente e vida útil do tíquete. Se um pacote é repetido, o carimbo de data/hora é verificado. Se o carimbo de data/hora for anterior ou igual a um autenticador anterior, o pacote será rejeitado. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure fornece serviços para ajudar a acompanhar a identidade, assim como integrá-la a locais de armazenamento de identidade que talvez já estejam em uso. O Azure AD é um serviço de gerenciamento de acesso e identidade abrangente para a nuvem que ajuda a proteger o acesso aos dados locais e aplicativos na nuvem. O Azure AD também simplifica o gerenciamento de usuários e grupos, combinando os serviços de diretório principal, governança de identidade avançada, segurança e gerenciamento de acesso de aplicativos. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Princípio 11 de segurança na nuvem do NCSC
### <a name="external-interface-protection"></a>Proteção de interface externa
Todas as interfaces externas ou menos confiáveis do serviço devem ser identificadas e adequadamente protegidas.
Se algumas das interfaces expostas forem particulares (como interfaces de gerenciamento), o impacto do comprometimento poderá ser mais significativo.
Você pode usar modelos diferentes para se conectar aos serviços de nuvem que expõem seus sistemas corporativos a níveis variados de risco.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema (por exemplo, o tráfego externo não pode acessar o banco de dados, o gerenciamento nem sub-redes do Active Directory).  <br /> <br /> Um Gateway de Aplicativo é implantado para gerenciar conexões externas para um aplicativo Web de implantação do cliente. Conexões externas para acesso de gerenciamento são restritas ao jumpbox (host bastião) implantado em uma sub-rede de gerenciamento com as regras de segurança de rede aplicadas para restringir as conexões externas a endereços IP autorizados. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A Microsoft emprega um método chamado "Red Teaming" para melhorar os controles de segurança do Azure e os processos por meio de testes de penetração regulares. O Red Team é um grupo de funcionários de tempo integral da Microsoft que se concentrar em executar ataques direcionados e persistentes contra a infraestrutura, as plataformas e os aplicativos da Microsoft, mas não contra os aplicativos e dados dos clientes finais. <br /> <br /> Para obter mais informações sobre o Red Teaming da Microsoft e uma a descrição dos esforços do Blue Teaming, veja a descrição completa na [Especificação Técnica do Azure – Princípios de segurança na nuvem do NCSC – Matriz de responsabilidades do cliente](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Princípio 12 de segurança na nuvem do NCSC
### <a name="secure-service-administration"></a>Administração segura do serviço
Sistemas usados para a administração de um serviço de nuvem têm acesso altamente privilegiado ao serviço. Um comprometimento teria um impacto significativo, incluindo os meios para ignorar os controles de segurança e roubar ou manipular grandes volumes de dados.
O design, a implementação e o gerenciamento de sistemas de administração devem seguir boas práticas empresariais, enquanto reconhecem seu alto valor aos invasores.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por garantir uma estação de trabalho segura para administração da assinatura do Azure e recursos implantados por ele (para incluir aplicativos, sistemas operacionais, bancos de dados e software). A instrução de implementação do cliente deve tratar os mecanismos usados para reduzir o risco de exploração dos recursos implantados pelo cliente. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | A equipe de operações do Microsoft Azure deve usar estações de trabalho do administrador seguras (SAWs; também conhecida como estações de trabalho de acesso privilegiado, ou PAWs). A abordagem SAW é uma extensão da prática recomendada bem estabelecida para usar contas de usuário e administrador separadas para a equipe administrativa. Esta prática usa uma conta administrativa individualmente atribuída que é completamente separada da conta de usuário padrão. A SAW se baseia nessa prática de separação de conta fornecendo uma estação de trabalho confiável para essas contas confidenciais. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Princípio 13 de segurança na nuvem do NCSC
### <a name="audit-information-for-users"></a>Informações de auditoria para usuários
Você deve receber os registros de auditoria necessários para monitorar o acesso ao seu serviço e aos dados mantidos dentro dele. O tipo de informações de auditoria disponíveis para você terá impacto direto na sua capacidade de detectar e responder a atividades mal-intencionadas ou inadequadas em um prazo razoável.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Eventos auditados por esta Especificação Técnica do Azure incluem os auditados pelos logs de atividades do Azure para os recursos implantados, logs de nível de sistema operacional e logs do Active Directory. Esses logs de eventos incluem informações suficientes para determinar quando ocorrem eventos, a origem do evento, o resultado do evento e outras informações detalhadas que dão suporte à investigação de incidentes de segurança. Os clientes podem selecionar eventos adicionais a serem auditados para atender às necessidades da tarefa. Todos os recursos do Azure têm logs de auditoria disponíveis no Portal do Azure. |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure Log Analytics coleta registros de eventos que ocorreram dentro de redes e sistemas de uma organização assim que eles ocorrem, antes que alguém possa violá-los, e permite diferentes tipos de análise, correlacionando os dados em vários computadores. O Azure permite que os clientes gerem e coletem eventos de segurança das funções IaaS e PaaS do Azure para armazenamento central nas assinaturas deles. Esses eventos coletados podem ser exportados para sistemas SIEM (gerenciamento de informações e eventos de segurança) locais com o objetivo de realizar um monitoramento contínuo. Depois de transferir os dados para armazenamento, há muitas opções para exibir os dados de diagnóstico. <br /> <br /> O diagnóstico interno do Azure pode ajudar na depuração. Para aplicativos implantados no Azure, um conjunto de eventos de segurança do sistema operacional está habilitado por padrão. Os clientes podem adicionar, remover ou modificar os eventos para auditoria por meio da personalização da política de auditoria do sistema operacional. <br /> <br /> Em um nível alto, é muito fácil e simples começar a coletar logs usando o WEF (Windows Event Forwarding) ou o Diagnóstico do Azure mais avançado quando VMs baseadas em Windows são implantadas usando o IaaS no Azure. Além disso, o Diagnóstico do Azure pode ser configurado para coletar logs e eventos de instâncias de função de PaaS. Ao usar VMs baseadas em IaaS, basta ao cliente configurar e permitir os eventos de segurança desejados da mesma forma que permite que servidores Windows registrem auditorias no datacenter local. Para aplicativos Web, também será possível habilitar o log do IIS se esse for o aplicativo principal e a implantação no Azure. Os clientes sempre podem armazenar dados de segurança em contas de armazenamento em áreas geográficas com suporte de sua escolha para atender aos requisitos de soberania de dados. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Princípio 14 de segurança na nuvem do NCSC
### <a name="secure-use-of-the-service"></a>Uso seguro do serviço
A segurança dos serviços de nuvem e dos dados mantidos dentro deles pode ser prejudicada se você usa o serviço inadequadamente. Consequentemente, você terá determinadas responsabilidades ao usar o serviço para que os dados sejam protegidos da maneira correta.
A extensão dessa responsabilidade varia dependendo dos modelos de implantação do serviço de nuvem e do cenário no qual você pretende usar o serviço. Recursos específicos de serviços individuais também podem ter relevância. Por exemplo, a forma como uma rede de distribuição de conteúdo protege sua chave privada ou como um provedor de pagamento na nuvem detecta transações fraudulentas são considerações de segurança importantes além das considerações gerais cobertas pelos princípios de segurança na nuvem.  
Com ofertas de IaaS e PaaS, você é responsável pelos aspectos significativos da segurança de dados e cargas de trabalho. Por exemplo, se você tiver uma instância de computação de IaaS, normalmente será responsável por instalar um sistema operacional moderno, configurando-o com segurança, implantando todos os aplicativos de maneira segura e, também, por manter essa instância por meio da aplicação de patches ou da execução da manutenção necessária.


**Responsabilidades:** `Customer`

> [!NOTE]
> O cliente pode usar a Central de Segurança do Azure para ajudar a prevenir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. A Central de Segurança do Azure permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

|||
|---|---|
| **Cliente** | Os modelos do Azure Resource Manager e os recursos que compõem esta Especificação Técnica do Azure seguem uma abordagem de defesa profunda em relação à segurança. Para ser compatível com esse princípio, são necessárias outras configurações do cliente para uso em produção (por exemplo, software de gerenciamento de banco de dados, implantação de aplicativo Web). |
| **Provedor&nbsp;(Microsoft&nbsp;Azure)** | Não aplicável |

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de sua organização em implementações do cliente aprovadas.