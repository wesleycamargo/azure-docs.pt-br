---
title: Configurar um projeto de serviço de nuvem do Azure com o Visual Studio | Microsoft Docs
description: Saiba como configurar um projeto de serviço de nuvem do Azure no Visual Studio, dependendo dos requisitos para o projeto.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher

---
# Configure um projeto de serviço de nuvem do Azure com o Visual Studio
Você pode configurar um projeto de serviço de nuvem do Azure, dependendo dos requisitos para o projeto. Você pode definir propriedades do projeto para as seguintes categorias:

* **Publicar um serviço de nuvem no Azure**
  
  Você pode definir uma propriedade para certificar-se de que um serviço de nuvem existente implantado no Azure não é excluído acidentalmente.
* **Executar ou depurar um serviço de nuvem no computador local**
  
  Você pode selecionar uma configuração de serviço para usar e indicar se deseja iniciar o emulador de armazenamento do Azure.
* **Validar um pacote de serviço de nuvem quando ele é criado**
  
  Você pode optar por tratar todos os avisos como erros, para que você possa certificar-se de que o pacote de serviço de nuvem será implantado sem problemas. Isso reduz o tempo de espera se você implantar e, em seguida, descobrir que ocorreu uma falha.

A ilustração a seguir mostra como selecionar uma configuração a ser usada quando você executa ou depura o serviço de nuvem localmente. Você pode definir qualquer uma das propriedades do projeto que necessita nesta janela, conforme mostrado na ilustração.

![Configurar um projeto do Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Para configurar um projeto de serviço de nuvem do Azure
1. Para configurar um projeto do serviço de nuvem **Gerenciador de Soluções**, abra o menu de atalho para o projeto de serviço de nuvem e, em seguida, escolha **Propriedades**.
   
   Uma página com o nome do projeto de serviço de nuvem aparece no editor do Visual Studio.
2. Escolha a guia **Desenvolvimento**.
3. Para certificar-se de não excluir acidentalmente uma implantação existente no Azure, no prompt antes de excluir uma lista de implantação existente, escolha **True**.
4. Para selecionar a configuração de serviço que você deseja usar quando executa ou depura seu serviço de nuvem localmente, na lista **Configuração do Serviço**, escolha a configuração do serviço.
   
   > [!NOTE]
   > Se você deseja criar uma configuração de serviço para usar, consulte como: gerenciar configurações de serviço e perfis. Se você quiser modificar uma configuração de serviço para uma função, consulte [como configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
   > 
   > 
5. Para iniciar o emulador de armazenamento do Azure, quando você executa ou depura seu serviço de nuvem localmente, no **Emulador de Armazenamento do Azure**, escolha **Verdadeiro**.
6. Para certificar-se de que você não pode publicar se houver erros de validação de pacote, em **tratar avisos como erros**, escolha **Verdadeiro**.
7. Para certificar-se de que sua função web usa a mesma porta cada vez que inicia localmente no IIS Express, em **Usar Portas de Projeto Web**, escolha **Verdadeiro**. Para usar uma porta específica para um projeto Web específico, abra o menu de atalho para o projeto Web, escolha a guia **Propriedades**, escolha a guia **Web** e altere o número da porta no **Url do projeto** definindo na seção **IIS Express**. Por exemplo, digite `http://localhost:14020` como a URL do projeto.
8. Para salvar as alterações feitas nas propriedades do projeto de serviço de nuvem, escolha o botão **Salvar** na barra de ferramentas.

## Próximas etapas
Para saber mais sobre como configurar projetos de serviço de nuvem do Azure no Visual Studio, consulte [Configurando seu projeto do Azure usando várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=AcomDC_0817_2016-->