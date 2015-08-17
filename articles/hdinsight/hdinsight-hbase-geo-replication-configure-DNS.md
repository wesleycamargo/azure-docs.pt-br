<properties 
   pageTitle="Configurar o DNS entre duas redes virtuais do Azure | Microsoft Azure" 
   description="Saiba como configurar conexões VPN e resolução de nome de domínio entre duas redes virtuais e como configurar a replicação geográfica do HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/08/2015"
   ms.author="jgao"/>

# Configurar o DNS entre duas redes virtuais do Azure

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 


Saiba como adicionar e configurar servidores DNS para redes virtuais do Azure para lidar com a resolução de nomes dentro e entre as redes virtuais.

Este tutorial é a segunda parte da [série][hdinsight-hbase-geo-replication] sobre a criação de replicação geográfica do HBase:

- [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]
- Configurar o DNS para as redes virtuais (esse tutorial)
- [Configurar a replicação geográfica HBase][hdinsight-hbase-geo-replication]


O diagrama a seguir ilustra as duas redes virtuais criadas no [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]\:

![Diagrama de rede virtual de replicação de HBase do HDInsight][img-vnet-diagram]

##Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>

- **Duas redes virtuais Azure com conectividade VPN**. Para obter instruções, consulte [Configurar uma conexão VPN entre duas redes virtuais do Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE]Os nomes de serviço do Azure e a máquina virtual devem ser exclusivos. O nome usado neste tutorial é Contoso-[Azure Service/VM name]-[EU/US]. Por exemplo, Contoso-VNet-EU é a rede virtual do Azure no datacenter do Norte da Europa; Contoso-DNS-US é a VM do servidor DNS no datacenter no Leste dos EUA. Você deve criar seus próprios nomes.
 
 
##Criar máquinas virtuais do Azure para serem usadas como servidores DNS

**Para criar uma máquina virtual no Contoso-VNet-EU, chamada Contoso-DNS-EU**

1.	Clique em **NOVO**, **COMPUTAÇÃO**, **MÁQUINA VIRTUAL**, **DA GALERIA**.
2.	Escolha **Windows Server 2012 R2 Datacenter**.
3.	Digite:
	- **NOME DA MÁQUINA VIRTUAL**: Contoso-DNS-EU
	- **NOVO NOME DE USUÁRIO**: 
	- **NOVA SENHA**: 
4.	Digite:
	- **SERVIÇO DE NUVEM**: criar um novo serviço de nuvem
	- **REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL**: (selecione Contoso-VNet-EU)
	- **SUB-REDES DE REDE VIRTUAL**: Sub-rede-1
	- **CONTA DE ARMAZENAMENTO**: usar uma conta de armazenamento gerada automaticamente
	
		O nome do serviço de nuvem será igual ao nome da máquina virtual. Nesse caso, é Contoso-DNS-EU. Para máquinas virtuais subsequentes, posso escolher usar o mesmo serviço de nuvem. Todas as máquinas virtuais no mesmo serviço de nuvem compartilham a mesma rede virtual e sufixo do domínio.

		A conta de armazenamento é usada para armazenar o arquivo de imagem de máquina virtual. 
	- **PONTOS DE EXTREMIDADE**: (Role para baixo e selecione **DNS**) 

Depois que a máquina virtual é criada, descubra o IP interno e o IP externo.

1.	Clique no nome da máquina virtual: **Contoso-DNS-EU**.
2.	Clique em **Painel**.
3.	Anote:
	- ENDEREÇO IP VIRTUAL PÚBLICO
	- ENDEREÇO IP INTERNO


**Para criar uma máquina virtual no Contoso-VNet-US, chamada Contoso-DNS-US**

- Repita o mesmo procedimento para criar uma máquina virtual com os seguintes valores:
	- NOME DA MÁQUINA VIRTUAL: Contoso-DNS-US
	- REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL: selecione Contoso-VNET-US
	- SUB-REDES DE REDE VIRTUAL: Sub-rede-1
	- CONTA DE ARMAZENAMENTO: usar uma conta de armazenamento gerada automaticamente
	- PONTOS DE EXTREMIDADE: (selecione DNS)

##Conjunto de endereços IP estáticos para as duas máquinas virtuais

Servidores DNS requerem endereços IP estáticos. Essa etapa não pode ser feita no Portal do Azure. Você usará o PowerShell do Azure.

**Para configurar o endereço IP estático para as duas máquinas virtuais**

1. Abra o ISE do Windows PowerShell.
2. Execute o cmdlet a seguir.  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName é o nome do serviço de nuvem. Como o servidor DNS é a primeira máquina virtual do serviço de nuvem, o nome do serviço de nuvem é o mesmo nome da máquina virtual.

	Talvez seja necessário atualizar o ServiceName e o Nome para corresponder aos nomes que você possui.


##Adicione a função de Servidor DNS às duas máquinas virtuais

**Para adicionar a função de Servidor DNS para o Contoso-DNS-EU**

