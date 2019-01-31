---
title: Objetos de entidade de serviço e aplicativo no Azure Active Directory
description: Saiba mais sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: fc66f97b39f69af4144c5dd135eac5ea0a7f9a41
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075288"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos de entidade de serviço e aplicativo no Azure Active Directory

Às vezes, o significado do termo "aplicativo" pode ser interpretado de forma errada quando usado no contexto do Azure AD (Azure Active Directory). Este artigo esclarece os aspectos conceituais e concretos da integração de aplicativos do Azure AD, com uma ilustração de registro e consentimento para um [aplicativo multilocatário](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Visão geral

Um aplicativo que foi integrado ao Azure AD tem implicações que ultrapassam o aspecto de software. "Aplicativo" é frequentemente usado como um termo conceitual, referindo-se não apenas ao software de aplicativo, mas também ao seu registro e função no Azure AD nas "conversas" de autenticação/autorização em tempo de execução.

Por definição, um aplicativo pode funcionar nestas funções:

- Função de [cliente](developer-glossary.md#client-application) (consumindo um recurso)
- Função de [servidor de recurso](developer-glossary.md#resource-server) (expondo APIs aos clientes)
- Nas duas funções, de cliente e de servidor de recurso

Um [fluxo de Concessão de Autorização OAuth 2.0](developer-glossary.md#authorization-grant) define o protocolo de conversa, permitindo que o cliente/recurso acesse/proteja dados de um recurso, respectivamente.

Nas seções a seguir, você verá como o modelo de aplicativo do Azure AD representa um aplicativo no tempo de design e no tempo de execução.

## <a name="application-registration"></a>Registro de aplicativo

Quando você registra um aplicativo do Azure AD no [portal do Azure][AZURE-Portal], dois objetos são criados no locatário do Azure AD:

- Um objeto de aplicativo, e
- Um objeto de entidade de serviço

### <a name="application-object"></a>Objeto de aplicativo

Um aplicativo do Azure AD é definido por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado, sendo conhecido como o locatário "inicial" do aplicativo. A [entidade de Aplicativo][AAD-Graph-App-Entity] do Azure AD Graph define o esquema para as propriedades de um objeto de aplicativo.

### <a name="service-principal-object"></a>Objeto de entidade de serviço

Para acessar os recursos que são protegidos por um locatário do Azure AD, a entidade que requer acesso deve ser representada por uma entidade de segurança. Isso é verdadeiro para usuários (entidade de usuário) e aplicativos (entidade de serviço).

A entidade de segurança define a política de acesso e as permissões para o usuário/aplicativo no locatário do Azure AD. Isso habilita recursos principais como a autenticação do usuário/aplicativo durante a entrada, bem como a autorização durante o acesso aos recursos.

Quando um aplicativo recebe permissão para acessar os recursos em um locatário (após o registro ou o [consentimento](developer-glossary.md#consent)), um objeto de entidade de serviço é criado. A [entidade ServicePrincipal][AAD-Graph-Sp-Entity] do Azure AD Graph define o esquema para as propriedades do objeto de uma entidade de serviço.

### <a name="application-and-service-principal-relationship"></a>Relação do aplicativo e a entidade de serviço

Você pode considerar o aplicativo como a representação *global* de seu aplicativo para uso em todos os locatários, e a entidade de serviço como a representação *local* para uso em um locatário específico.

O objeto de aplicativo serve como o modelo do qual as propriedades comuns e padrão são *derivadas* para uso na criação de objetos de entidade de serviço correspondentes. Portanto, um objeto de aplicativo tem uma relação de um para um com o aplicativo de software e uma relação de um para muitos com seus objetos de entidade de serviço correspondentes.

Uma entidade de serviço deve ser criada em cada locatário no qual o aplicativo é usado, permitindo o estabelecimento de uma identidade para entrada e/ou acesso aos recursos que estão sendo protegidos pelo locatário. Um aplicativo de locatário único tem apenas uma entidade de serviço (em seu locatário inicial), criado e com consentimento para uso durante o registro do aplicativo. Uma API/aplicativo Web multilocatário também tem uma entidade de serviço criada em cada locatário no qual um usuário consentiu com o seu uso. 

> [!NOTE]
> As alterações feitas no objeto de aplicativo também são refletidas apenas no objeto de entidade de serviço do locatário inicial do aplicativo (o locatário em que ele foi registrado). Para aplicativos multilocatário, as alterações no objeto do aplicativo não serão refletidas em objetos de entidade de serviço dos locatários de qualquer consumidor até que o acesso seja removido por meio do [Painel de Acesso do Aplicativo](https://myapps.microsoft.com) e concedido novamente.
>
> Observe também que os aplicativos nativos são registrados como multilocatários, por padrão.

## <a name="example"></a>Exemplo

O diagrama a seguir ilustra o relacionamento entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD nesse exemplo de cenário:

- **Adatum**: o locatário usado pela empresa que desenvolveu o **aplicativo de RH**
- **Contoso**: o locatário utilizado pela empresa Contoso, que é consumidora do **aplicativo de HR**
- **Fabrikam**: o locatário usado pela organização Fabrikam, que também consome o **aplicativo de HR**

![Relação entre um objeto de aplicativo e um objeto de entidade de serviço](./media/app-objects-and-service-principals/application-objects-relationship.png)

Nesse cenário de exemplo:

| Etapa | DESCRIÇÃO |
|------|-------------|
| 1    | É o processo de criação do aplicativo e dos objetos de entidade de serviço no locatário inicial do aplicativo. |
| 2    | Quando os administradores da Contoso e da Fabrikam concluem o consentimento, um objeto de entidade de serviço é criado no locatário do Azure AD da empresa e recebe as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual. |
| 3    | Os locatários do consumidor do aplicativo de RH (Contoso e Fabrikam) tem seu próprio objeto de entidade de serviço. Cada um deles representa o uso de uma instância do aplicativo em tempo de execução, controlado pelas permissões concedidas pelo respectivo administrador. |

## <a name="next-steps"></a>Próximas etapas

- Use o [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) para consultar o aplicativo e os objetos de entidade de serviço.
- Acesse um objeto de aplicativo do aplicativo por meio da API do Graph do Azure AD, do editor de manifesto do aplicativo do [portal do Azure][AZURE-Portal] ou dos [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) conforme representado por sua [Entidade de aplicativo][AAD-Graph-App-Entity] do OData.
- Acesse um objeto de entidade de serviço do aplicativo por meio da API do Graph do Azure AD ou dos [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade ServicePrincipal][AAD-Graph-Sp-Entity] do OData.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
