---
title: Acesso e autenticação sem entrar – Aplicativo Lógico do Azure | Microsoft Docs
description: Criar uma identidade gerenciada para que o aplicativo lógico possa se autenticar e acessar recursos em outros locatários do Azure AD (Azure Active Directory) sem suas credenciais
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973959"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Acessar recursos e autenticar-se como identidades gerenciadas nos Aplicativos Lógicos do Azure

Para acessar recursos em outros locatários do Azure AD (Azure Active Directory) e autenticar sua identidade sem precisar entrar, você pode criar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) a ser usada pelo aplicativo lógico em vez das credenciais. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou alternar segredos. Este artigo mostra como criar e usar uma identidade gerenciada para seu aplicativo lógico. Para obter mais informações, confira [Gerenciar identidades para recursos do Azure](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço antes conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* O aplicativo lógico em que você deseja usar a identidade gerenciada. Se você não tiver um aplicativo lógico, confira [Criar seu primeiro fluxo de trabalho de aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Criar identidade gerenciada

Você pode criar ou habilitar uma identidade gerenciada para seu aplicativo lógico por meio do portal do Azure, dos modelos do Azure Resource Manager ou do Azure PowerShell. 

### <a name="azure-portal"></a>Portal do Azure

Para criar uma identidade gerenciada para o aplicativo lógico no portal do Azure, ative a configuração **Registrar no Azure Active Directory** nas configurações de fluxo de trabalho do aplicativo lógico.

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. Siga estas etapas: 

   1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**. 

   1. Em **Identidade de serviço gerenciada** > 
   **Registrar no Azure Active Directory**, escolha **Habilitado**.

   1. Quando terminar, escolha **Salvar** na barra de ferramentas.

      ![Habilitar a configuração de identidade gerenciada](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      O Azure agora mostra essas propriedades e valores para a identidade gerenciada do aplicativo lógico:

      ![GUIDS da ID da entidade de segurança e da ID do locatário](./media/create-managed-service-identity/principal-tenant-id.png)

      | Propriedade | Valor | DESCRIÇÃO | 
      |----------|-------|-------------| 
      | **ID da entidade de segurança** | <*principal-ID-GUID*> | Um GUID (identificador global exclusivo) que representa o aplicativo lógico em um locatário do Azure AD | 
      | **ID do locatário** | <*Azure-AD-tenant--ID-GUID*> | Um GUID (identificador global exclusivo) que representa o locatário do Azure AD do qual o aplicativo lógico agora é membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. | 
      ||| 

### <a name="deployment-template"></a>Modelo de implantação

Para automatizar a criação e a implantação de recursos do Azure, como os aplicativos lógicos, você pode configurar modelos do Azure Resource Manager. Para obter mais informações, confira [Criar e implantar aplicativos lógicos com modelos do Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

Para criar uma identidade gerenciada para seu aplicativo lógico por meio de um modelo, adicione o elemento **identity** e a propriedade **type** à definição de fluxo de trabalho do aplicativo lógico no modelo de implantação. Essas configurações indicam que o Azure cria e gerencia essa identidade para o aplicativo lógico:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Por exemplo, o aplicativo lógico pode parecer com esta versão:

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
          "actions": {}, 
          "parameters": {}, 
          "triggers": {}, 
          "contentVersion": "1.0.0.0", 
          "outputs": {} 
     }, 
     "parameters": {}, 
     "dependsOn": [] 
}
```

Quando o Azure cria o aplicativo lógico, a definição de fluxo de trabalho do aplicativo lógico inclui estas propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Propriedade | Valor | DESCRIÇÃO | 
|----------|-------|-------------|
| **principalId** | <*principal-ID-GUID*> | Um GUID (identificador global exclusivo) que representa o aplicativo lógico no locatário do Azure AD | 
| **tenantId** | <*Azure-AD-tenant--ID-GUID*> | Um GUID (identificador global exclusivo) que representa o locatário do Azure AD do qual o aplicativo lógico agora é membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Acessar recursos com identidade gerenciada

Depois de criar uma identidade gerenciada para o aplicativo lógico, você pode [dar a essa identidade o acesso a outros recursos](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Em seguida, você pode usar essa identidade gerenciada para autenticação, como em qualquer outra [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md). 

Por exemplo, suponha que você já tenha configurado um aplicativo lógico usando uma identidade gerenciada com acesso a outro recurso. Agora você pode adicionar uma ação de HTTP para que o aplicativo lógico possa enviar uma solicitação ou chamada HTTP a esse recurso. 

1. No aplicativo lógico, adicione a ação **HTTP**. 

1. Forneça os detalhes necessários sobre essa ação, como o local do **Método** e do **URI** da solicitação ao recurso que você deseja chamar.

1. Na ação de HTTP, escolha **Mostrar opções avançadas**. 

1. Na lista **Autenticação**, selecione **Identidade de Serviço Gerenciada**, que mostra, em seguida, a propriedade **Audiência** a ser definida:

   ![Selecionar a "Identidade de Serviço Gerenciada"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Remover a identidade gerenciada

Para desabilitar uma identidade gerenciada no aplicativo lógico, siga etapas semelhantes às de criação da identidade por meio do portal do Azure, dos modelos de implantação do Azure Resource Manager ou do Azure PowerShell. 

Quando você exclui o aplicativo lógico, o Azure remove automaticamente a identidade atribuída pelo sistema ao aplicativo lógico do Azure AD.

### <a name="azure-portal"></a>Portal do Azure

1. No Designer de Aplicativo Lógico, abra o aplicativo lógico.

1. Siga estas etapas: 

   1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**. 
   
   1. Em **Identidade de serviço gerenciada**, escolha **Desabilitar** para a propriedade **Registrar no Azure Active Directory**.

   1. Quando terminar, escolha **Salvar** na barra de ferramentas.

      ![Desligar a configuração de identidade gerenciada](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modelo de implantação

Se você tiver criado a identidade gerenciada do aplicativo lógico com um modelo de implantação do Azure Resource Manager, defina a propriedade `"type"` do elemento `"identity"` como `"None"`. Essa ação também exclui a ID da entidade de segurança do Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).
