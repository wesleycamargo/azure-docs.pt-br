<properties 
	pageTitle="Usar privilégios de raiz nas máquinas virtuais Linux do Azure" 
	description="Saiba como usar privilégios de raiz em uma máquina virtual Linux do Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="szark"/>




# Utilizando privilégios de raiz nas máquinas virtuais Linux do Azure

Por padrão, o usuário  `root` está desabilitado nas máquinas virtuais Linux no Azure. Os usuários podem executar comandos com privilégios elevados usando o comando  `sudo`. No entanto, a experiência pode variar dependendo de como o sistema foi fornecido.

1. **A chave SSH e a senha OU somente senha** - a máquina virtual foi configurada com um certificado (arquivo '.CER') ou chave SSH, bem como uma senha, ou apenas um nome de usuário e senha. Neste caso `sudo` solicitará a senha do usuário antes de executar o comando.

2. **Somente a chave SSH** - a máquina virtual foi provisionada com um certificado (arquivo `.cer` ou `.pem`) ou chave SSH, mas nenhuma senha.  Neste caso  `sudo` **não** solicitará a senha do usuário antes de executar o comando.


## SSH chave e a senha ou a senha apenas

Faça logon na máquina virtual Linux usando autenticação de senha ou chave SSH, em seguida, executar comandos usando  `sudo`, por exemplo:

	# sudo <command>
	[sudo] password for azureuser:

Nesse caso, o usuário será solicitado para uma senha. Depois de inserir a senha,  `sudo` executará o comando com raiz `root` privilégios.


## Somente a chave SSH

Faça logon na máquina virtual Linux usando autenticação de chave SSH, em seguida, execute comandos usando  `sudo`, por exemplo:

	# sudo <command>

Nesse caso, o usuário **não** será solicitado para uma senha. Depois de pressionar `<enter,>` `sudo` executará o comando com  `root` privilégios.


<!--HONumber=42-->
