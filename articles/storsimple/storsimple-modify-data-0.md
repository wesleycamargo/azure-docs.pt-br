<properties 
   pageTitle="Modificar as configurações de interface de rede DADOS 0 em seu dispositivo StorSimple"
   description="Saiba como reconfigurar a interface de rede DADOS 0 em seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="alkohli" />

# Modificar as configurações de interface de rede DADOS 0 em seu dispositivo StorSimple

## Visão geral
O dispositivo Microsoft Azure StorSimple tem seis interfaces de rede de DADOS 0 a DADOS 5. A interface de DADOS 0 é sempre configurada por meio da interface do Windows PowerShell ou do console serial e é habilitada para a nuvem automaticamente. A interface de DADOS 0 é configurada primeiro por meio do assistente de instalação durante a implantação inicial do dispositivo StorSimple. Quando o dispositivo está em modo operacional, talvez você precise redefinir as configurações de DADOS 0. Este tutorial fornece dois métodos para modificar as configurações de rede de DADOS 0; os dois por meio do Windows PowerShell para StorSimple.

Depois de ler este tutorial, você poderá:

- Modificar a configuração de rede de DADOS 0 por meio do assistente de instalação
- Modificar as configurações de rede de DADOS 0 por meio do cmdlet`Set-HcsNetInterface`


## Modificar as configurações de rede de DADOS 0 por meio do assistente de instalação
Você pode reconfigurar as definições de rede de DADOS 0 conectando-se à interface do Windows PowerShell de seu dispositivo StorSimple e iniciando uma sessão do assistente de instalação. Execute as etapas a seguir para modificar as configurações de DADOS 0:

#### Para modificar as configurações de rede de DADOS 0 por meio do assistente de instalação

1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.

1. No prompt de comando, digite:


	`Invoke-HcsSetupWizard`

1. Um assistente de instalação aparecerá para lhe ajudar a configurar a interface dos DADOS 0 do seu dispositivo. Fornece novos valores para o endereço IP, gateway e máscara de rede.

> [AZURE.NOTE]Os IPs de controladores fixos precisarão ser reconfigurados por meio da página **Configurar** do dispositivo StorSimple no Portal de Gerenciamento do Azure. Para obter mais informações, vá para [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces).


## Modificar as configurações de rede de DADOS 0 por meio do cmdlet Set-HcsNetInterface
Uma maneira alternativa de reconfigurar a interface de rede DADOS 0 é através do uso do cmdlet `Set-HcsNetInterface`. O cmdlet é executado na interface do Windows PowerShell do seu dispositivo StorSimple. Execute as etapas a seguir para modificar as configurações de DADOS 0:

#### Para modificar as configurações de rede de DADOS 0 por meio do cmdlet Set-HcsNetInterface

1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a senha de administrador do dispositivo. A senha padrão é `Password1`.

2. No prompt de comando, digite:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
	
    Entre os colchetes angulares, digite os seguintes valores para DATA 0:
											
	- Endereço IPv4
	
	- Gateway IPv4
	
	- Máscara da sub-rede IPv4
	
	- Endereço IPv4 fixo para o controlador 0

	- Endereço IPv4 fixo para o controlador 1

## Próximas etapas

Para configurar as interfaces de rede que não sejam DATA 0, você pode usar a [página Configurar no Portal de Gerenciamento](storsimple-modify-device-config.md). Se você tiver problemas ao configurar suas interfaces de rede, consulte [Solucionar problemas de implantação](storsimple-troubleshoot-deployment.md).

<!---HONumber=August15_HO8-->