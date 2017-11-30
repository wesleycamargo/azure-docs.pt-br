---
title: "Plano gráfico de processamento de pagamento do Azure - Requisitos de Identidade"
description: Requisito 8 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Requisitos de identidade para ambientes em conformidade com PCI DSS 
## <a name="pci-dss-requirement-8"></a>Requisito 8 de PCI DSS

**Identificar e autenticar o acesso aos componentes do sistema**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

A atribuição de uma identificação exclusiva (ID) para cada pessoa com acesso garante que cada indivíduo seja exclusivamente responsável por suas ações. Quando está em vigor tal responsabilidade, as ações realizadas nos sistemas e dados críticos são executadas por, e podem ser atribuídas a, usuários e processos autorizados e conhecidos.
A eficácia de uma senha é determinada principalmente pelo design e pela implementação do sistema de autenticação — particularmente, com que frequência as tentativas de senhas podem ser feitas por um invasor e os métodos de segurança para proteger as senhas de usuário no ponto de entrada, durante a transmissão e no armazenamento.

> [!NOTE]
> Esses requisitos são aplicáveis a todas as contas, incluindo contas de ponto de venda, com recursos administrativos e todas as contas usadas para exibir ou acessar dados de titulares de cartão ou para acessar sistemas com dados de titular de cartão de crédito. Isso inclui contas usadas pelos fornecedores e terceiros (por exemplo, para manutenção ou suporte). Esses requisitos não se aplicam às contas usadas pelos consumidores (por exemplo, o titulares de cartão de crédito). No entanto, os requisitos 8.1.1, 8.2, 8.5, 8.2.3 a 8.2.5 e 8.1.6 a 8.1.8 não são aplicáveis às contas de usuário dentro de um aplicativo de pagamento de ponto de venda que só têm acesso a um número de cartão por vez para facilitar uma única transação (como contas de caixa).
 
## <a name="pci-dss-requirement-81"></a>Requisito 8.1 de PCI DSS

**8.1** Defina e implemente políticas e procedimentos para garantir o gerenciamento de identificação de usuário apropriado para usuários e administradores não consumidores em todos os componentes do sistema, da seguinte maneira.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um caso de uso e uma descrição para o uso correto de administradores para o exemplo de implantação.|



### <a name="pci-dss-requirement-811"></a>Requisito 8.1.1 de PCI DSS

**8.1.1** Atribua a todos os usuários uma ID exclusiva antes de permitir que eles acessem componentes do sistema ou dados de titular do cartão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore implementa o Active Directory do Azure e o RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory para garantir que todos os usuários tenham uma ID exclusiva. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Requisito 8.1.2 de PCI DSS

**8.1.2** Controle a adição, a exclusão e a modificação de IDs de usuário, credenciais e outros objetos identificadores.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore implementa o Active Directory do Azure e o RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory para garantir que todos os usuários tenham uma ID exclusiva. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Requisito 8.1.3 de PCI DSS

**8.1.3** Revogue imediatamente o acesso para qualquer usuário encerrado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore utiliza o Microsoft Azure Active Directory para o gerenciamento de usuários. A revogação de usuários pode ser feita no Active Directory.|



### <a name="pci-dss-requirement-814"></a>Requisito 8.1.4 de PCI DSS

**8.1.4** Remova ou desabilite contas de usuários inativas dentro de 90 dias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore utiliza o Microsoft Azure Active Directory para o gerenciamento de usuários. A opção `-enableADDomainPasswordPolicy` pode ser definida para garantir que as senhas expirem em 90 dias.|



### <a name="pci-dss-requirement-815"></a>Requisito 8.1.5 de PCI DSS

**8.1.5** Gerencie as IDs usadas por terceiros para acessar, dar suporte ou manter os componentes do sistema por meio do acesso remoto, da seguinte maneira:
- Habilitado somente durante o período de tempo necessário e desabilitado quando não estiver em uso.
- Monitorado quando em uso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure adotou políticas de segurança corporativa e organizacional aplicáveis, inclusive uma Política de Segurança de Informações. As políticas foram aprovadas, publicadas e comunicadas ao Microsoft Azure. A Política de Segurança de Informações exige que o acesso aos ativos do Microsoft Azure seja concedido sob justificativa de negócios, com a autorização do proprietário do ativo e limitado com base nos princípios de "necessidade de conhecimento" e "privilégios mínimos". Além disso, a política também cumpre requisitos para o ciclo de vida de gerenciamento de acesso, inclusive provisionamento de acesso, autenticação, autorização de acesso, remoção de direitos de acesso e revisões periódicas de acesso. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A demonstração da Contoso Webstore implementou o Active Directory do Azure e o RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory para gerenciar o acesso de usuário para a instalação. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Requisito 8.1.6 de PCI DSS

