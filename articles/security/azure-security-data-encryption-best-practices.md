---
title: Práticas Recomendadas de Segurança de Dados e Criptografia | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas de segurança de dados e criptografia usando recursos internos do Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: barclayn
ms.openlocfilehash: 686d4a8ac5239af12206b57072cc00aa10114d79
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976504"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Práticas Recomendadas de Segurança de Dados e Criptografia do Azure

Para ajudar a proteger os dados na nuvem, você precisa levar em consideração os possíveis estados em que seus dados podem ocorrer e quais controles estão disponíveis para esse estado. Práticas recomendadas para criptografia e segurança de dados do Azure se relacionam aos seguintes estados de dados:

- Em repouso: Isso inclui todos os objetos de armazenamento, contêineres e tipos de informações que existem estaticamente em mídia física, seja ela magnética ou disco óptico.
- Em trânsito: Quando os dados estão sendo transferidos entre componentes, locais ou programas, estará em trânsito. Os exemplos são a transferência pela rede, através de um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como o ExpressRoute), ou durante um processo de entrada/saída.

Neste artigo, veremos uma coleção de práticas de recomendadas de segurança de dados e criptografia do Azure. Essas práticas recomendadas derivam da nossa experiência com segurança de dados e criptografia do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo Práticas Recomendadas de Segurança de Dados e Criptografia do Azure baseiam-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos, tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="choose-a-key-management-solution"></a>Escolha uma solução de gerenciamento de chaves

Proteger suas chaves é essencial para proteger seus dados na nuvem.

[Azure Key Vault](../key-vault/key-vault-overview.md) ajuda a proteger chaves criptográficas e segredos que aplicativos e serviços de nuvem usam. O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-las para chaves de produção. Administradores de segurança podem conceder (e revogar) permissão a chaves conforme for necessário.

Você pode usar o Key Vault para criar vários contêineres seguros, chamados de cofre. Esses cofres contam com HSMs. Os cofres ajudam a reduzir a possibilidade de perda acidental de informações de segurança pela centralização do armazenamento de segredos do aplicativo. Os Key Vaults também controlam e registram o acesso a todas as coisas armazenadas neles. O Azure Key Vault pode tratar da solicitação e renovação de certificados de Segurança de Camada de Transporte (TLS). Fornece recursos para uma solução robusta para gerenciamento de ciclo de vida do certificado.

O Azure Key Vault foi projetado para dar suporte a segredos e chaves do aplicativo. O Key Vault não deve ser usado como um repositório de senhas de usuário.

A seguir estão as práticas recomendadas de segurança para usar o Azure Key Vault.

**Melhor prática**: Conceder acesso a usuários, grupos e aplicativos em um escopo específico.   
**Detalhe**: Usar funções predefinidas de RBAC. Por exemplo, para conceder acesso a um usuário para gerenciar os cofres de chaves, você atribuiria a função predefinida [Colaborador do Key Vault](../role-based-access-control/built-in-roles.md) a esse usuário em um escopo específico. O escopo seria uma assinatura, um grupo de recursos ou apenas um cofre de chaves específico. Se as funções predefinidas não atendem às suas necessidades, você poderá [definir suas próprias funções](../role-based-access-control/custom-roles.md).

**Melhor prática**: Controlar quais usuários têm acesso.   
**Detalhe**: O acesso a um cofre de chaves é controlado por meio de duas interfaces separadas: plano de gerenciamento e plano de dados. Os controles de acesso do plano de gerenciamento e do plano e dados funcionam de forma independente.

Use o RBAC para controlar quais usuários têm acesso. Por exemplo, se desejar conceder a um aplicativo acesso para usar as chaves em um cofre de chaves, você só precisará conceder permissões de acesso ao plano de dados usando políticas de acesso do cofre de chaves, e nenhum acesso do plano de gerenciamento será necessário para o aplicativo. Por outro lado, se quiser que um usuário possa ler propriedades de cofre e marcas, mas não tenha acesso a chaves, segredos ou certificados, você poderá conceder ao usuário acesso de 'leitura' usando o RBAC, e nenhum acesso a dados será necessário.

