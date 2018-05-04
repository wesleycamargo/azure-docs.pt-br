---
title: "Projeto de segurança e conformidade do Azure – Automação de aplicativos Web para FedRAMP – Proteção de mídia"
description: "Automação de aplicativos Web para FedRAMP – Proteção de mídia"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 37812c2f7ee79685f9014a7999b4355e649ca6e1
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="media-protection-mp"></a>Proteção de mídia (MP)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-mp-1"></a>Controle NIST 800-53 MP-1

#### <a name="media-protection-policy-and-procedures"></a>Procedimentos e política de proteção de mídia

**MP-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de proteção de mídia que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de proteção de mídia e os controles de proteção de mídia associados; e revisa e atualiza a política de proteção de mídia atual [Atribuição: frequência definida pela organização]; e os procedimentos de proteção de mídia [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e procedimentos de proteção de mídia de nível empresarial do cliente pode ser suficiente para endereçar este controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-mp-2"></a>Controle NIST 800-53 MP-2

#### <a name="media-access"></a>Acesso à mídia

**MP-2** A organização restringe o acesso a [Atribuição: tipos de mídia digital e/ou não digital definidos pela organização] para [Atribuição: pessoal ou funções definidas pela organização].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure implementou o acesso à mídia por meio da implementação da Política de segurança da Microsoft. O acesso lógico à mídia digital é controlado por meio dos objetos de política de grupo do Active Directory (AD GPOs) e grupos de segurança. O acesso físico a todas as mídias é restringido pelo processo de acesso do datacenter. O acesso é restrito a indivíduos que tenham finalidade de negócios legítima para acessar os dados. Consulte PE-3, Controle de acesso físico, para obter mais detalhes sobre os controles de acesso de datacenter em vigor. O padrão de proteção de ativos define as garantias necessárias para proteger a confidencialidade, integridade e disponibilidade dos ativos de informações em datacenters do Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>Controle NIST 800-53 MP-3.a

#### <a name="media-marking"></a>Marcação de mídia

**MP-3.a** A organização marca a mídia do sistema de informações indicando as limitações de distribuição, advertências de manuseio e marcações de segurança aplicáveis (se houver) das informações.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure marca ativos em datacenters da Microsoft com uma designação HBI, MBI ou LBI (alto, médio e baixo impacto nos negócios, respectivamente), o que exige diferentes níveis de segurança e precauções de manuseio. Os proprietários dos ativos precisam classificar seus ativos armazenados em um datacenter da Microsoft. Consulte o Padrão de classificação de ativos e o Padrão de proteção de ativos para obter mais informações. |


 ## <a name="nist-800-53-control-mp-3b"></a>Controle NIST 800-53 MP-3.b

#### <a name="media-marking"></a>Marcação de mídia

**MP-3.b** A organização isenta [Atribuição: tipos de mídia do sistema de informações definidos pela organização] de marcação desde que a mídia permaneça em [Atribuição: áreas controladas definidas pela organização].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure exige que os proprietários dos ativos atribuam seus ativos a uma classificação de ativos e nenhum ativo está isento dessa exigência. No ambiente de datacenter da Microsoft, os ativos referem-se a servidores, dispositivos de rede e fitas magnéticas. Outras mídias digitais, como unidades USB, discos rígidos externos/removíveis ou CDs/DVDs, não são usadas. Mídias não digitais não são usadas no datacenter. |


 ## <a name="nist-800-53-control-mp-4a"></a>Controle NIST 800-53 MP-4.a

#### <a name="media-storage"></a>Armazenamento de mídia

**MP-4.a** A organização controla fisicamente e armazena com segurança [Atribuição: tipos de mídia digital e/ou mídia não digital definidos pela organização] em [Atribuição: áreas controladas definidas pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | Os ativos de mídia digital do Microsoft Azure são armazenados fisicamente e com segurança em salas de colocação do datacenter. Datacenters da Microsoft têm diversas camadas de controles de acesso físico (credencial de acesso, biometria; consulte PE-3 para obter mais detalhes sobre controles de acesso físico) e vigilância em vídeo em vigor para fornecer armazenamento seguro. As mídias digitais incluem servidores, dispositivos de rede e fitas magnéticas usados para backup. As mídias não digitais não são usadas no ambiente do datacenter. |


 ## <a name="nist-800-53-control-mp-4b"></a>Controle NIST 800-53 MP-4.b

#### <a name="media-storage"></a>Armazenamento de mídia

**MP-4.b** A organização protege a mídia do sistema de informações até que ela seja destruída ou limpa usando equipamentos, técnicas e procedimentos aprovados.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | Os ativos de mídia digital do Microsoft Azure são protegidos nas colocações do datacenter da Microsoft por meio de controles de acesso físico (PE-3) e controles de acesso lógico (IA-2) durante toda a vida útil. Os ativos do Microsoft Azure são limpos, removidos ou destruídos com métodos consistentes com o NIST SP 800-88 antes de serem descartados. Para destruir o ativo, o Microsoft Azure utiliza os serviços de destruição de ativos locais. |


 ## <a name="nist-800-53-control-mp-5a"></a>Controle NIST 800-53 MP-5.a

#### <a name="media-transport"></a>Transporte de dados

**MP-5.a** A organização protege e controla [Atribuição: tipos de mídia do sistema de informações definidos pela organização] durante o transporte fora de áreas controladas usando [Atribuição: proteções de segurança definidas pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | As mídias digitais do Microsoft Azure em datacenters da Microsoft consistem em servidores, dispositivos de rede e fitas e discos magnéticos de backup, quando apropriado. Os datacenters da Microsoft não usam mídia não digital. A Microsoft utiliza três métodos para proteger a mídia que está sendo transportada fora do datacenter: 1) Transporte seguro, 2) Criptografia 3) Limpar, remover ou destruir. |


 ## <a name="nist-800-53-control-mp-5b"></a>Controle NIST 800-53 MP-5.b