**8.1.6** Limite as repetidas tentativas de acesso bloqueando a ID de usuário depois de não mais de seis tentativas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore implementou a separação clara das responsabilidades (SOD) para todos os usuários da demonstração. Para obter mais informações, consulte ""Azure Active Directory Identity Protection" em [Diretriz de PCI - Gerenciamento de identidades](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>Requisito 8.1.7 de PCI DSS

**8.1.7** Defina a duração do bloqueio como um mínimo de 30 minutos ou até que um administrador habilite a ID de usuário.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis pela criação, imposição e monitoramento de uma política de senha compatível com os requisitos de PCI DSS.|



### <a name="pci-dss-requirement-818"></a>Requisito 8.1.8 de PCI DSS

**8.1.8** Se uma sessão estiver ociosa por mais de 15 minutos, exija que o usuário se autentique novamente para reativar o terminal ou a sessão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis pela criação, imposição e monitoramento de uma política de senha compatível com os requisitos de PCI DSS.|



## <a name="pci-dss-requirement-82"></a>Requisito 8.2 de PCI DSS

**8.2** Além de atribuir uma ID exclusiva, garanta o gerenciamento de autenticação de usuário apropriado para usuários e administradores não consumidores em todos os componentes do sistema empregando pelo menos um dos métodos a seguir para autenticar todos os usuários:
- Algo que você sabe, como uma senha ou frase secreta
- Algo que você tem, como um dispositivo de token ou um cartão inteligente
- Algo que você é, por exemplo, biométrico

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A implementação da Contoso Webstore para a autenticação multifator foi desabilitada para fornecer a facilidade de uso para a demonstração. A autenticação multifator pode ser implementada usando-se a [Autenticação multifator do Azure](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Requisito 8.2.1 de PCI DSS

**8.2.1** Usando a criptografia forte, processe todas as credenciais de autenticação (como senhas/frases) ilegíveis durante a transmissão e o armazenamento em todos os componentes do sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure estabeleceu os principais procedimentos de gerenciamento para gerenciar as chaves de criptografia em todo o ciclo de vida (por exemplo, geração, distribuição, revogação). O Microsoft Azure usa a infraestrutura de PKI corporativa da Microsoft. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore impõe senhas fortes, documentadas no guia de implantação. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Requisito 8.2.2 de PCI DSS

**8.2.2** Verifique a identidade do usuário antes de modificar qualquer credencial de autenticação — por exemplo, executando redefinições de senha, provisionando novos tokens ou gerando novas chaves.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure estabeleceu os principais procedimentos de gerenciamento para gerenciar as chaves de criptografia em todo o ciclo de vida (por exemplo, geração, distribuição, revogação). O Microsoft Azure usa a infraestrutura de PKI corporativa da Microsoft. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore impõe senhas fortes, documentadas no guia de implantação. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>Requisito 8.2.3 de PCI DSS

**8.2.3** Senhas/frases secretas devem atender aos seguintes requisitos:
- Precisam de um comprimento mínimo de sete caracteres.
- Conter caracteres numéricos e alfabéticos.
Como alternativa, as senhas/frases secretas devem ter complexidade e força pelo menos equivalentes aos parâmetros especificados acima.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore impõe senhas fortes, documentadas no guia de implantação.|



### <a name="pci-dss-requirement-824"></a>Requisito 8.2.4 de PCI DSS

**8.2.4** Altere as senhas de usuário/frases secretas pelo menos uma vez a cada 90 dias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore utiliza o Microsoft Azure Active Directory para o gerenciamento de usuários. A opção `-enableADDomainPasswordPolicy` pode ser definida para garantir que as senhas expirem pelo menos uma vez a cada 90 dias.|



### <a name="pci-dss-requirement-825"></a>Requisito 8.2.5 de PCI DSS

**8.2.5** Não permita que um indivíduo envie uma nova senha/frase secreta que seja igual a qualquer uma das últimas quatro senhas/frases secretas que ele tenha usado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore impõe senhas fortes, documentadas no guia de implantação. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Requisito 8.2.6 de PCI DSS

**8.2.6** Defina senhas/frases secretas para o uso pela primeira vez e após a redefinição como um valor exclusivo para cada usuário e altere imediatamente após o primeiro uso.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore impõe senhas fortes, documentadas no guia de implantação. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Requisito 8.3 de PCI DSS

**8.3** Proteja todo o acesso individual administrativo de não console e todo o acesso remoto ao ambiente de dados do titular do cartão (CDE) usando a autenticação multifator.

> [!NOTE]
> A autenticação multifator requer que pelo menos dois dos três métodos de autenticação (veja no Requisito 8.2 as descrições dos métodos de autenticação) sejam usados para a autenticação. Usar um fator duas vezes (por exemplo, usando duas senhas separadas) não é considerado a autenticação multifator.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Exige-se que os administradores do Azure usem a autenticação multifator para o acesso ao executar a manutenção e a administração dos servidores e sistemas do Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). A autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-831"></a>Requisito 8.3.1 de PCI DSS

