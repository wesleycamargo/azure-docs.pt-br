---
title: Tutorial- Atualizar um aplicativo de Malha de malha de serviço do Azure | Microsoft Docs
description: Aprenda como atualizar um aplicativo do Service Fabric usando o Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: a734eb7b4efecf14a4d47a660c00d561a5fa6677
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971800"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Aprenda como atualizar um aplicativo do Service Fabric usando o Visual Studio

Este tutorial é a parte quatro de uma série e mostra como atualizar um aplicativo do Azure Service Fabric Mesh diretamente do Visual Studio. A atualização incluirá uma atualização de código e uma atualização de configuração. Você verá que as etapas para atualização e publicação no Visual Studio são as mesmas.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Atualizar um serviço de malha de malha de serviço usando o Visual Studio

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Crie um aplicativo Mesh de malha de serviço no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depure um aplicativo Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implantar uma malha de serviço do Fabric aplicativo](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uma malha de malha do serviço de atualização do aplicativo
> * [Limpar os recursos de malha do serviço do Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver implantado o aplicativo de tarefas, siga as instruções em [Publicar um aplicativo da Web do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

# <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualizar um serviço de malha de malha de serviço usando o Visual Studio

Este artigo mostra como atualizar de forma independente um micro serviço em um aplicativo.  Neste exemplo, modificaremos o serviço `WebFrontEnd` para exibir uma categoria de tarefa. Em seguida, atualizaremos o serviço implantado.

## <a name="modify-the-config"></a>Modificar a configuração

Os upgrades podem ser causados por alterações de código, alterações de configuração ou ambos.  Para introduzir uma alteração de configuração, abra o arquivo do `WebFrontEnd`projeto`service.yaml` (que está no nó **Service Resources**).

No `resources:` seção, altere `cpu:` de 0,5 a 1,0, em antecipação a que o front-end da web será muito usado. Agora, ele deve ser assim:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma mudança de código, adicione uma propriedade `Category` à classe `ToDoItem` no arquivo `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Em seguida, atualize o método `Load()`, no mesmo arquivo, para definir a categoria como uma string padrão:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modificar o serviço

O projeto `WebFrontEnd` é um aplicativo ASP.NET Core com uma página da Web que mostra itens da lista de tarefas pendentes. No projeto `WebFrontEnd`, abra `Index.cshtml` e adicione as seguintes duas linhas, indicadas abaixo, para exibir a categoria da tarefa:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Crie e execute o aplicativo para verificar se você vê uma nova coluna de categoria na página da Web que lista as tarefas.

## <a name="upgrade-the-app-from-visual-studio"></a>Atualizar o aplicativo do Visual Studio

Independentemente se você estiver fazendo uma atualização de código ou uma atualização de configuração (nesse caso, estamos fazendo ambos), para atualizar seu aplicativo de malha do serviço do Fabric no Azure botão direito do mouse em **todolistapp** no Visual Studio e selecione **publicar ...**

A seguir, você verá uma caixa de diálogo **Publicar aplicativo do Service Fabric**.

![Caixa de diálogo de publicação de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Escolha a conta e assinatura do Azure. Certifique-se de que **local** é definido como o local em que você usou quando você publicou originalmente o aplicativo de tarefas pendentes para o Azure. Este artigo usado **Leste dos EUA**.

Verifique se **Grupo de recursos** está definido como o grupo de recursos que você usou quando publicou originalmente o aplicativo de tarefas no Azure.

Verifique se o **Registro de Contêiner do Azure** está definido para o nome do registro do contêiner azul que você criou quando publicou originalmente o aplicativo de tarefas no Azure.

Na caixa de diálogo de publicação, pressione o botão **Publicar** para atualizar o aplicativo de tarefas no Azure.

Você pode monitorar o progresso da atualização, selecionando o **ferramentas do Service Fabric** painel no Visual Studio **saída** janela. Quando a atualização for concluída, a saída **Service Fabric Tools** exibirá o endereço IP e a porta do seu aplicativo na forma de uma URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um navegador da Web e navegue até a URL para ver o site em execução no Azure. Agora você deve ver uma página da web que contém uma coluna de categoria.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Como atualizar um aplicativo de serviço do Fabric bagunça usando o Visual Studio

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Limpar os recursos de malha do serviço do Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)