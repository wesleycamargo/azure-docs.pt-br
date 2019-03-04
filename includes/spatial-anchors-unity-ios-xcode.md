---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890995"
---
Selecione **Build** para abrir uma caixa de diálogo. Em seguida, selecione uma pasta para exportar o projeto do Xcode.

Quando a exportação for concluída, será exibida uma pasta que contém o projeto do Xcode exportado.

### <a name="open-the-xcode-project"></a>Abrir o projeto do Xcode

Na pasta do projeto do Xcode exportado, execute o seguinte comando para instalar o CocoaPods necessário para o projeto:

```bash
pod install --repo-update
```

Agora você pode abrir `Unity-iPhone.xcworkspace` para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se um erro `library not found for -lPods-Unity-iPhone` é exibido, provavelmente, você abriu o arquivo `.xcodeproj` em vez do `.xcworkspace`. Abra o `.xcworkspace` e tente novamente.

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e selecione a guia **Geral**.

Em **Assinatura**, selecione **Gerenciar assinatura automaticamente**. Selecione **Habilitar Automático** na caixa de diálogo exibida para redefinir as configurações de build.

Em **Informações de Implantação**, verifique se o **Destino de Implantação** está definido como `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** como o dispositivo iOS.

![Selecionar o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e, em seguida, executar o esquema atual**.

![Implantar e executar](./media/spatial-anchors-unity/deploy-run.png)