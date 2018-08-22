---
title: Controle de acesso de Monitoramento Remoto – Azure | Microsoft Docs
description: Este artigo fornece informações sobre como configurar os controles de acesso baseados em função (RBAC) no acelerador da solução de Monitoramento Remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 8886c08e88c1dd46d2b325b9ba8e9e0b8ae104f9
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007009"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Configure os controles de acesso baseados em função no acelerador da solução de Monitoramento Remoto

Este artigo fornece informações sobre como configurar os controles de acesso baseados em função no acelerador da solução de Monitoramento Remoto. Os controles de acesso baseados em função permitem que você restrija o acesso para usuários individuais a recursos específicos da solução.

## <a name="default-settings"></a>Configurações padrão

Quando você implantar a solução de monitoramento remoto, há duas funções: **Admin** e **Somente Leitura**.

Qualquer usuário na função **Admin** tem acesso completo à solução. Um usuário na função **Somente Leitura** não pode fazer qualquer uma das seguintes tarefas:

- Atualizar alarmes
- Excluir alarmes
- Criar dispositivos
- Atualizar dispositivos
- Excluir dispositivos
- Criar grupos de dispositivos
- Atualizar grupos de dispositivos
- Excluir grupos de dispositivos
- Criar regras
- Atualizar regras
- Excluir regras
- Criar trabalhos
- Atualizar gerenciamento de SIM

A pessoa que implanta a solução de monitoramento remoto é atribuída automaticamente para a função **Admin** e é um proprietário de aplicativo do Microsoft Azure Active Directory. Como um proprietário de aplicativo, você pode atribuir funções a outros usuários no portal do Azure.

Se desejar que outro usuário atribua s funções na solução, também devem ser definido como um proprietário de aplicativo no portal do Azure.

## <a name="add-or-remove-users"></a>Adicionar ou remover usuários

