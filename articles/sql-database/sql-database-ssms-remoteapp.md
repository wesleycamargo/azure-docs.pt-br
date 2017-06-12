---
title: Conectar Banco de Dados SQL usando o SQL Server Management Studio no Azure RemoteApp | Microsoft Docs
description: "Use este tutorial para aprender a usar o SQL Server Management Studio no Azure RemoteApp para segurança e desempenho ao se conectar ao Banco de Dados SQL"
services: sql-database
documentationcenter: 
author: adhurwit
manager: jhubbard
ms.assetid: 1052c83c-e7f5-4736-922f-216194d8874b
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: adhurwit
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 92cad42344fb6f77b3088f74ea0e37a542bd0378
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017


---
# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Usar o SQL Server Management Studio no Azure RemoteApp para conectar-se ao Banco de dados SQL

> [!IMPORTANT]
> O RemoteApp do Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
>

## <a name="introduction"></a>Introdução
Este tutorial mostra como usar o SQL Server Management Studio (SSMS) no RemoteApp do Azure para conectar-se ao Banco de Dados SQL. Ele fornece uma orientação pelo processo de configuração do SQL Server Management Studio no Azure RemoteApp, explica os benefícios e mostra os recursos de segurança que podem ser usados no Active Directory do Azure.

**Tempo estimado para conclusão:** 45 minutos

## <a name="ssms-in-azure-remoteapp"></a>SSMS no Azure RemoteApp
O Azure RemoteApp é um serviço RDS no Azure que fornece aplicativos. Saiba mais sobre ele aqui: [O que é o RemoteApp?](../remoteapp/remoteapp-whatis.md)

O SSMS em execução no Azure RemoteApp fornece a mesma experiência que a execução local do SSMS.

![Captura de tela mostrando o SSMS em execução no Azure RemoteApp][1]

## <a name="benefits"></a>Benefícios
Há muitos benefícios no uso do SSMS no Azure RemoteApp, incluindo:

* A porta 1433 no Azure SQL Server não precisa ser exposta externamente (fora do Azure).
* Não há necessidade de continuar a adicionar e remover endereços IP no firewall do Azure SQL Server.
* Todas as conexões do Azure RemoteApp ocorrem por HTTPS na porta 443 usando o protocolo de Área de Trabalho Remota
* Ele é multiusuário e pode ser dimensionado.
* Há um ganho de desempenho por ter o SSMS na mesma região que o Banco de Dados SQL.
* Você pode auditar o uso do Azure RemoteApp com o Active Directory do Azure Premium Edition, que tem relatórios de atividade do usuário.
* Você pode habilitar a MFA (Multi-Factor Authentication).
* Acesse o SSMS em qualquer lugar ao usar qualquer um dos clientes com suporte do Azure RemoteApp, entre eles iOS, Android, Mac, Windows Phone e PCs com Windows.

## <a name="create-the-azure-remoteapp-collection"></a>Criar a coleção do Azure RemoteApp
Veja a seguir as etapas para criar sua coleção do Azure RemoteApp com o SSMS:

### <a name="1-create-a-new-windows-vm-from-image"></a>1. Criar uma nova VM do Windows a partir da Imagem
Use a imagem "Windows Server Remote Desktop Session Host Windows Server 2012 R2" da Galeria para criar sua nova VM.

### <a name="2-install-ssms-from-sql-express"></a>2. Instalar o SSMS do SQL Express
Acesse a nova VM e navegue até essa página de download: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/download/details.aspx?id=42299)

Há uma opção para baixar somente o SSMS. Após o download, vá até o diretório de instalação e execute a Instalação para instalar o SSMS.

Também é necessário instalar o SQL Server 2014 Service Pack 1. Você pode baixá-lo aqui: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/download/details.aspx?id=46694)

O SQL Server 2014 Service Pack 1 inclui uma funcionalidade essencial para trabalhar com o Banco de Dados SQL.

