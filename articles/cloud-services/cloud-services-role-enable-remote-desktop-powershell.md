<properties 
pageTitle="Habilitar a Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure usando o PowerShell" 
description="Como configurar seu aplicativo de serviço de nuvem do Azure usando o PowerShell para permitir conexões de área de trabalho remota" 
services="cloud-services" 
documentationCenter="" 
authors="thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="01/19/2016" 
ms.author="adegeo"/>

# Habilitar a Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure usando o PowerShell

>[AZURE.SELECTOR]
- [Portal clássico do Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar a conexão da área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo durante a execução.

Este artigo descreve como habilitar a área de trabalho remota em suas funções de serviço de nuvem usando o PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para este artigo. O PowerShell usa a abordagem de Extensão da Área de Trabalho Remota para que você possa habilitar a Área de Trabalho Remota, mesmo depois que o aplicativo for implantado.


## Configurar a Área de Trabalho Remota por meio do PowerShell

O cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permite habilitar a Área de Trabalho Remota em funções especificadas ou todas as funções da implantação do serviço de nuvem. O cmdlet permite que você especifique o nome de usuário e a senha para o usuário da área de trabalho remota por meio do parâmetro *Credential*, que aceita um objeto PSCredential.

Se estiver usando o PowerShell interativamente, você pode definir facilmente o objeto PSCredential chamando o cmdlet [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx).

```
	$remoteusercredentials = Get-Credential
```

Isso exibirá uma caixa de diálogo, permitindo que você insira o nome de usuário e a senha para o usuário remoto de modo seguro.

Já que o PowerShell será usado principalmente para cenários de automação, você também pode configurar o objeto PSCredential de modo que não exija interação do usuário. Para fazer isso, primeiro você precisa configurar uma senha de segurança. Comece com a especificação de uma senha de texto sem formatação e converta-a em uma cadeia de caracteres segura usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida, você precisa converter essa cadeia de caracteres segura em uma cadeia de caracteres criptografada padrão usando [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora você pode salvar essa cadeia de caracteres criptografada padrão em um arquivo, usando [Set-Content](https://technet.microsoft.com/library/ee176959.aspx). Ao criar o objeto PSCredential, é possível ler por meio deste arquivo para definir a senha de uma maneira segura, sem ter que especificar a senha em um prompt ou armazená-la como texto sem formatação.

Use o PowerShell a seguir para criar um arquivo de senha de segurança:

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

Uma vez criado o arquivo de senha (password.txt), você usará apenas esse arquivo e não terá que especificar a senha em texto sem formatação. Se precisar atualizar a senha, você poderá executar o PowerShell acima novamente, com a nova senha, para gerar um novo arquivo password.txt.

>[AZURE.IMPORTANT] Ao definir a senha, certifique-se de atender aos [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto de credencial com base no arquivo de senha segura, você deve ler os conteúdos do arquivo e convertê-los novamente em uma cadeia de caracteres segura, usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Além de credenciais, o cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) também aceita um parâmetro *Expiration* que especifica um DateTime (data e hora) em que a conta de usuário vai expirar. Você pode obtê-lo especificando uma data e hora estáticas ou você pode simplesmente optar por expirar a conta em alguns dias a partir da data atual.

Este PowerShell de exemplo mostra como definir a Extensão de Área de Trabalho Remota em um serviço de nuvem:

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
Você também pode especificar o slot de implantação e as funções em que deseja habilitar a área de trabalho remota. Se esses parâmetros não forem especificados, o cmdlet usará por padrão o slot de implantação Production e habilitará a área de trabalho remota em todas as funções na implantação de produção.

A extensão de Área de Trabalho Remota está associada uma implantação. Se criar uma nova implantação para o serviço, você precisará habilitar novamente a área de trabalho remota na nova implantação. Se quiser ter a área de trabalho remota sempre habilitada em suas implantações, você deve considerar integrar os scripts do PowerShell para habilitar a área de trabalho remota em seu fluxo de trabalho de implantação.


## Área de Trabalho Remota em uma instância de função
O cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) pode ser usado para a área de trabalho remota em uma instância de função específica do serviço de nuvem. Você pode usar o parâmetro *LocalPath* no cmdlet para baixar o arquivo RDP localmente, ou você pode usar o parâmetro *Launch* para iniciar diretamente a caixa de diálogo Conexão de Área de Trabalho Remota para acessar a instância de função do serviço de nuvem.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Verifique se a extensão de Área de Trabalho Remota está habilitada em um serviço 
O cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) exibe se a área de trabalho remota está ou não habilitada em uma implantação de serviço. O cmdlet retornará o nome de usuário para o usuário de área de trabalho remota e as funções nas quais a extensão de área de trabalho remota está habilitada. Você pode, opcionalmente, especificar um slot de implantação cujo tipo padrão será de produção.

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Remover a extensão de Área de Trabalho Remota de um serviço 
Se você já tiver habilitado a extensão de área de trabalho remota em uma implantação e precisar atualizar as configurações de área de trabalho remota, você deverá primeiro remover a extensão de área de trabalho remota e depois habilitá-la novamente com as novas configurações. Por exemplo, se você deseja definir uma nova senha para a conta de usuário remoto ou se a conta de usuário expirou, você precisa remover a extensão e, em seguida, adicioná-la novamente com a nova senha ou expiração. Isso só é necessário em implantações existentes que têm a extensão de área de trabalho remota habilitada. Para novas implantações que você pode chamar, apenas aplique a extensão diretamente.

Para remover a extensão de área de trabalho remota de uma implantação de serviço, você pode usar o cmdlet [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx). Você também pode especificar o slot de implantação e a função dos quais você deseja remover a extensão da área de trabalho remota.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE] O parâmetro *UninstallConfiguration* desinstalará qualquer configuração de extensão que tenha sido aplicada ao serviço. Em toda a configuração de extensão que está associada à configuração do serviço para ativar a extensão com uma implantação, a implantação deve ser associada a essa configuração de extensão. Chamar o cmdlet Remove sem *UninstallConfiguration* dissociará a implantação da configuração de extensão, removendo assim efetivamente a extensão da implantação. No entanto, a configuração de extensão ainda permanecerá associada ao serviço. Para remover completamente a configuração de extensão, você deve chamar o cmdlet Remove com o parâmetro *UninstallConfiguration*.



## Recursos adicionais

[Como configurar serviços de nuvem](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_0504_2016-->