**Melhor prática**: Armazenar certificados no cofre de chaves. Seus certificados são de alto valor. Em mãos erradas, a segurança do aplicativo ou a segurança dos seus dados pode ser comprometida.   
**Detalhe**: O Azure Resource Manager pode implantar com segurança os certificados armazenados no Azure Key Vault para VMs do Azure quando as VMs são implantadas. Ao definir políticas de acesso apropriado para o Cofre de chaves, você também controlar quem obtém acesso ao certificado. Outro benefício é que você gerencie todos os certificados em um único lugar no Azure Key Vault. Consulte [Implantar certificados em VMs por meio de um cofre de chaves gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) para obter mais informações.

**Melhor prática**: Certifique-se de que você pode recuperar uma exclusão de cofres de chaves ou objetos de cofre de chaves.   
**Detalhe**: Exclusão da chave de cofres ou objetos de cofre de chaves pode ser inadvertida ou mal-intencionada. Habilitar a exclusão reversível e limpar os recursos de proteção do cofre de chaves, especialmente para chaves que são usadas para criptografar dados em repouso. A exclusão dessas chaves é equivalente à perda de dados, para que você possa recuperar os cofres excluídos e objetos do Key Vault se necessário. Praticar as operações de recuperação de Key Vault regularmente.

> [!NOTE]
> Se um usuário tiver permissões de contribuidor (RBAC) para um plano de gerenciamento de cofre de chaves, poderá conceder a si mesma acesso ao plano de dados, definindo a política de acesso do cofre de chaves, que controla o acesso ao plano de dados. É recomendável controlar exatamente quem tem o acesso de colaborador para seus cofres de chaves para garantir que somente pessoas autorizadas possam acessar e gerenciar cofres de chaves, chaves, segredos e certificados.
>
>

## <a name="manage-with-secure-workstations"></a>Gerenciar com estações de trabalho protegidas

> [!NOTE]
> O proprietário ou administrador da assinatura deve usar uma estação de trabalho para proteger o acesso ou uma estação de trabalho de acesso privilegiado.
>
>

Uma vez que a maioria dos ataques tem o usuário final como alvo, o ponto de extremidade se torna um dos principais pontos de ataque. Um invasor se compromete com o ponto de extremidade, ele poderá aproveitar as credenciais do usuário para obter acesso aos dados da organização. A maioria dos ataques de ponto de extremidade aproveita o fato dos usuários finais serem os administradores em suas estações de trabalho locais.

