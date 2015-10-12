<properties
	pageTitle="Como posso alterar a letra da unidade do disco temporário? | Microsoft Azure"
	description="Altere a letra da unidade do disco temporário em uma máquina virtual Windows criada com o modelo de implantação clássica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="cynthn"/>

#Alterar a letra da unidade do disco temporário do Windows em uma máquina virtual criada com o modelo de implantação clássica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo clássico de implantação.

Se você precisar usar a unidade D para armazenar dados, siga estas instruções para usar uma unidade diferente para o disco temporário. Nunca use o disco temporário para armazenar os dados que você precisa manter.

Antes de começar, você precisará de um disco de dados anexado à máquina virtual, permitindo que você armazene o arquivo de paginação (pagefile. sys) do Windows durante esse procedimento. Consulte [Como anexar um disco de dados a uma máquina virtual Windows][Attach] se você não tiver um. Para obter instruções sobre como descobrir quais discos estão anexados, consulte "Localizar o disco" em [Como desanexar um disco de dados de uma máquina virtual Windows][Detach].

Se você quiser usar um disco de dados existente na unidade D, verifique se que você carregou também o VHD para a conta de Armazenamento. Para obter instruções, consulte as etapas 3 e 4 em [Criar e carregar um VHD do Windows Server no Azure][VHD].

> [AZURE.WARNING]Se você redimensionar ou "Parar (desalocar)" uma máquina virtual, isso pode disparar o posicionamento da máquina virtual para um novo hipervisor. Um evento de manutenção planejada ou não planejada também pode disparar esse posicionamento. Nesse cenário, o disco temporário será reatribuído à primeira letra da unidade disponível. Se você tiver um aplicativo que requer especificamente a unidade "D", certifique-se de que, depois de mover o arquivo de paginação, você atribui um novo disco persistente e ele recebe a letra D. O Azure não aceitará a letra D de volta.

> [AZURE.WARNING]Se você redimensionar uma máquina virtual depois de mover explicitamente o arquivo de paginação, observe que você pode encontrar um erro na inicialização se o disco temporário da nova máquina virtual não for grande o suficiente para conter o arquivo de paginação do tamanho da VM original. Você também poderá encontrar este erro se o disco temporário não for definido para a próxima letra da unidade disponível, fazendo com que o Windows referencie uma letra da unidade inválida na configuração do arquivo de paginação enquanto o Azure cria a unidade temporária com a próxima letra da unidade disponível.

##Alterar a letra da unidade

1. Faça logon na máquina virtual. Para obter mais detalhes, consulte [Como fazer logon em uma máquina virtual executando o Windows Server][Logon].

2. Mova pagefile.sys da unidade D para outra unidade.

3. Reinicie a máquina virtual.

4. Faça logon novamente e altere a letra da unidade de D para E.

5. No [portal do Azure](http://manage.windowsazure.com), anexe um disco de dados existente ou esvazie o disco de dados.

6.	Faça logon na máquina virtual novamente, inicialize o disco e atribua D como a letra da unidade do disco que você acabou de anexar.

7.	Verifique se E está mapeada para o disco temporário.

8.	Mova pagefile.sys da outra unidade para a unidade E.

9.	Reinicie a máquina virtual.



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

<!---HONumber=Oct15_HO1-->