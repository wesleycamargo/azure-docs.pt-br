---
title: "Identidade de Serviço Gerenciado (MSI) para o Azure Active Directory"
description: "Uma visão geral da Identidade de Serviço Gerenciado para recursos do Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identidade de Serviço Gerenciado (MSI) para recursos do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais que precisam estar em seu código para autenticar para serviços de nuvem. Manter essas credenciais seguras é uma tarefa importante. Idealmente, eles nunca aparecem em estações de trabalho do desenvolvedor ou são verificadas no controle do código-fonte. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. A Identidade de Serviço Gerenciado (MSI) torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

## <a name="how-does-it-work"></a>Como ele funciona?

Ao usar uma Identidade de Serviço Gerenciado em uma instância de serviço do Azure, o Azure cria uma identidade no locatário do Azure Active Directory usado por sua assinatura do Azure. Além disso, o Azure provisiona as credenciais de identidade para a instância do serviço. Como resultado, seu código pode fazer uma solicitação local para obter tokens de acesso para os serviços que oferecem suporte à autenticação do Azure Active Directory. Enquanto isso, o Azure é responsável por reverter as credenciais usadas pela instância de serviço.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Como fazer para habilitar meus recursos para usar uma Identidade de Serviço Gerenciado?

Há dois tipos de Identidades de Serviço Gerenciado: *Atribuída pelo sistema* e *Atribuída pelo usuário*.

- Uma MSI **atribuída pelo sistema** é habilitada diretamente em uma instância de serviço do Azure. Durante o processo de habilitação, o Azure cria uma identidade para a instância de serviço no locatário do Azure Active Directory e provisiona as credenciais de identidade para a instância do serviço. O ciclo de vida de uma MSI atribuída pelo sistema está diretamente relacionado à instância de serviço do Azure na qual está habilitado. Se a instância de serviço é excluída, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.

- Uma MSI **Atribuída pelo usuário** *(visualização particular)* é criada como recurso autônomo do Azure. Durante o processo de criação, o Azure cria uma identidade no locatário do Azure Active Directory. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure. Como a MSI atribuída pelo usuário pode ser usada por várias instâncias de serviço do Azure, seu ciclo de vida é gerenciado separadamente.

Aqui está um exemplo de como a MSI atribuída pelo sistema funciona com as Máquinas Virtuais do Azure.

![Exemplo de MSI de Máquina Virtual](~/articles/active-directory/media/msi-vm-example.png)

1. O Azure Resource Manager recebe uma mensagem para habilitar a MSI atribuída pelo sistema em uma máquina virtual.
2. O Azure Resource Manager cria uma Entidade de Serviço no Azure AD para representar a identidade da máquina virtual. A Entidade de Serviço é criada no locatário do Azure AD que é confiável para essa assinatura.
3. O Azure Resource Manager configura os detalhes da Entidade de Serviço na extensão de VM do MSI da VM. Essa etapa inclui definir a ID do cliente e o certificado usado pela extensão para obter tokens de acesso do Azure AD.
4. Agora que a identidade da Entidade de Serviço da VM é conhecida, ela pode ser concedida acesso aos recursos do Azure. Por exemplo, se seu código precisa chamar o Azure Resource Manager, em seguida, você atribuiria à Entidade de Serviço da VM a função apropriada usando o controle de acesso baseado em função (RBAC) no Azure AD.  Se seu código precisar chamar o Key Vault, então você concederia ao seu código o acesso ao segredo ou chave específica no Key Vault.
5. O código em execução na máquina virtual solicita um token de um ponto de extremidade local que é hospedado pela extensão de VM MSI: http://localhost:50342/oauth2/token. O parâmetro de recurso especifica o serviço ao qual o token é enviado. Por exemplo, se você quiser que seu código autentique para o Azure Resource Manager, você usaria resource=https://management.azure.com/.
6. A extensão de VM do MSI usa sua ID de cliente configurado e o certificado para solicitar um token de acesso do Azure AD.  O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

Usando o mesmo diagrama, aqui está um exemplo de como a MSI atribuída pelo usuário funciona com as Máquinas Virtuais do Azure.

![Exemplo de MSI de Máquina Virtual](~/articles/active-directory/media/msi-vm-example.png)