#### <a name="media-transport"></a>Transporte de dados

**MP-5.b** A organização mantém a responsabilidade da mídia do sistema de informações durante o transporte fora de áreas controladas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure mantém a responsabilidade pelos ativos que deixam o datacenter por meio das diretrizes do NIST SP 800-88: limpeza/remoção consistente, destruição de ativos, criptografia, inventário preciso, controle e proteção de cadeia de custódia durante o transporte. |


 ## <a name="nist-800-53-control-mp-5c"></a>Controle NIST 800-53 MP-5.c

#### <a name="media-transport"></a>Transporte de dados

**MP-5.c** A organização documenta as atividades associadas ao transporte de mídia do sistema de informações.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure mantém registros de inventário antes do transporte, controle e proteção da cadeia de custódia durante o transporte, limpeza/remoção de ativos, destruição de ativos, recebimento de ativos e validação de inventário depois do transporte. |


 ## <a name="nist-800-53-control-mp-5d"></a>Controle NIST 800-53 MP-5.d

#### <a name="media-transport"></a>Transporte de dados

**MP-5.d** A organização restringe as atividades associadas ao transporte de mídia do sistema de informações ao pessoal autorizado.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure restringe as atividades de transporte de ativos ao pessoal autorizado por meio da proteção da cadeia de custódia. O uso de cadeados, lacres à prova de violação e exigência de validação dos inventários de ativos garante que somente pessoas autorizadas estejam envolvidas no transporte dos ativos. |


 ### <a name="nist-800-53-control-mp-5-4"></a>Controle NIST 800-53 MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Transporte de mídia | Proteção criptográfica

**MP-5 (4)** O sistema de informações implementa mecanismos de criptografia para proteger a confidencialidade e a integridade das informações armazenadas em mídia digital durante o transporte fora de áreas controladas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure utiliza um Serviço de Proteção de Dados (DPS) para gerenciar chaves de criptografia usando um módulo de criptografia FIPS 140-2 com validação de Nível 3 (cert. nº 1694) e HSM (cert. nº 1178) para proteger os dados nas fitas magnéticas criptografadas AES de 256 bits. |


 ## <a name="nist-800-53-control-mp-6a"></a>Controle NIST 800-53 MP-6.a

#### <a name="media-sanitization"></a>Limpeza de mídia

**MP-6.a** A organização limpa [Atribuição: mídia do sistema de informações definida pela organização] antes do descarte, liberação para além do controle organizacional ou liberação para reutilização usando [Atribuição: técnicas e procedimentos de limpeza definidos pela organização] de acordo com normas e políticas federais e organizacionais pertinentes.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure exige que as mídias digitais no ambiente de datacenter do Microsoft Azure sejam limpas/removidas usando ferramentas aprovadas do Microsoft Azure e de maneira consistente com NIST SP 800-88, Diretrizes para limpeza de mídia, antes de serem reutilizadas ou descartadas. As mídias não digitais não são usadas pelo Microsoft Azure no ambiente do datacenter. |


 ## <a name="nist-800-53-control-mp-6b"></a>Controle NIST 800-53 MP-6.b

#### <a name="media-sanitization"></a>Limpeza de mídia

**MP-6.b** A organização emprega mecanismos de limpeza com a força e a integridade proporcionais à categoria de segurança ou à classificação das informações.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure usa unidades de eliminação de dados e processos para limpar/remover dados de maneira consistente com NIST SP 800-88 e que são proporcionais à classificação do ativo do Microsoft Azure. Para ativos que precisam ser destruídos, o Microsoft Azure utiliza os serviços locais de destruição de ativos. |


 ### <a name="nist-800-53-control-mp-6-1"></a>Controle NIST 800-53 MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Limpeza de mídia | Analisar / Aprovar / Acompanhar / Documentar / Verificar