1.	No portal do Azure, clique em **Máquinas Virtuais** à esquerda. 
2.	Clique em **Contoso-DNS-EU**.
3.	Clique em **PAINEL** na parte superior.
4.	Clique em **CONECTAR** da parte inferior e siga as instruções para se conectar à máquina virtual via RDP.
2.	Na sessão RDP, clique no botão Windows no canto inferior esquerdo para abrir a tela Inicial.
3.	Clique no bloco **Gerenciador de Servidores**.
4.	Clique em **Adicionar funções e recursos**.
5.	Clique em **Próximo**.
6.	Selecione **Instalação baseada em função ou recurso** e clique em **Avançar**.
7.	Selecione sua máquina virtual DNS (ela já deve estar realçada) e, em seguida, clique em **Próximo**.
8.	Marque **Servidor DNS**.
9.	Clique em **Adicionar recursos** e, em seguida, clique em **Continuar**.
10.	Clique em **Próximo** três vezes e, em seguida, clique em **Instalar**. 

**Para adicionar a função de Servidor DNS para o Contoso-DNS-US**

- Repita as etapas para adicionar a função do DNS ao **Contoso-DNS-US**.

##Atribuir servidores DNS a redes virtuais

**Para registrar os dois servidores DNS**

1.	No portal do Azure, clique em **NOVO**, **SERVIÇOS DE REDE**, **REDE VIRTUAL**, **REGISTRAR SERVIDOR DNS**.
2.	Digite:
	- **NOME**: Contoso-DNS-EU
	- **ENDEREÇO IP DO SERVIDOR DNS**: 10.1.0.4 – o endereço IP deve corresponder ao endereço IP da máquina virtual do servidor DNS.
	 
3.	Repita o processo para registrar Contoso-DNS-US com as seguintes configurações:
	- **NOME**: Contoso-DNS-US
	- **ENDEREÇO IP DO SERVIDOR DNS**: 10.2.0.4

**Para atribuir os dois servidores DNS às duas redes virtuais**

1.	Clique em **Redes** no painel à esquerda no portal de gerenciamento.
2.	Clique em **Contoso-VNet-EU**.
3.	Clique em **CONFIGURAR**.
4.	Selecione **Contoso-DNS-EU** na seção **servidores dns**.
5.	Clique em **SALVAR** na parte inferior da página e clique em **Sim** para confirmar.
6.	Repita o processo para atribuir o servidor DNS **Contoso-DNS-US** à rede virtual **Contoso-VNet-US**.

Todas as máquinas virtuais que foram implantadas para as redes virtuais devem ser reinicializadas para atualizar a configuração do servidor DNS.

**Reinicializar as máquinas virtuais**

1. No portal do Azure, clique em **Máquinas Virtuais** à esquerda.
2. Clique em **Contoso-DNS-EU**.
3. Clique em **Painel** na parte superior.
4. Clique em **REINICIAR** na parte inferior.
5. Repita as mesmas etapas para reinicializar **Contoso-DNS-US**.


##Configure os encaminhadores condicionais DNS

O servidor DNS em cada rede virtual somente pode resolver nomes DNS dentro da rede virtual. Você precisa configurar um encaminhador condicional para apontar para o servidor DNS de ponto a ponto para resoluções de nome na rede virtual correspondente.

Para configurar o encaminhador condicional, você precisa saber os sufixos de domínio dos dois servidores DNS. Os sufixos DNS podem ser diferentes dependendo da configuração dos Serviços de Nuvem usados ao criar as máquinas virtuais. Para cada sufixo DNS usado na rede virtual, você deve adicionar um encaminhador condicional.

**Para localizar os sufixos de domínio dos dois servidores DNS**

1. RDP no **Contoso-DNS-EU**.
2. Abra o console do Windows PowerShell ou o prompt de comando.
3. Execute **ipconfig** e anote o **sufixo DNS específico da conexão**.
4. Não feche a sessão RDP, você ainda precisará dela. 
5. Repita as mesmas etapas para descobrir o **sufixo DNS específico da conexão** de **Contoso-DNS-US**.


**Para configurar os encaminhadores DNS**
 
1.	Na sessão RDP para **Contoso-DNS-EU**, clique na tecla Windows no canto inferior esquerdo.
2.	Clique em **Ferramentas administrativas**.
3.	Clique em **DNS**.
4.	No painel esquerdo, expanda **DSN**, **Contoso-DNS-EU**.
5.	Insira as seguintes informações:
	- **Domínio DNS**: insira o sufixo DNS do Contoso-DNS-US. Por exemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
	- **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é o endereço IP do Contoso-DNS-US.
6.	Pressione **ENTER** e, em seguida, clique em **OK**. Agora você poderá resolver o endereço IP do Contoso-DNS-US por meio do Contoso-DNS-EU.
7.	Repita as etapas para adicionar um encaminhador do DNS para o serviço DNS na máquina virtual Contoso-DNS-US com os seguintes valores:
	- **Domínio DNS**: insira o sufixo DNS do Contoso-DNS-EU. 
	- **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é o endereço IP do Contoso-DNS-EU.

##Testar a resolução de nomes entre as redes virtuais

Agora você pode testar a resolução de nomes de host entre as redes virtuais. Ping é bloqueada pelo firewall por padrão. Você pode usar nslookup para resolver as máquinas virtuais do servidor DNS (você deve usar FQDN) nas redes ponto a ponto.


##Próximas etapas

Neste tutorial, você aprendeu como configurar a resolução de nomes entre as redes virtuais com conexões VPN. Os outros dois artigos da série abrangem:

- [Configurar uma conexão VPN entre duas redes virtuais do Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurar a replicação geográfica HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: ../install-configure-powershell.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=August15_HO6-->