**8.3.1** Incorpore a autenticação multifator para todos os acessos que não sejam por console ao CDE para os funcionários com acesso administrativo.

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Exige-se que os administradores do Azure usem a autenticação multifator para o acesso ao executar a manutenção e a administração dos servidores e sistemas do Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). A autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-832"></a>Requisito 8.3.2 de PCI DSS

**8.3.2** Incorpore a autenticação multifator em todos os acessos de rede remota (tanto para usuário quanto administrador, e incluindo o acesso de terceiros para manutenção ou suporte) provenientes de fora da rede da entidade.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Exige-se que os administradores do Azure usem a autenticação multifator para o acesso ao executar a manutenção e a administração dos servidores e sistemas do Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). A autenticação multifator não está implementada para a demonstração.|



## <a name="pci-dss-requirement-84"></a>Requisito 8.4 de PCI DSS

**8.4** Documente e comunique as políticas e os procedimentos de autenticação a todos os usuários, incluindo:
- Orientação sobre como selecionar credenciais de autenticação fortes
- Diretrizes sobre como os usuários devem proteger as credenciais de autenticação
- Instruções para a não reutilização das senhas usadas anteriormente
- Instruções para a alteração da senha caso haja qualquer suspeita de comprometimento da senha

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes têm a responsabilidade de seguir as diretrizes e a documentação e de documentar os procedimentos e as políticas de autenticação a todos os usuários.|



## <a name="pci-dss-requirement-85"></a>Requisito 8.5 de PCI DSS

**8.5** Não use IDs e senhas genéricas, compartilhadas ou de grupo, nem outros métodos de autenticação, conforme o seguinte:
- As IDs de usuário genéricas são desabilitadas ou removidas.
- As IDs de usuário compartilhadas não existem para a administração do sistema e outras funções críticas.
- As IDs de usuário compartilhadas e genéricas não são usadas para se administrar qualquer componente do sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). A autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-851"></a>Requisito 8.5.1 de PCI DSS

**8.5.1** **Requisitos adicionais somente para os provedores de serviço:** Os provedores de serviços com acesso remoto às instalações do cliente (por exemplo, para a manutenção de servidores ou sistemas de POS) devem usar uma credencial de autenticação exclusiva (como uma senha/frase) para cada cliente. 

> [!NOTE]
> Esse requisito não deve ser aplicado aos provedores de hospedagem compartilhada, acessando o próprio ambiente de hospedagem deles, onde vários ambientes de cliente estão hospedados.

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável a clientes do Microsoft Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável a clientes do Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Requisito 8.6 de PCI DSS

**8.6** Quando outros mecanismos de autenticação são usados (por exemplo, tokens de segurança físicos ou lógicos, cartões inteligentes, certificados, etc.), o uso desses mecanismos deve ser atribuído da seguinte maneira:
- Os mecanismos de autenticação devem ser atribuídos a uma conta individual e não compartilhados entre várias contas.
- Os controles físicos e/ou lógicos devem estar em vigor para garantir que apenas a conta pretendida possa usar esse mecanismo para obter acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). A autenticação multifator não está implementada para a demonstração. Todo o acesso é gerenciado pelo [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), que ajuda a proteger segredos e chaves criptográficas usados por aplicativos e serviços na nuvem. |



## <a name="pci-dss-requirement-87"></a>Requisito 8.7 de PCI DSS

**8.7** Todo o acesso a qualquer banco de dados que contém dados do titular do cartão (incluindo o acesso por aplicativos, administradores e todos os outros usuários) é restrito da seguinte maneira:
- Todo o acesso do usuário, consultas de usuário e ações do usuário nos bancos de dados são feitos por meio de métodos de programação.
- Somente os administradores de banco de dados têm a capacidade de acessar diretamente ou consultar bancos de dados.
- As IDs de aplicativo para aplicativos de banco de dados só podem ser usadas pelos aplicativos (e não por usuários individuais ou outros processos que não sejam por aplicativo).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore protege todos os dados do titular do cartão com o Azure Key Vault, e a criptografia dos registros é descrita na documentação da implantação. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Requisito 8.8 de PCI DSS

**8.8** Garanta que as políticas de segurança e os procedimentos operacionais para identificação e autenticação estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por garantir que as políticas de segurança e os procedimentos operacionais para identificação e autenticação estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.|




