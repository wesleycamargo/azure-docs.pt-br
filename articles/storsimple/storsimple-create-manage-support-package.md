<properties 
   pageTitle="Criar um pacote de suporte do StorSimple | Microsoft Azure"
   description="Saiba como criar, descriptografar e editar um pacote de suporte para o dispositivo StorSimple."
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
   ms.date="09/15/2015"
   ms.author="alkohli" />


# Criar e gerenciar um pacote de suporte do StorSimple

## Visão geral

Este tutorial descreve as várias tarefas associadas à criação e ao gerenciamento de um pacote de suporte. Um pacote de suporte inclui todos os logs relevantes em um formato compactado e criptografado e é usado para auxiliar a equipe de Suporte da Microsoft na solução de problemas do dispositivo StorSimple.

Este tutorial inclui instruções passo a passo para criar e gerenciar o pacote de suporte usando o seguinte:

- A seção **Pacote de suporte** da página **Manutenção** no serviço StorSimple Manager
- Windows PowerShell para StorSimple

Depois de ler este tutorial, você poderá:

- Criar um pacote de suporte
- Descriptografar e editar um pacote de suporte


## Criar um pacote de suporte no Portal de Gerenciamento

Para solucionar os problemas que possam ocorrer com o serviço StorSimple Manager, você pode criar e carregar um pacote de suporte para o site do Suporte da Microsoft por meio da página **Manutenção** do serviço no Portal de Gerenciamento. Você precisará fornecer uma chave de acesso de suporte para permitir o carregamento. A chave de acesso de suporte deve ser fornecida a você por seu Engenheiro de Suporte em um email. Um pacote de suporte não criptografado e compactado é criado (arquivo .cab). Esse pacote pode ser recuperado pelo Engenheiro de Suporte do site de Suporte quando o engenheiro fornece a chave de acesso.

Execute as seguintes etapas no Portal de Gerenciamento para criar um pacote de suporte:

#### Para criar um pacote de suporte no Portal de Gerenciamento

1. Navegue até **Dispositivos > Manutenção**.

2. Na seção **Pacote de suporte**, clique em **Criar e carregar pacote de suporte**.

3. Na caixa de diálogo **Criar e carregar pacote de suporte**, faça o seguinte:

	![Criar um pacote de suporte](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- Forneça a **Chave de acesso de suporte**. Essa chave deve ser enviada a você por seu Engenheiro de Suporte da Microsoft em um email.
 	
	- Marque a caixa de combinação para dar consentimento para **carregar automaticamente o pacote de suporte para o site de Suporte da Microsoft**.
 	
	- Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-create-manage-support-package/IC740895.png).


## Criar um pacote de suporte no Windows PowerShell para StorSimple

Se precisar editar os arquivos de log antes de criar um pacote, você precisará criar o pacote por meio do Windows PowerShell para StorSimple.

Execute as seguintes etapas para criar um pacote de suporte no Windows PowerShell para StorSimple:


#### Para criar um pacote de suporte no Windows PowerShell para StorSimple

1. Digite o seguinte comando para iniciar uma sessão do Windows PowerShell como administrador no computador remoto usado para se conectar ao dispositivo StorSimple:

	`Start PowerShell`

2. Na sessão do Windows PowerShell, conecte-se ao espaço de execução do Console do SSAdmin do dispositivo:


	- No prompt de comando, digite: 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. Na caixa de diálogo que é aberta, digite sua senha de administrador do dispositivo. A senha padrão é:
	 
		`Password1`

		![Sessão do PowerShell para SSAdminConsole Runspace](./media/storsimple-create-manage-support-package/IC740962.png)

	2. Clique em **OK**.
	1. No prompt de comando, digite: 
		
		`Enter-PSSession $MS`


3. Na sessão que é aberta, digite o comando apropriado.


	- Para compartilhamentos de rede protegidos por senha, digite:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		Você será solicitado a fornecer uma senha, um caminho para a pasta compartilhada de rede e uma senha de criptografia (porque o pacote de suporte é criptografado). Quando as informações forem fornecidas, um pacote de suporte será criado na pasta especificada.
											

	- Para pastas compartilhadas de rede aberta (aquelas que não são protegidas por senha), não é necessário o parâmetro `-Credential`. Digite o seguinte:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		O pacote de suporte será criado para ambos os controladores na pasta compartilhada de rede especificada. É um arquivo compactado e criptografado que pode ser enviado ao Suporte da Microsoft para solução de problemas. Para obter mais informações, consulte [Contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).


