---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cb7cf932433927d4ead853952ff74d55c55b9cf7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160014"
---
Galerias de imagens compartilhadas permitem que você tenha imagens de compartilhamento usando o RBAC. Você pode usar o RBAC para compartilhar imagens dentro do seu locatário e até mesmo pessoas fora do seu locatário. Mas, se você quiser compartilhar imagens fora do seu locatário do Azure, em grande escala, você deve criar um registro de aplicativo para facilitar o compartilhamento.  Usar um registro de aplicativo pode habilitar cenários de compartilhamento mais complexos, como: 

* Gerenciando compartilhadas imagens quando uma empresa adquire outra e a infraestrutura do Azure é distribuída entre locatários separados. 
* Parceiros do Azure gerenciar a infraestrutura do Azure em nome dos clientes. Personalização de imagens é feita dentro do locatário parceiros, mas as implantações de infraestrutura que vai acontecer no locatário do cliente. 


## <a name="create-the-app-registration"></a>Criar o registro do aplicativo

Crie um registro de aplicativo que será usado por ambos os locatários para compartilhar os recursos da Galeria de imagem.
1. Abra o [registros do aplicativo (visualização) no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecione **novo registro** no menu na parte superior da página.
1. Na **nome**, digite *myGalleryApp*.
1. Na **suporte para tipos de conta**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na **URI de redirecionamento**, digite *https://www.microsoft.com* e, em seguida, selecione **registrar**. Depois que o registro do aplicativo tiver sido criado, a página de visão geral será aberta.
1. Na página Visão geral, copie o **ID do aplicativo (cliente)** e salve para uso posterior.   
1. Selecione **certificados e segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Na **descrição**, digite *segredo de aplicativo entre locatários de galeria de imagem compartilhado*.
1. Na **Expires**, deixe o padrão de **em 1 ano** e, em seguida, selecione **adicionar**.
1. Copie o valor do segredo e salvá-lo em um local seguro. Você não pode recuperá-la depois que você sair da página.


Conceda a permissão de registro de aplicativo para usar a Galeria de imagens compartilhadas.
1. No portal do Azure, selecione a Galeria de imagens compartilhado que você deseja compartilhar com outro locatário.
1. Selecione **selecione o controle de acesso (IAM)** e, em **Adicionar atribuição de função** selecionar *adicionar*. 
1. Sob **função**, selecione **leitor**.
1. Sob **atribuir acesso a:**, mantenha-o como **usuário, grupo ou entidade de serviço do Azure AD**.
1. Sob **selecionar**, digite *myGalleryApp* e selecione-o quando ele aparece na lista. Quando você terminar, selecione **salvar**.


Crie uma entidade de serviço para o locatário 2 a ser usado para acessar o aplicativo, solicitando uma entrada usando um navegador. Substitua *<Tenant2 ID>* com a ID do locatário para o locatário que você gostaria de compartilhar com sua galeria de imagens. Substitua *< ID do aplicativo (cliente) >* com a ID do aplicativo de registro de aplicativo que você criou. Quando terminar de fazer as substituições, cole a URL em um navegador e siga os prompts de entrada para entrar no locatário 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

No [portal do Azure](https://portal.azure.com) entre como o locatário 2 e conceder o acesso de registro de aplicativo para o grupo de recursos onde você deseja criar a VM.

1. Selecione o grupo de recursos e, em seguida, selecione **controle de acesso (IAM)**. Sob **Adicionar atribuição de função** selecionar **Add**. 
1. Sob **função**, digite **Colaborador**.
1. Sob **atribuir acesso a:**, mantenha-o como **usuário, grupo ou entidade de serviço do Azure AD**.
1. Sob **selecionar** tipo *myGalleryApp* , em seguida, selecione-o quando ele aparece na lista. Quando você terminar, selecione **salvar**.

> [!NOTE]
> Você precisa esperar para a versão da imagem concluir completamente que está sendo criado e replicadas antes de você pode usar a mesma imagem gerenciada para criar outra versão da imagem.