**MP-6 (1)** A organização analisa, aprova, acompanha, documenta e verifica as ações de limpeza e eliminação de mídia.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure implementou procedimentos de limpeza de mídia em conformidade com a diretriz NIST SP 800-88 para o Padrão de classificação de ativos e o Padrão de proteção de ativos. Todas as mídias magnéticas ou eletrônicas são limpas/removidas seguindo a especificação NIST SP 800-88 de acordo com a classificação de ativos do Azure. O Azure utiliza unidades de eliminação de dados da Extreme Protocol Solutions (EPS). O software da EPS é compatível com os requisitos de NIST SP 800-88 para a limpeza e remoção/eliminação segura. |


 ### <a name="nist-800-53-control-mp-6-2"></a>Controle NIST 800-53 MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Limpeza de mídia | Teste de equipamentos

**MP-6 (2)** A organização testa procedimentos e equipamentos de limpeza [Atribuição: frequência definida pela organização] para verificar que a limpeza pretendida está sendo obtida.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure usa unidades de eliminação de dados e processos para limpar/remover dados de maneira consistente com NIST SP 800-88. A cada 180 dias, as operações de DCS testam as unidades de eliminação de dados do Microsoft Azure e o processo de eliminação. No teste, as operações de DCS verificam se a limpeza pretendida está sendo obtida por meio de uma análise forense de discos rígidos testados para confirmar que os dados foram limpos pelas unidades de eliminação de dados |


 ### <a name="nist-800-53-control-mp-6-3"></a>Controle NIST 800-53 MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Limpeza de mídia | Técnicas não destrutivas

**MP-6 (3)** A organização aplica técnicas de limpeza não destrutiva a dispositivos portáteis de armazenamento antes de conectar esses dispositivos ao sistema de informações nas seguintes circunstâncias: [Atribuição: circunstâncias definidas pela organização que exigem a limpeza de dispositivos portáteis de armazenamento].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure garante que os datacenters do Azure sigam as ferramentas e o procedimento de segurança de mídia removível no runbook de serviços de data center para impedir a infecção do ambiente governamental por malware em dispositivos portáteis de armazenamento. O procedimento especifica que as seguintes ações sejam realizadas com unidades USB antes do uso no ambiente governamental: <br /> (1) Formate as unidades USB após a compra do fabricante ou fornecedor, antes de começar a usar ou ao reutilizar para uma ferramenta diferente. <br /> (2) Verifique qualquer unidade USB a ser usada em uma área designada pelo governo em busca de malwares, antes de levar a unidade para a área. <br /> (3) Depois de usar uma unidade dentro de uma área designada pelo governo, formate-a antes de deixar a área. <br /> As ferramentas e o procedimento de segurança de mídia removível também exigem que nenhuma unidade USB perdida, descartada, roubada ou extraviada seja reintroduzida em datacenters do Azure, mas que, em vez disso, sejam catalogadas e destruídas. |


 ## <a name="nist-800-53-control-mp-7"></a>Controle NIST 800-53 MP-7

#### <a name="media-use"></a>Uso de mídia

**MP-7** A organização [Seleção: restringe; proíbe] o uso de [Atribuição: tipos de mídia do sistema de informações definidos pela organização] em [Atribuição: sistema ou componentes do sistema de informações definidos pela organização] usando [Atribuição: garantias de segurança definidos pela organização].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure exige que os proprietários dos ativos atribuam seus ativos a uma classificação de ativos e nenhum ativo está isento dessa exigência. No ambiente de datacenter do Microsoft Azure, os ativos referem-se a servidores e dispositivos de rede. Outras mídias digitais, como unidades USB, são gerenciadas por políticas específicas e procedimentos que controlam como esses dispositivos são gerenciados. CD/DVDs não são usados. Mídias não digitais não são usadas no datacenter. O uso de mídia digital em ambientes de datacenter do Microsoft Azure é monitorado 24h por dia, sete dias por semana, por meio de cobertura de CFTV. Consulte PE-06 para obter mais detalhes. |


 ### <a name="nist-800-53-control-mp-7-1"></a>Controle NIST 800-53 MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Uso de mídia | Proibir o uso sem proprietário

**MP-7 (1)** A organização proíbe o uso de dispositivos portáteis de armazenamento em sistemas de informações organizacionais quando esses dispositivos não tem nenhum proprietário identificável.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhuma mídia controlada pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | A Microsoft restringe o uso de mídia removível e gravável em mídias que tenham sido explicitamente aprovadas pelo gerenciamento de datacenter pelas ferramentas de DCS e pelo procedimento de mídias removíveis. As mídias de propriedade pessoal ou que não tenham nenhum proprietário identificável são proibidas em qualquer área de produção, conforme observado no documento de regras e regulamentos de trabalho de datacenters da Microsoft. |
