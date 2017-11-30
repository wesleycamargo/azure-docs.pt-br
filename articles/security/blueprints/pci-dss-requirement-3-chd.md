---
title: Diagrama de processamento de pagamento do Azure - Requisitos de CHD
description: Requisito 3 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Requisitos de CHD para ambientes em conformidade com o PCI DSS
## <a name="pci-dss-requirement-3"></a>Requisito 3 de PCI DSS

**Proteger os dados do titular do cartão armazenados**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Métodos de proteção, como criptografia, truncamento, mascaramento e hash, são essenciais na proteção dos dados do titular do cartão. Se um invasor contorna outros controles de segurança e obtém acesso aos dados criptografados, sem as chaves de criptografia adequadas, os dados não podem ser lidos ou usados por ele. Outros métodos eficazes de proteger os dados armazenados também devem ser considerados como possíveis oportunidades de mitigação de risco. Por exemplo, os métodos para minimizar os riscos incluem não armazenar os dados do titular do cartão a menos que seja absolutamente necessário; truncar os dados do titular do cartão se não for necessário um PAN completo e não enviar PANs desprotegidos usando tecnologias de mensagem de usuário final, como email e sistema de mensagens instantâneas.
Consulte o Glossário de termos, as Abreviações e os Acrônimos de PCI DSS e PA-DSS para obter definições de "criptografia forte" e outros termos de PCI DSS.

## <a name="pci-dss-requirement-31"></a>Requisito 3.1 de PCI DSS

**3.1** Manter o mínimo de dados do titular do cartão armazenados implementando políticas de retenção e eliminação de dados, procedimentos e processos que incluem pelo menos o seguinte para todo o armazenamento de CHD (dados de titular de cartão):
- Limitar a quantidade e o tempo de retenção dos dados armazenamento necessários para fins legais, regulatórios e comerciais
- Especificar requisitos de retenção para os dados do titular do cartão
- Processos para exclusão segura de dados quando não forem mais necessários
- Um processo trimestral para identificar e excluir com segurança os dados armazenados que ultrapassarem o tempo de retenção definido.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Azure é responsável por garantir que os dados do cliente designados para exclusão sejam encerrados com segurança usando protocolos compatíveis com NIST 800-88 especificados nas suas políticas de Descarte Seguro. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não exclui ou destrói CHDs armazenados. No entanto, todos os dados são criptografados e nenhum dado de PAN (número de conta primária) é armazenado.<br /><br />|



## <a name="pci-dss-requirement-32"></a>Requisito 3.2 de PCI DSS

**3.2** Não armazenar dados confidenciais de autenticação após autorização (mesmo se criptografado). Se dados confidenciais de autenticação forem recebidos, considere todos os dados irrecuperáveis após a conclusão do processo de autorização. 
- Há uma justificativa comercial e 
- Os dados são armazenados com segurança.
Os dados confidenciais de autenticação incluem os dados mencionados nos seguintes Requisitos 3.2.1 a 3.2.3:


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não excluir ou destrói CHDs armazenados; os dados de exemplo são armazenados apenas a fins de demonstração. No entanto, todos os dados são criptografados e nenhum dado de PAN (número de conta primária) é armazenado.<br /><br />|



### <a name="pci-dss-requirement-321"></a>Requisito 3.2.1 de PCI DSS

**3.2.1** Não armazenar o conteúdo de uma faixa (da fita magnética localizada atrás de um cartão, dados equivalentes contidos em um chip ou em outro lugar) completo após a autorização. Esses dados também são chamados de faixa completa, faixa, faixa 1, faixa 2 e dados de fita magnética. 

> [!NOTE]
> No curso normal dos negócios, os seguintes elementos de dados da fita magnética podem precisar ser mantidos: 
> - Nome do titular do cartão 
> - PAN (Número de conta principal) 
> - Data de validade 
> - Código de serviço 
>
> Para minimizar o risco, somente armazene esses elementos de dados conforme a necessidade dos negócios.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não armazena o conteúdo completo de nenhum CHD.|



### <a name="pci-dss-requirement-322"></a>Requisito 3.2.2 de PCI DSS