### Para obter mais informações sobre o cmdlet Export-HcsSupportPackage
Os diferentes parâmetros que podem ser usados com o cmdlet Export-HcsSupportPackage estão na tabela abaixo.

| S. Nº | Parâmetro | Obrigatório/Opcional | Descrição |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Caminho | Obrigatório | Use para fornecer o local da pasta compartilhada de rede na qual o pacote de suporte será colocado. |
| 2 | EncryptionPassphrase | Obrigatório | Use para fornecer uma senha para ajudar a criptografar o pacote de suporte. |
| 3 | Credencial | Opcional | Use esse parâmetro para fornecer credenciais de acesso para a pasta compartilhada de rede. |
| 4 | Forçar | Opcional | Use para ignorar a etapa de confirmação de senha de criptografia. |
| 5 | PackageTag | Opcional | Use para especificar um diretório no Caminho no qual o pacote de suporte será colocado. O padrão é [nome do dispositivo]-[data e hora atuais:aaaa-MM-dd-HH-mm-ss]. |
| 6 | Escopo | Opcional | Especifique como **Cluster** (padrão) para criar um pacote de suporte para ambos os controladores. Se você quiser criar um pacote somente para o controlador atual, especifique **Controlador**. |


## Editar um pacote de suporte

Após gerar um pacote de suporte, talvez você precise editá-lo para remover informações específicas do cliente, como nomes de volume, endereços IP de dispositivos e nomes de backup dos arquivos de log.

> [AZURE.IMPORTANT]Você só pode editar um pacote de suporte que tenha sido gerado por meio do Windows PowerShell para StorSimple. Você não pode editar um pacote criado no Portal de Gerenciamento com o serviço StorSimple Manager.

Para editar um pacote de suporte antes de carregá-los no site de Suporte da Microsoft, você precisará descriptografar o pacote de suporte, editar os arquivos e criptografá-lo novamente. Execute as seguintes etapas para editar um pacote de suporte:

#### Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gere um pacote de suporte conforme descrito em [Criar um pacote de suporte no Windows PowerShell para StorSimple](#create-a-support-package-in-windows-powershell-for-storsimple).

2. [Baixe o script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente em seu cliente.

3. Importe o módulo do Windows PowerShell. Você precisará especificar o caminho para a pasta local em que você baixou o script. Para importar o módulo, digite:
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Abra a pasta do pacote de suporte. Observe que todos os arquivos são arquivos *.aes* compactados e criptografados. Abra os arquivos. Para abrir arquivos, digite:

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	Isso descompactará e descriptografará os arquivos. Você observará que as extensões de arquivo reais agora são exibidas para todos os arquivos.
	
	![Editar Pacote de Suporte 3](./media/storsimple-create-manage-support-package/IC750706.png)


5. Quando for solicitado a fornecer a senha de criptografia, digite a senha usada quando o pacote de suporte foi criado.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	
6. Navegue até a pasta que contém os arquivos de log. Como os arquivos de log agora estão descompactados e descriptografados, eles terão as extensões de arquivo originais. Modifique esses arquivos para remover todas as informações específicas do cliente, como nomes de volume e endereços IP de dispositivos, e salve os arquivos.

7. Feche os arquivos. Quando os arquivos forem fechados, serão compactados com Gzip e criptografados com AES-256. Isso é para garantir a segurança e a velocidade ao transferir o pacote de suporte em uma rede. Para fechar os arquivos, digite:

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![Editar Pacote de Suporte 2](./media/storsimple-create-manage-support-package/IC750707.png)

8. Quando solicitado, forneça uma senha de criptografia para o pacote de suporte modificado.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

9. Anote a nova senha para que você possa compartilhá-la com o Suporte da Microsoft quando solicitado.


### Exemplo: edição de arquivos em um pacote de suporte em um compartilhamento protegido por senha

Abaixo há um exemplo que demonstra como descriptografar, editar e criptografar novamente um pacote de suporte.

![Editar Pacote de Suporte 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## Próximas etapas

- Saiba como [usar pacotes de suporte e logs de dispositivo para solucionar problemas de implantação do dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting). 
- Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->