1. O Azure Resource Manager recebe uma mensagem para criar uma MSI atribuída pelo usuário.
2. O Azure Resource Manager cria uma Entidade de Serviço no Azure Active Directory para representar a identidade da MSI. A Entidade de Serviço é criada no locatário do Azure AD que é confiável para essa assinatura.
3. O Azure Resource Manager recebe uma mensagem para configurar os detalhes da Entidade de Serviço na extensão de VM do MSI de uma VM. Essa etapa inclui definir a ID do cliente e o certificado usado pela extensão para obter tokens de acesso do Azure AD.
4. Agora que a identidade da Entidade de Serviço da MSI é conhecida, ela pode ser concedida acesso aos recursos do Azure. Por exemplo, se seu código precisa chamar o Azure Resource Manager, em seguida, você atribuiria à Entidade de Serviço da MSI a função apropriada usando o controle de acesso baseado em função (RBAC) no Azure Active Directory. Se seu código precisar chamar o Key Vault, então você concederia ao seu código o acesso ao segredo ou chave específica no Key Vault. Observação: a etapa 3 não é necessária para concluir a etapa 4. Quando existe uma MSI, ela pode receber acesso aos recursos, independentemente de ser configurada em uma máquina virtual ou não.
5. O código em execução na máquina virtual solicita um token de um ponto de extremidade local que é hospedado pela extensão de VM MSI: http://localhost:50342/oauth2/token. O parâmetro de ID do cliente especifica o nome da identidade de MSI que será usado. Além disos, o parâmetro de recurso especifica o serviço ao qual o token é enviado. Por exemplo, se você quiser que seu código autentique para o Azure Resource Manager, você usaria resource=https://management.azure.com/.
6. A extensão de VM da MSI verifica se o certificado referente à ID do cliente solicitada está configurado e solicita um token de acesso ao Azure Active Directory. O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

Cada serviço do Azure que dá suporte a Identidades de Serviço Gerenciado tem seu próprio método para seu código obter um token de acesso. Confira os tutoriais para cada serviço para descobrir o método específico para obter um token.

## <a name="try-managed-service-identity"></a>Experimente a Identidade de Serviço Gerenciado

Tente um tutorial de identidade de serviço gerenciado para saber os cenários de ponta a ponta para acessar recursos do Azure diferentes:
<br><br>
| De recurso de permissão MSI | Saiba como |
| ------- | -------- |
| VM do Azure (Linux)   | [Acessar o Azure Resource Manager com uma Identidade de Serviço Gerenciado de VM do Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Acessar o Armazenamento do Azure por meio de uma chave de acesso com uma Identidade do Serviço Gerenciado de VM do Linux](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Os serviços do Azure oferecem suporte à Identidade de Serviço Gerenciado?

Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado podem usar o MSI para autenticar para serviços com suporte à autenticação do Azure AD.  Estamos no processo de integração da autenticação do MSI e do Azure AD no Azure.  Verifique regularmente as atualizações.

### <a name="azure-services-that-support-managed-service-identity"></a>Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado

Os seguintes serviços do Azure oferecem suporte à Identidade de Serviço Gerenciado.

| Serviço | Status | Data | Configurar | Obter um token |
| ------- | ------ | ---- | --------- | ----------- |
| Máquinas Virtuais do Azure | Visualização | Setembro de 2017 | [CLI do Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modelos do Gerenciador de Recursos do Azure](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os seguintes serviços dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam a Identidade de Serviço Gerenciado.

| Serviço | ID de Recurso | Status | Data | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Gerenciador de Recursos do Azure | https://management.azure.com/ | Disponível | Setembro de 2017 | [CLI do Azure](msi-howto-assign-access-CLI.md) |
| Cofre da Chave do Azure | https://vault.azure.net/ | Disponível | Setembro de 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponível | Setembro de 2017 | |
| SQL do Azure | https://database.windows.net/ | Disponível | Outubro de 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Quanto custa a Identidade de Serviço Gerenciado?

A Identidade de Serviço Gerenciado vem com o Azure Active Directory Gratuito, que é o padrão para as assinaturas do Azure.  Não há nenhum custo adicional para a Identidade de Serviço Gerenciado.

## <a name="support-and-feedback"></a>Suporte e comentários

Adoraríamos ouvir o que você tem para nos dizer!

* Faça perguntas sobre instruções no Stack Overflow com a marca [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Faça solicitações de recursos ou deixe comentários no [Fórum de comentários do Azure AD para desenvolvedores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