**Melhor prática**: Usar uma estação de trabalho de gerenciamento seguro para proteger contas confidenciais, tarefas e dados.   
**Detalhe**: Use uma [estação de trabalho de acesso privilegiado](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Essas estações de trabalho de gerenciamento seguras podem ajudar a atenuar alguns desses ataques e a garantir que seus dados ficarão mais seguros.

**Melhor prática**: Garantir proteção do ponto de extremidade.   
**Detalhe**: Impor políticas de segurança em todos os dispositivos que são usados para consumir dados, independentemente do local de dados (nuvem ou local).

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

A [criptografia de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória para garantir a privacidade de dados, conformidade e soberania.

**Melhor prática**: Aplicar a criptografia de disco para ajudar a proteger seus dados.   
**Detalhe**: Use [Azure Disk Encryption](azure-security-disk-encryption.md). Permite que os administradores de TI criptografem discos de VM IaaS Windows e Linux. O Disk Encryption combina o recurso BitLocker do Windows padrão do setor e o recurso dm-crypt do Linux para fornecer uma criptografia de volume para o sistema operacional e os discos de dados.

Dados em repouso de armazenamento de criptografia do Armazenamento do Microsoft Azure e Banco de Dados SQL do Azure por padrão e muitos serviços oferecem criptografia como uma opção. Você pode usar o Azure Key Vault para manter o controle das chaves que acessam e criptografar seus dados. Consulte [suporte ao modelo de criptografia de provedores do recurso do Azure para saber mais](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Práticas recomendadas**: Usar criptografia para ajudar a atenuar os riscos relacionados ao acesso não autorizado.
**Detalhe**: Criptografe as unidades antes de gravar dados confidenciais nelas.

Organizações que não impõem criptografia de dados estão mais expostas a problemas de confidencialidade de dados. Por exemplo, usuários não autorizados ou não autorizados podem roubar dados nas contas comprometidas ou acesso não autorizado a dados codificados em Clear Format. As empresas também devem provar que são diligentes e que usam controles de segurança corretos para melhorar a segurança de seus dados para estar em conformidade com as regulamentações do setor.

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito

A proteção dos dados em trânsito deve ser parte essencial de sua estratégia de proteção de dados. Uma vez que os dados se movem de e para trás em vários locais, geralmente recomendamos que você sempre use protocolos SSL/TLS para trocar dados entre diferentes locais. Em algumas circunstâncias, convém isolar o canal de toda comunicação entre seu local e nuvem infra-estruturas usando uma VPN.

Para dados que se movem entre sua infraestrutura local e o Azure, considere proteções adequadas, como HTTPS ou VPN. Ao enviar tráfego criptografado entre uma rede virtual do Azure e um local na Internet pública, use [Gateway de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/).

Estas são melhores práticas específicas para o uso de HTTPS, SSL/TLS e o Gateway de VPN do Azure.

**Melhor prática**: Acesso seguro de várias estações de trabalho locais para uma rede virtual do Azure.   
**Detalhe**: Use [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Melhor prática**: Acesso seguro de uma estação de trabalho individual localizada no local a uma rede virtual do Azure.   
**Detalhe**: Use [VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Melhor prática**: Mover grandes conjuntos de dados em um link WAN de alta velocidade dedicado.   
**Detalhe**: Use [ExpressRoute](../expressroute/expressroute-introduction.md). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

**Melhor prática**: Interagir com o Armazenamento do Azure por meio do portal do Azure.   
**Detalhe**: Todas as transações ocorrerão via HTTPS. Você também pode usar [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) via HTTPS para interagir com [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/) e [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que não protegem dados em trânsito são mais suscetíveis a [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [espionagem](https://technet.microsoft.com/library/gg195641.aspx)e sequestro de sessão. Esses ataques podem ser a primeira etapa na obtenção de acesso a dados confidenciais.

## <a name="secure-email-documents-and-sensitive-data"></a>Proteger emails, documentos e dados confidenciais

Você deseja controlar e proteger o e-mail, documentos e dados confidenciais que você compartilha fora da sua empresa. [O Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) é uma solução baseada em nuvem que ajuda uma organização a classificar, rotular e proteger seus documentos e e-mails. Isso pode ser feito automaticamente por administradores que definem regras e condições, manualmente pelos usuários ou uma combinação de onde os usuários obtém recomendações.

A classificação fica identificável o tempo todo, independentemente do local em que os dados são armazenados ou com quem eles são compartilhados. Os rótulos incluem marcas visuais como um cabeçalho, rodapé ou marca d'água. Os metadados são adicionados aos arquivos e cabeçalhos de e-mail em texto não criptografado. O texto não criptografado garante que outros serviços, como soluções para evitar a perda de dados, podem identificar a classificação e tomar as devidas providências.

A tecnologia de proteção usa o Microsoft Azure AD Rights Management (Azure RMS). Essa tecnologia está integrada com outros serviços de nuvem da Microsoft e aplicativos, como o Office 365 e Azure Active Directory. Essa tecnologia de proteção usa criptografia, identidade e políticas de autorização. A proteção aplicada por meio do Azure RMS permanece com os documentos e e-mails, independentemente da localização — dentro ou fora de sua organização, redes, servidores de arquivos e aplicativos.

Essa solução de proteção de informações mantém você no controle de seus dados, mesmo quando for compartilhado com outras pessoas. Você também pode usar o Azure RMS com seus próprios aplicativos de linha de negócios e soluções de proteção de informações de fornecedores de software, se esses aplicativos e soluções estiverem no local ou na nuvem.

É recomendável que você:

- [Implante a proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) para sua organização.
- Aplica rótulos que reflitam seus requisitos de negócios. Por exemplo:  Aplicar um rótulo denominado "altamente confidencial" a todos os documentos e e-mails que contêm dados confidenciais, para classificar e proteger esses dados. Em seguida, somente usuários autorizados podem acessar esses dados, com todas as restrições que você especificar.
- Configure o [log de uso para o Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) para que você possa monitorar como a sua organização está usando o serviço de proteção.

As organizações que não priorizam a [classificação de dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e a proteção de arquivos pode estar mais vulnerável à perda de dados. Com a proteção de arquivos apropriada, você pode analisar os fluxos de dados para obter informações sobre sua empresa, detectar comportamentos de risco e tomar medidas corretivas, controlar o acesso a documentos e assim por diante.

## <a name="next-steps"></a>Próximas etapas

Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por email para secure@microsoft.com