### <a name="3-run-validate-script-and-sysprep"></a>3. Executar o script Validate e o Sysprep
Na área de trabalho da VM há um script do PowerShell chamado Validate. Execute-o clicando duas vezes nele. Ele verificará se a VM está pronta para ser usada para hospedagem remota de aplicativos. Quando a verificação estiver concluída, você receberá uma solicitação para executar o sysprep. Escolha executá-lo.

Após a conclusão do sysprep, ele desligará a VM.

Para saber mais sobre como criar uma imagem do Azure RemoteApp, confira: [Como criar uma imagem de modelo do RemoteApp no Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)

### <a name="4-capture-image"></a>4. Capturar a imagem
Após a interrupção da execução da VM, encontre-a no portal atual e capture-a.

Para saber mais sobre como capturar uma imagem, consulte [Capturar uma imagem de uma máquina virtual Windows do Azure criada com o modelo de implantação clássico](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Adicionar a imagens de modelo do Azure RemoteApp
Na seção Azure RemoteApp do portal atual, acesse a guia Imagens de Modelo e clique em Adicionar. Na caixa pop-up, selecione "Importar uma imagem da biblioteca Máquinas Virtuais" e, em seguida, escolha a imagem que você acabou de criar.

### <a name="6-create-cloud-collection"></a>6. Criar uma coleção na nuvem
No portal atual, crie uma nova Coleção na Nuvem do Azure RemoteApp. Escolha a Imagem de Modelo que você acabou de importar com o SSMS instalado.

![Criar uma nova coleção na nuvem][2]

### <a name="7-publish-ssms"></a>7. Publicar o SSMS
Na guia Publicação de sua nova coleção na nuvem, selecione Publicar um aplicativo no Menu Iniciar e, em seguida, escolha o SSMS na lista.

![Publicar aplicativo][5]

### <a name="8-add-users"></a>8. Adicionar usuários
Na guia Acesso de Usuário, você pode selecionar os usuários que terão acesso a esta coleção do Azure RemoteApp que inclui somente o SSMS.

![Adicionar usuário][6]

### <a name="9-install-the-azure-remoteapp-client-application"></a>9. Instalar o aplicativo cliente do Azure RemoteApp
Você pode baixar e instalar um cliente do Azure RemoteApp aqui: [Baixar | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)

## <a name="configure-azure-sql-server"></a>Configurar o Azure SQL Server
A única configuração necessária é garantir que os Serviços do Azure estejam habilitados para o firewall. Se você usar essa solução, não será necessário adicionar endereços IP para abrir o firewall. O tráfego de rede permitido para o SQL Server é de outros serviços do Azure.

![Permissão do Azure][4]

## <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)
A MFA pode ser habilitada especificamente para esse aplicativo. Acesse a guia Aplicativos do Active Directory do Azure. Você encontrará uma entrada para o Microsoft Azure RemoteApp. Se você clicar nesse aplicativo e configurá-lo, verá a página abaixo de onde você pode habilitar a MFA para este aplicativo.

![Habilitar MFA][3]

## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Auditar a atividade do usuário com o Active Directory Premium do Azure
Se você não tiver o Azure AD Premium, será necessário ativá-lo na seção Licenças do diretório. Com o Premium ativado, você poderá atribuir usuários para o nível Premium.

Quando você acessa um usuário no Active Directory do Azure, é possível acessar a guia Atividade para ver informações de logon para o Azure RemoteApp.

## <a name="next-steps"></a>Próximas etapas
Depois de concluir todas as etapas acima, você será capaz de executar o cliente do Azure RemoteApp e fazer logon com um usuário atribuído. Você receberá o SSMS como um de seus aplicativos e poderá executá-lo da mesma forma que se estivesse instalado em seu computador com acesso ao Azure SQL Server.

Para saber mais sobre como fazer a conexão com o Banco de Dados SQL, confira [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md).

Isso é tudo por enquanto. Aproveite!

<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