**3.2.2** Não armazenar o valor ou código de verificação do cartão (número de três ou quatro dígitos impresso na parte frontal ou traseira de um cartão de pagamento usado para verificar as transações de cartão não presenciais) após a autorização.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore criptografa todos os dados, incluindo os exemplos de CVV.|



### <a name="pci-dss-requirement-323"></a>Requisito 3.2.3 de PCI DSS

**3.2.3** Não armazenar o PIN (número de identificação pessoal) ou o bloco de PIN criptografado após a autorização.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não armazena informações de PIN.|



## <a name="pci-dss-requirement-33"></a>Requisito 3.3 de PCI DSS

**3.3** Mascarar o PAN quando exibido (os primeiros seis e os últimos quatro dígitos são o número máximo de dígitos a serem exibidos), de modo que apenas o pessoal com necessidade comercial legítima possa ver o PAN completo. 

> [!NOTE]
> Esse requisito não substitui requisitos mais rígidos em vigor para exibições dos dados de titulares, por exemplo, requisitos jurídicos ou da bandeira do cartão de pagamento para notas fiscais dos PDVs (pontos de venda).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore mascara o PAN (número da conta primária) usando Transparent Data Encryption, colunas sempre criptografadas e mascaramento de dados dinâmico. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>Requisito 3.4 de PCI DSS

**3.4** Considerar o PAN ilegível em todos os lugares em que ele está armazenado (incluindo mídia digital portátil, mídia de backup e logs), usando uma das seguintes abordagens:
- Hashes unidirecionais baseados em criptografia forte (o hash deve ser de todo o PAN)
- Truncamento (o hash não pode ser usado para substituir o segmento truncado do PAN)
- Indexar tokens e pads (os pads devem ser armazenados com segurança)
- Criptografia forte com procedimentos e processos de gerenciamento de chaves associados. 

> [!NOTE]
> É um esforço relativamente fácil para um indivíduo mal-intencionado reconstruir os dados de PAN originais se ele tem acesso à versão truncada e em hash de um PAN. Quando as versões do mesmo PAN em hash e truncadas estão presentes no ambiente de uma entidade, outros controles devem estar em vigor para fazer com que as versões truncadas e em hash não possam ser correlacionadas e reconstruir o PAN original.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore criptografa todos os dados de cartão de crédito e usa o Azure Key Vault para gerenciar chaves, impedindo a recuperação de CHD.<br /><br />Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>Requisito 3.4.1 de PCI DSS

**3.4.1** Se a criptografia de disco é usada (em vez de criptografia de banco de dados em nível de arquivo ou de coluna), o acesso lógico deve ser gerenciado separadamente e independentemente dos mecanismos de controle de acesso e autenticação do sistema operacional nativo (por exemplo, não usando bancos de dados de conta de usuário local ou credenciais de logon de rede geral). As chaves de descriptografia não devem estar associadas a contas de usuário. 

