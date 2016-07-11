
<properties
    pageTitle="Validar o VNET do Azure para utilização com o Azure RemoteApp | Microsoft Azure"
    description="Saiba como verificar se o VNET do Azure está pronto para uso com o RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/12/2016"
    ms.author="elizapo" />



# Validar o VNET do Azure para usar com o RemoteApp do Azure

Antes de usar um VNET do Azure com o RemoteApp do Azure, é recomendado validar o VNET. Isso ajuda a evitar problemas com conectividade.

Para validar seu VNET do Azure, faça o seguinte:

1. Crie uma máquina virtual do Azure dentro da sub-rede do VNET do Azure que deseja usar com o RemoteApp do Azure.

2. Conecte-se à VM usando a opção **Conectar** no portal de gerenciamento.
3. Adicione a máquina virtual no mesmo domínio que você deseja usar com o RemoteApp do Azure. Se estiver criando uma coleção híbrida que se conecta à sua rede local, ingresse a máquina virtual no domínio local.

Se essa ação for bem-sucedida, o VNET do Azure está pronto para uso com o RemoteApp.

Para obter mais informações sobre o fluxo de trabalho de coleção híbrido de ponta a ponta, consulte os seguintes artigos:

- [Como planejar sua rede virtual para o RemoteApp do Azure](remoteapp-planvnet.md)
- [Criar uma coleção híbrida](remoteapp-create-hybrid-deployment.md)
- [Implantar a coleção do RemoteApp do Azure à sua Rede Virtual do Azure (com suporte para Rota Expressa)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

<!---HONumber=AcomDC_0629_2016-->