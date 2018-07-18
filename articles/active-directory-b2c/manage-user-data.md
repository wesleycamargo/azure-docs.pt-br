---
title: Gerenciar dados dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como excluir ou exportar dados de usuário no Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dacff48be3fbf16fc719f5a0395937b1f5acc979
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712531"
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Gerenciar dados de usuário no Azure AD B2C

 Este artigo fornece informações sobre como você pode gerenciar os dados de usuário no Azure Active Directory (AD) B2C usando as operações fornecidas pelo [API do Graph do Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). O gerenciamento de dados de usuário inclui a capacidade de excluir dados ou exportar dados de logs de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Excluir dados de usuário

Os dados de usuário são armazenados no diretório do Azure AD B2C e nos logs de auditoria. Todos os dados de auditoria de usuário serão mantidos por 30 dias para retenção de dados no Azure AD B2C. Se você quiser excluir os dados de usuário nesses 30 dias, você pode usar a operação [Excluir um usuário](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser). É necessária uma operação de exclusão para cada um dos locatários do Azure AD B2C onde os dados podem residir. 

A cada usuário no Azure AD B2C é atribuído uma ID de objeto. A ID de objeto fornece um identificador não ambíguo usado para excluir dados de usuário no Azure AD B2C.  Dependendo da arquitetura, a ID de objeto pode ser um identificador de correlação útil em outros serviços, como finanças, marketing e bancos de dados de gerenciamento de relacionamento com o cliente.  

A maneira mais precisa obter a ID de objeto para um usuário é obtê-la como parte de uma viagem de autenticação com o Azure AD B2C.  Se uma solicitação válida para dados for recebida de um usuário usando outros métodos, um processo offline, como uma pesquisa por um agende de serviço de atendimento, talvez seja necessário localizar o usuário e anotar a ID de objeto associada. 

O exemplo a seguir mostra um fluxo de exclusão de dados possível:

1. O usuário faz logon e seleciona **Excluir meus dados**.
2. O aplicativo oferece uma opção para excluir os dados dentro de uma seção de administração do aplicativo.
3. O aplicativo força uma autenticação do Azure AD B2C. B2C do Azure Active Directory fornece um token com a ID de objeto do usuário de volta para o aplicativo. 
4. O token é recebido pelo aplicativo e a ID do objeto é usada para excluir os dados de usuário por meio de uma chamada à API do Graph do Azure Active Directory. A API do Graph do Azure Active Directory exclui os dados de usuário e retorna um código de status 200 OK.
5. O aplicativo orquestra a exclusão dos dados de usuário em outros sistemas organizacionais conforme necessário usando a ID de objeto ou outros identificadores.
6. O aplicativo confirma a exclusão dos dados e fornece as próximas etapas para o usuário.

## <a name="export-customer-data"></a>Exportar dados do cliente

O processo para exportar os dados do cliente do Azure AD B2C é semelhante ao processo de exclusão.

Os dados de usuário do Azure AD B2C são limitados a:

- **Dados armazenados no Azure Active Directory** - os dados podem ser recuperados em um percurso do usuário de autenticação no Azure AD B2C usando a ID de objeto ou qualquer nome de entrada, como email ou nome de usuário.  
- **Relatório de eventos de auditoria específicas do usuário** - os dados são indexados usando a ID de objeto.

No exemplo a seguir de uma exportação de fluxo de dados, as etapas descritas como sendo executadas pelo aplicativo também podem ser executadas por um processo de back-end ou por um usuário com uma função de administrador no Diretório:

1. O usuário entra no alicativo. O Azure AD B2C impõe a autenticação com autorização multifator se necessário.
2. O aplicativo usa as credenciais do usuário para chamar uma operação de API do Graph do Azure AD para recuperar os atributos de usuário. A API do Graph do Azure AD fornece os dados de atributo no formato JSON. Dependendo do esquema, o conteúdo do token de identificação pode ser definido para incluir todos os dados pessoais de um usuário.
3. O aplicativo recupera a atividade de auditoria do usuário final. A API do Graph do Azure AD fornece os dados de evento para o aplicativo.
4. O aplicativo agrega os dados e os torna disponíveis para o usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba como gerenciar como os usuários podem acessar seu aplicativo em [Gerenciar o acesso do usuário](manage-user-access.md)




