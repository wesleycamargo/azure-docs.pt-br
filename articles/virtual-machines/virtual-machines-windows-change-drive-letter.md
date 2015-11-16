<properties
	pageTitle="Tornar a unidade D de uma VM um disco de dados | Microsoft Azure"
	description="Descreve como alterar as letras da unidade para uma VM do Windows criada usando o modelo clássico de implantação para que você possa usar a unidade D: como uma unidade de dados."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# Usar a unidade D como uma unidade de dados em uma VM do Windows 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Se você precisar usar a unidade D para armazenar dados, siga estas instruções para usar uma letra de unidade diferente para o disco temporário. Nunca use o disco temporário para armazenar os dados que você precisa manter.

## Anexar o disco de dados

Primeiro, você precisará anexar o disco de dados à máquina virtual. Para anexar um novo disco, confira [Como anexar um disco de dados a uma máquina virtual do Windows][Attach].

Se você quiser usar um disco de dados existente, verifique se carregou também o VHD para a Conta de armazenamento. Para obter instruções, consulte as etapas 3 e 4 em [Criar e carregar um VHD do Windows Server no Azure][VHD].


## Mover temporariamente o pagefile.sys para a unidade C

1. Conectar-se à máquina virtual. 

2. Clique com o botão direito no menu **Iniciar** e selecione **Sistema**.

3. No menu à esquerda, selecione **Configurações avançadas do sistema**.

4. Na seção **Desempenho**, selecione **Configurações**.

5. Selecione a guia **Avançado**.

5. Na seção **Memória virtual**, selecione **Alterar**.

6. Selecione a unidade **C**, em seguida, clique em **Tamanho gerenciado do sistema** e clique em **Definir**.

7. Selecione a unidade **C**, em seguida, clique em **Nenhum arquivo de paginação** e clique em **Definir**.

8. Clique em Aplicar. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.

9. Reinicie a máquina virtual.




## Alterar a letra da unidade 

1. Depois que a VM for reiniciada, faça logon novamente na VM.

2. Clique no menu **Iniciar**, digite **diskmgmt. msc** e pressione Enter. O Gerenciamento de Disco será iniciado.

3. Clique com o botão direito do mouse em **D**, a unidade de Armazenamento Temporário, e selecione **Alterar Letra da Unidade e Caminhos**.

4. Na Letra da unidade, selecione a unidade **G** e clique em **OK**.

5. Clique com o botão direito do mouse no disco de dados e selecione **Alterar Letra da Unidade e Caminhos**.

6. Na Letra da unidade, selecione a unidade **D** e clique em **OK**.

7. Clique com o botão direito do mouse em **G**, a unidade de Armazenamento Temporário, e selecione **Alterar Letra da Unidade e Caminhos**.

8. Na Letra da unidade, selecione a unidade **E** e clique em **OK**.

> [AZURE.NOTE]Se sua VM tiver outros discos ou unidades, use o mesmo método para reatribuir as letras da unidade dos outros discos e unidades. Você deseja que a configuração do disco seja: - C: disco do SO - D: Disco de Dados - E: Disco temporário



## Mova o pagefile.sys de volta para a unidade de armazenamento temporário 

1. Clique com o botão direito no menu **Iniciar** e selecione **Sistema**

2. No menu à esquerda, selecione **Configurações avançadas do sistema**.

3. Na seção **Desempenho**, selecione **Configurações**.

4. Selecione a guia **Avançado**.

5. Na seção **Memória virtual**, selecione **Alterar**.

6. Selecione a unidade **C** do SO, em seguida, clique em **Nenhum arquivo de paginação** e clique em **Definir**.

7. Selecione a unidade **E** de armazenamento temporário, em seguida, clique em **Tamanho gerenciado do sistema** e clique em **Definir**.

8. Clique em **Aplicar**. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.

9. Reinicie a máquina virtual.




## Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server][Logon]

[Como desanexar um disco de dados de uma máquina virtual Windows][Detach]

[Sobre as contas de Armazenamento do Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Nov15_HO2-->