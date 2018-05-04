---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN

Um perfil CDN é um contêiner para pontos de extremidade CDN e especifica um tipo de preço.

1. No Portal do Azure, no canto superior esquerdo, selecione **Criar um recurso**.
    
    O **Novo** painel é exibido.
   
2. Selecione **Web + Celular**, depois **CDN**.
   
    ![Selecionar recursos CDN](./media/cdn-create-profile/cdn-new-resource.png)

    O painel **Perfil CDN** é exibido.

    Use as configurações especificadas na tabela que acompanha a imagem.
   
    ![Novo perfil CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Configuração  | Valor |
    | -------- | ----- |
    | **Nome** | Insira *my-cdn-profile-123* como nome de perfil. Esse nome deve ser globalmente exclusivo. Se ele já estiver em uso, você pode inserir um diferente. |
    | **Assinatura** | Selecione uma assinatura do Azure na lista suspensa.|
    | **Grupo de recursos** | Selecione **Criar novo** e insira *my-resource-group-123* como o nome para seu novo grupo de recursos. Esse nome deve ser globalmente exclusivo. Se ele já estiver em uso, você pode inserir um diferente. | 
    | **Localização do grupo de recursos** | Selecione **Centro dos EUA** na lista suspensa. |
    | **Tipo de preços** | Selecione **Verizon Standard** na lista suspensa. |
    | **Criar um novo ponto de extremidade CDN** | Deixe não selecionado. |  
   
3. Selecione **Fixar no painel** para salvar o perfil em seu painel após sua criação.
    
4. Selecione **Criar** para criar o perfil. 