Use o portal do Azure para adicionar ou remover um usuário da solução de monitoramento remoto. As seguintes etapas usam o [Aplicativo do Microsoft Azure Active Directory corporativo](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) que foi criado para você quando você implantou a solução de monitoramento remoto.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Verifique o [usuário que está no diretório](../active-directory/fundamentals/add-users-azure-active-directory.md) que você está usando. Você escolheu o diretório a ser usado quando você entrou no site [Aceleradores de Solução de IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators) site. O nome do diretório estará visível no canto superior direito da [página](https://www.azureiotsolutions.com/Accelerators).

1. Localizar o **Aplicativos empresarial** para sua solução no portal do Azure. O nome do aplicativo é o nome da sua solução de Monitoramento Remoto. Na captura de tela a seguir, os nomes de exibição de solução e de aplicativo são **contoso-rm4**.

    ![Aplicativo empresarial](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Verifique se você é proprietário do aplicativo clicando no aplicativo e, em seguida, clicando em **Proprietários**. Na captura de tela a seguir o **admin do Contoso** é um proprietário do aplicativo **contoso-rm4**:

    ![Proprietários](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Se você não for um proprietário, precisará solicitar um proprietário existente para adicioná-lo à lista. Somente os proprietários podem atribuir funções de aplicativo, como **Admin** ou **Somente Leitura** a outros usuários.

1. Para ver a lista de usuários atribuídos a funções no aplicativo, clique em **Usuários e Grupos**.

1. Para adicionar um usuário, clique em **+ Adicionar usuário**e, em seguida, clique em **Usuários e grupos, nenhum selecionado** para selecionar um usuário do diretório.

1. Para atribuir o usuário a uma função, clique em **Selecionar função, Nenhum selecionado** e escolha o **Admin** ou a função **Somente leitura** do usuário. Clique em **Selecionar** e clique em **Atribuir**.

    ![Escolher função](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. O usuário agora pode acessar a solução de monitoramento remoto com as permissões definidas pela função.

1. Você pode excluir os usuários do aplicativo na página **Usuários e grupos** no portal.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A solução de monitoramento remoto inclui o as funções **Admin** e **Somente Leitura** quando é implantado pela primeira vez. Você pode adicionar funções personalizadas com diferentes conjuntos de permissões. Para definir uma função personalizada, você precisa:

- Adicionar a nova função ao aplicativo no portal do Azure.
- Definir uma política para a nova função no microsserviço de autenticação e autorização.
- Atualizar a interface do usuário web da solução.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definir a função personalizada no portal do Azure

As etapas a seguir descrevem como adicionar uma função a um aplicativo no Microsoft Azure Active Directory. Neste exemplo, você criará uma nova função que permite aos membros criar, atualizar e excluir dispositivos na solução de monitoramento remoto.

1. Localize o **Registro de aplicativo** para sua solução no portal do Azure. O nome do aplicativo é o nome da sua solução de Monitoramento Remoto. Na captura de tela a seguir, os nomes de exibição de solução e de aplicativo são **contoso-rm4**.

    ![Registro do aplicativo](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Selecione o seu aplicativo, em seguida, clique em **Manifesto**. Você pode ver as duas [funções de aplicativo](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) existentes definidas para o aplicativo:

    ![Visualizar manifesto](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Editar o manifesto para adicionar uma função chamada **ManageDevices** conforme mostrado no trecho a seguir. Você precisa de uma cadeia de caracteres exclusiva como um GUID para a nova ID de função. Você pode gerar um novo GUID, usando um serviço, como o [Gerador de GUID Online](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Salve as alterações.

### <a name="define-a-policy-for-the-new-role"></a>Definir uma política para a nova função

Depois, para adicionar a função para o aplicativo no portal do Azure, você precisa definir uma política em [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/pcs-auth/Services/data/policies/roles.json) para a função que atribui as permissões necessárias para gerenciar dispositivos.

1. Clone o repositório de [microsserviço de Autenticação e Autorização](https://github.com/Azure/pcs-auth-dotnet) do GitHub em seu computador local.

1. Edite o arquivo **Services/data/policies/roles.json** para adicionar a política para a função **ManageDevices**, conforme mostrado no trecho a seguir. Os valores de **ID** e **Função** devem corresponder a definição de função no manifesto do aplicativo da seção anterior. A lista de ações permitidas permite que alguém na função **ManageDevices** possa criar, atualizar e excluir dispositivos conectados à solução:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Quando você tiver terminado a edição do arquivo **Services/data/policies/roles.json**, recompile e reimplante o microsserviço de autenticação e autorização do acelerador da solução.

### <a name="how-the-web-ui-enforces-permissions"></a>Como a IU da web impõe permissões

A web de interface do usuário usa o [microsserviço de Autenticação e Autorização](https://github.com/Azure/pcs-auth-dotnet) para determinar quais ações de um usuário tem permissão para fazer e quais controles estão visíveis na interface do usuário. Por exemplo, se sua solução é chamada **contoso-rm4**, a interface da web recupera uma lista de ações permitidas para o usuário atual, enviando a solicitação a seguir:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Para um usuário chamado **Gerenciador de Dispositivos** na função **ManageDevices**, a resposta inclui o seguinte JSON no corpo:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

O trecho de código de [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) na [interface do usuário web](https://github.com/Azure/pcs-remote-monitoring-webui/) mostra como as permissões são impostas declarativamente:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Para saber mais, consulte as [Propriedades dos Componentes](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Você pode definir permissões adicionais no arquivo [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js).

### <a name="how-the-microservices-enforce-permissions"></a>Como os microsserviços impõem permissões

Os microsserviços também verificam permissões para proteger contra solicitações não autorizadas de API. Quando um microsserviço recebe uma solicitação de API, ele decodifica e valida o token JWT para obter a ID de usuário e permissões associadas à função do usuário.

O trecho a seguir do arquivo [DevicesController.cs](https://github.com/Azure/iothub-manager-dotnet/blob/master/WebService/v1/Controllers/DevicesController.cs) no [microsserviço do Gerenciador de IoTHub](https://github.com/Azure/iothub-manager-dotnet), mostra como as permissões são aplicadas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu que os controles são implementados no acelerador de solução do Monitoramento Remoto.

Para obter mais informações conceituais sobre o acelerador da solução Monitoramento Remoto, veja [Arquitetura de Monitoramento Remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para saber mais sobre como personalizar a solução Monitoramento Remoto, veja [Personalizar e reimplantar um microsserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->