> [!NOTE]
> Esse requisito se aplica além de todos os requisitos de gerenciamento de chaves e de criptografia de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore criptografa todos os dados armazenados e separa o tráfego para evitar a elevação de privilégios de funções de DevOps.<br /><br />Como o Ambiente do Serviço de Aplicativo é protegido e bloqueado, é preciso haver um mecanismo para permitir as versões ou alterações feitas por DevOps que possam ser necessárias, como a capacidade de monitorar um aplicativo Web usando Kudu.<br /><br />Uma máquina virtual é definida como jumpbox (host bastião) com as seguintes configurações:<br /><br /><ul><li>[Extensão de antimalware](/azure/security/azure-security-antimalware)</li><li>[Extensão de monitoramento do OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Extensão Diagnóstico de VM](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Disco criptografado com BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>O uso do Azure Key Vault atende aos requisitos de HIPAA, PCI DSS e Azure Governamental.|



## <a name="pci-dss-requirement-35"></a>Requisito 3.5 de PCI DSS

**3.5** Documentar e implementar procedimentos para proteger as chaves usadas para proteger os dados de titulares de cartão armazenados contra divulgação e uso indevido. 

> [!NOTE]
> Esse requisito se aplica às chaves usadas para criptografar os dados de titulares de cartão armazenados e também se aplica às chaves de criptografia de chaves usadas para proteger as chaves de criptografia de dados; essas chaves de criptografia de chave devem ser pelo menos tão fortes quanto a chave de criptografia de dados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />O Microsoft Azure faz com que os cofres de chaves do cliente fiquem logicamente isolados uns dos outros e logicamente isolados do plano de gerenciamento do serviço Key Vault. O Key Vault foi projetado para que a Microsoft não tenha nenhum acesso permanente ao cofre de chaves do cliente. <br /><br />As chaves são protegidas pelo Microsoft Azure, usando HSMs (módulos de segurança de hardware), comprimentos da chave e algoritmos padrão da indústria.<br /><br />Uma chave armazenada no Microsoft Azure Key Vault pode ser usada para proteger outra chave. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece documentação para explicar e ajudar a implantar uma solução de chave protegida para proteger o CHD de demonstração.|



### <a name="pci-dss-requirement-351"></a>Requisito 3.5.1 de PCI DSS

**3.5.1** *Requisitos adicionais somente para provedores de serviço:* manter uma descrição documentada da arquitetura de criptografia que inclui:
- Detalhes de todos os algoritmos, protocolos e chaves usadas para a proteção dos dados do titular do cartão, incluindo a data de validade e o nível da chave
- Descrição do uso da chave para cada chave
- Inventário de HSMs e outros SCDs usados para gerenciamento de chaves 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />O Microsoft Azure faz com que os cofres de chaves do cliente fiquem logicamente isolados uns dos outros e logicamente isolados do plano de gerenciamento do serviço Key Vault. O Key Vault foi projetado para que a Microsoft não tenha nenhum acesso permanente ao cofre de chaves do cliente. <br /><br />As chaves são protegidas pelo Microsoft Azure, usando HSMs (módulos de segurança de hardware), comprimentos da chave e algoritmos padrão da indústria.<br /><br />Uma chave armazenada no Microsoft Azure Key Vault pode ser usada para proteger outra chave. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece documentação para explicar e ajudar a implantar uma solução de chave protegida para proteger o CHD de demonstração.|



### <a name="pci-dss-requirement-352"></a>Requisito 3.5.2 de PCI DSS

**3.5.2** Restringir o acesso a chaves de criptografia ao menor número de responsáveis possível.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />O Key Vault dá suporte a políticas de acesso granular, que permitem a um proprietário do Key Vault conceder acesso a funções específicas para a execução de operações específicas em entidades específicas. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | O gerenciamento de chaves da Contoso Webstore é isolado em uma conta de usuário (administrador##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Requisito 3.5.3 de PCI DSS

**3.5.3** Armazenar chaves privadas e segredos usados para sempre criptografar/descriptografar os dados do titular do cartão em um (ou mais) dos seguintes formulários:
- Criptografado com uma chave de criptografia de chave que seja pelo menos tão forte quanto a chave de criptografia de dados, e que esteja armazenado separadamente da chave de criptografia de dados
- Dentro de um dispositivo de criptografia seguro (por exemplo, um HSM (módulo de segurança de hardware) (host) ou o dispositivo de ponto de interação aprovado por PTS)
- Como pelo menos dois componentes principais completos ou compartilhamentos de chave, de acordo com um método aceito pelo setor 

> [!NOTE]
> Não é necessário que as chaves públicas sejam armazenadas em uma desses formulários.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />As chaves são armazenadas nos cofres de chaves específicos identificados pelo cliente.<br /><br />O Key Vault pode ser acessado globalmente e simultaneamente por vários aplicativos, o que reduz a necessidade de copiar uma chave e armazená-la em vários locais. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>Requisito 3.5.4 de PCI DSS

**3.5.4** Armazenar chaves de criptografia no menor número de locais possível.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />As chaves são armazenadas nos cofres de chaves específicos identificados pelo cliente. <br /><br />O Key Vault pode ser acessado globalmente e simultaneamente por vários aplicativos, o que reduz a necessidade de copiar uma chave e armazená-la em vários locais. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>Requisito 3.6 de PCI DSS

**3.6** totalmente documentar e implementar todos os processos de gerenciamento de chaves e procedimentos para as chaves de criptografia usadas para criptografia de dados do titular do cartão, incluindo o seguinte. 

> [!NOTE]
> Vários padrões do setor para gerenciamento de chaves estão disponíveis em vários recursos, incluindo o NIST, que pode ser encontrado em http://csrc.nist.gov.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>Requisito 3.6.1 de PCI DSS

**3.6.1** Geração de chaves de criptografia fortes

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:** <br /><br />Ao gerar chaves no Key Vault, o Azure será responsável por gerar chaves de acordo com as especificações do cliente. As chaves são geradas usando um HSM. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>Requisito 3.6.2 de PCI DSS

**3.6.2** Proteger a distribuição de chaves de criptografia

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />A ferramenta BYOK (traga sua própria chave) encapsula a chave de cliente e tem como alvo um cofre de segurança específico que está associado a uma assinatura do Azure específica. A chave só pode ser importada para o cofre de chaves da assinatura definida, na região especificada. Esse processo usa os procedimentos de criptografia fornecidos pelo fabricante do hardware. Os clientes recebem uma notificação de que a transferência foi bem-sucedida. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>Requisito 3.6.3 de PCI DSS

**3.6.3** Proteger o armazenamento de chaves de criptografia

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />As chaves são armazenadas em HSMs e protegidas por meio de segurança criptográfica do fabricante do hardware. Os metadados sobre as chaves são armazenados no Armazenamento do Azure em estado criptografado, que é exclusivo para cada cofre de chaves. <br /><br /> |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>Requisito 3.6.4 de PCI DSS

**3.6.4** Alterações de chave de criptografia de chaves que atingiram o final do seu período de criptografia (por exemplo, depois de um período de tempo definido e/ou depois que determinada quantidade de texto criptografado foi produzida por determinada chave), conforme definido pelo fornecedor ou proprietário da chave associado e com base em melhores práticas e diretrizes (por exemplo, Publicação Especial NIST 800-57).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />O Key Vault dá suporte à funcionalidade de atualizar ou reverter chaves, que é definida pelo cliente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>Requisito 3.6.5 de PCI DSS

**3.6.5** Desativação ou Substituição (por exemplo, arquivamento, destruição e/ou revogação) das chaves conforme a necessidade quando a integridade da chave for reduzida (por exemplo, a saída de um funcionário com conhecimento de um componente de chave de texto não criptografado) ou em caso de suspeita de comprometimento das chaves. 

> [!NOTE]
> Se as chaves de criptografia aposentadas ou substituídas precisarem ser guardadas, essas chaves deverão ser arquivadas com segurança (por exemplo, usando uma chave de criptografia de chaves). As chaves de criptografia arquivadas só devem ser usadas para fins de verificação/descriptografia.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />O Key Vault dá suporte à funcionalidade de aposentar ou sustituir, que é definida pelo cliente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>Requisito 3.6.6 de PCI DSS

**3.6.6** Se houver o uso de operações de gerenciamento de chaves de criptografia de texto não criptografado, essas operações deverão ser gerenciadas usando conhecimento dividido e controle duplo. 

> [!NOTE]
> Exemplos de operações de gerenciamento de chaves manuais incluem, dentre outras: geração, transmissão, carregamento, armazenamento e destruição de chaves.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>Requisito 3.6.7 de PCI DSS

**3.6.7** Prevenção de substituição não autorizada das chaves de criptografia.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | **Para clientes usando o Key Vault:**<br /><br />Os cofres de chaves são separados logicamente e não dão suporte à autorização entre diretórios. Com isso, evita-se a substituição não autorizada. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>Requisito 3.6.8 de PCI DSS

**3.6.8** Requisito para que os responsáveis pelas chaves de criptografia confirmem formalmente que compreendem e aceitam suas responsabilidades de custódia de chave.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | O gerenciamento de chaves da Contoso Webstore é isolado em uma conta de usuário (administrador##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Requisito 3.7 de PCI DSS

**3.7** Garantir que políticas de segurança e procedimentos operacionais para proteger dados do titular do cartão armazenados estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Key Vault em todo o gerenciamento de chaves. Para saber mais, confira [Diretriz de PCI - Criptografia](payment-processing-blueprint.md#encryption-and-secrets-management).|




