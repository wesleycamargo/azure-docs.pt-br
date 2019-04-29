---
title: Controle de instâncias grandes do HANA do Azure por meio do portal do Azure | Microsoft Docs
description: Descreve a maneira como você pode identificar e interagir com instâncias grandes do HANA do Azure por meio do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482080"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controle do HANA em Instâncias Grandes do Azure por meio do portal do Azure
Este documento aborda a maneira como [instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) são apresentados na [portal do Azure](https://portal.azure.com) e as atividades podem ser realizadas por meio do portal do Azure com as unidades de instância grande do HANA que são implantados para você. Visibilidade do HANA em instâncias grandes no portal do Azure é fornecida por meio de um provedor de recursos do Azure para instâncias grandes HANA, que está atualmente em visualização pública

## <a name="register-hana-large-instance-resource-provider"></a>Registrar provedor de recursos de instância grande do HANA
Normalmente, sua assinatura do Azure que você estava usando para implantações de instância grande do HANA está registrada para o provedor de recursos de instância grande do HANA. No entanto, se você não conseguir ver que você implantou unidades de instância grande do HANA, você deverá registrar o provedor de recursos em sua assinatura do Azure. Há duas maneiras de registrar o provedor de recursos de instância grande do HANA

### <a name="register-through-cli-interface"></a>Registrar por meio da interface CLI
Você precisa estar conectado a sua assinatura do Azure, usada para a implantação de instância grande do HANA por meio da interface de CLI do Azure. Você pode (re) se registrar o provedor de instância grande do HANA com este comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Para obter mais informações, consulte o artigo [tipos e provedores de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registre-se por meio do portal do Azure
Você pode (re) se registrar o provedor de recursos do HANA grande instância por meio do portal do Azure. Você precisa listar sua assinatura no portal do Azure e clique duas vezes em que a assinatura, que foi usada para implantar suas unidades de instância grande do HANA. Um estão na página de visão geral da sua assinatura, selecione "Provedores de recursos", conforme mostrado abaixo e digite "HANA" na janela de pesquisa. 

![Registrar HLI RP por meio do portal do Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na captura de tela mostrada, o provedor de recursos já foi registrado. No caso do provedor de recursos ainda não está registrado, pressione "registrar novamente" ou "Registrar".

Para obter mais informações, consulte o artigo [tipos e provedores de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exibição de unidades de instância grande do HANA no portal do Azure
Ao enviar uma solicitação de implantação de instância grande do HANA, você será solicitado para especificar a assinatura do Azure que você se conectar a instâncias grandes HANA também. É recomendável, para usar a mesma assinatura que você está usando para implantar a camada de aplicativo SAP que funciona com as unidades de instância grande do HANA.
Como seu primeiro a instâncias grandes HANA estão sendo implantadas, um novo [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) é criado na assinatura do Azure que você enviou na solicitação de implantação para suas instâncias grandes do HANA.  Novo grupo de recursos listará todas as suas unidades de instância grande do HANA que você tiver implantado na assinatura específica.

Para localizar o novo grupo de recursos do Azure, você lista o grupo de recursos em sua assinatura navegando por meio do painel de navegação esquerdo do portal do Azure

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, você está obtendo listado, talvez você precise filtrar a assinatura que você costumava ter instâncias grandes do HANA implantadas

![Filtrar grupos de recursos no portal do Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Após a filtragem para a assinatura correta, você ainda pode ter uma longa lista de grupos de recursos. Procure uma com uma pós-correção de **- Txxx** onde "xxx" é três dígitos, como **-T050**. 

Uma vez que você localizou o grupo de recursos, liste os detalhes dele. A lista que você recebeu pode ser semelhante:

![Lista HLI no portal do Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas são que representa uma única unidade de instância grande do HANA que foi implantada em sua assinatura. Nesse caso, você examinar oito unidades diferentes de instância grande do HANA, que foram implantadas em sua assinatura.

Se você tiver implantado vários locatários de instância grande do HANA sob a mesma assinatura do Azure, você encontrará vários grupos de recursos do Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Examine os atributos da única unidade HLI
Na lista de unidades de instância grande do HANA, você pode clicar em uma única unidade e obter os detalhes da única unidade de instância grande do HANA. 

Na tela de visão geral, você está obtendo uma apresentação da unidade, que se parece com:

![Mostra uma visão geral de uma unidade HLI](./media/hana-li-portal/portal-show-overview.png)

Examinar os diferentes atributos mostrados, esses atributos são dificilmente diferentes atributos de VM do Azure. No cabeçalho do lado esquerdo, ele mostra o grupo de recursos, região do Azure, nome da assinatura e ID, bem como algumas marcas que você adicionou. Por padrão, as unidades de instância grande do HANA não têm nenhuma marca atribuída. No lado direito do cabeçalho, o nome da unidade está listado é atribuído quando a implantação foi concluída. O sistema operacional é mostrado, bem como o endereço IP. Como com a unidade de instância grande do HANA de tipo de VMs com o número de CPU threads e memória também é exibido. Obter mais detalhes sobre as diferentes unidades de instância grande do HANA, são mostrados aqui:

- [SKUs disponíveis para HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Arquitetura de armazenamento do SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Um campo adicional na coluna à direita do cabeçalho informa sobre o estado de energia da unidade de instância grande do HANA.

> [!NOTE]
> O estado de energia descreve se a unidade de hardware está ativada ou desativado. Ele não fornecerá informações sobre o sistema operacional, estando em execução e em execução. Como reiniciar uma unidade de instância grande do HANA, você experimentará um tempo de pequenas em que o estado da unidade é alterado para **iniciando** para mover para o estado do **iniciado**. Ter ficado no estado de **iniciado** significa que o sistema operacional está sendo inicializado ou se o sistema operacional foi iniciado-se completamente. Como resultado, após a reinicialização da unidade, você não pode esperar fazer logon imediatamente para a unidade assim que o estado muda para **iniciado**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Verificar as atividades de uma única unidade de instância grande do HANA 
Além de apresentar uma visão geral das unidades de instância grande do HANA, você pode verificar as atividades da unidade específica. Um log de atividades pode ser semelhante:

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registradas são reinicializações de uma unidade. Os dados listados incluem o status da atividade, o carimbo de hora que a atividade foi disparada, a ID da assinatura de que a atividade foi acionada e o usuário do Azure que disparou a atividade. 

Outra atividade que está sendo gravada são alterações para a unidade nos metadados do Azure. Além de reinicialização iniciada, você pode ver a atividade de **HANAInstances gravar**. Esse tipo de atividade executa sem alterações na unidade de instância grande do HANA em si, mas é documentar as alterações nos dados de meta da unidade no Azure. No caso de listados, nós adicionados e excluídos de uma marca (consulte a próxima seção).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicionar e excluir uma marca do Azure para uma unidade de instância grande do HANA
Outra possibilidade de você ter é adicionar um [marca](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a uma unidade de instância grande do HANA. A maneira como as marcas são obtendo atribuídas não será diferente de atribuição de marcas para máquinas virtuais. Como com VMs as marcas existem nos metadados do Azure e, para instâncias grandes do HANA, tem as mesmas restrições que as marcas para as VMs.

Excluir marcas funciona da mesma forma como ocorre com máquinas virtuais. As duas atividades, aplicando e uma marca de exclusão serão listadas no log de atividades de determinada unidade de instância grande do HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade de instância grande do HANA
A seção **propriedades** inclui informações importantes que você obtém quando as instâncias são enviadas a você. É uma seção em que você obtém todas as informações que você poderia exigir suporte casos ou que você precisa ao configurar a configuração de instantâneo de armazenamento. Como tal, esta seção é uma coleção de dados em torno de sua instância, a conectividade da instância do Azure e o back-end de armazenamento. A parte superior da seção se parece com:


![parte superior das propriedades HLI no portal do Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiro alguns itens de dados, você viu na tela de visão geral já. Mas uma parte importante dos dados é a ID de circuito de ExpressRoute, que você obteve como as primeiras unidades implantadas foram entregues. Em alguns casos de suporte, você pode obter solicitado para os dados. Uma entrada de dados importantes é mostrada na parte inferior da captura de tela. Os dados exibidos são o endereço IP da cabeça de armazenamento NFS que isola seu armazenamento para seus **locatário** na pilha de instância grande do HANA. Esse endereço IP também é necessário quando você edita a [backups de instantâneo de arquivo de configuração para o armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Conforme você rola para baixo no painel de propriedade, você obtém dados adicionais, como uma ID de recurso exclusivo para sua unidade de instância grande do HANA ou a ID da assinatura que foi atribuída à implantação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reiniciar uma unidade de instância grande do HANA por meio do portal do Azure
Iniciando uma reinicialização do sistema operacional Linux, havia várias situações em que o sistema operacional não pôde concluir uma reinicialização com êxito. Para forçar uma reinicialização, era necessário abrir uma solicitação de serviço para fazer com que o Microsoft operations execute uma reinicialização de energia da unidade de instância grande do HANA. A funcionalidade de uma reinicialização de energia de uma unidade de instância grande do HANA agora está integrada ao portal do Azure. Como a parte da visão geral da unidade de instância grande do HANA, você verá o botão de reinicialização na parte superior da seção de dados

![Etapa 1 # de reinicialização no portal do Azure](./media/hana-li-portal/portal-restart-first-step.png)

Conforme você está nos pressionando o botão de reinicialização, será perguntado se você realmente deseja reiniciar a unidade. Como confirmar pressionando o botão "Sim", a unidade será reiniciado.

> [!NOTE]
> O processo de reinicialização, você experimentará um tempo de pequenas em que o estado da unidade é alterado para **iniciando** para mover para o estado do **iniciado**. Ter ficado no estado de **iniciado** significa que o sistema operacional está sendo inicializado ou se o sistema operacional foi iniciado-se completamente. Como resultado, após a reinicialização da unidade, você não pode esperar fazer logon imediatamente para a unidade assim que o estado muda para **iniciado**.

> [!IMPORTANT]
> Depende da quantidade de memória em sua unidade de instância grande do HANA, uma reinicialização e a reinicialização de hardware e o sistema operacional podem levar até uma hora


## <a name="open-a-support-request-for-hana-large-instances"></a>Abra uma solicitação de suporte para instâncias grandes HANA
Sem a exibição do portal do Azure de unidades de instância grande do HANA, você pode criar solicitações de suporte especificamente para uma unidade grande do HANA instância também. Como você, siga o link **nova solicitação de suporte** 

![Iniciar a etapa de solicitação de serviço #1 no portal do Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de instâncias grandes do HANA SAP listados na próxima tela, talvez você precise selecionar ' todos os serviços "conforme mostrado abaixo

![Selecione todos os serviços no portal do Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, você pode encontrar o serviço **SAP HANA em instâncias grandes**. Como você escolher esse serviço, você pode selecionar os tipos de problema específico, conforme mostrado:


![Selecione a classe problem no portal do Azure](./media/hana-li-portal/portal-select-problem-class.png)

Em cada um dos tipos de problema diferente, você terá uma seleção de subtipos de problema, que você precisará selecionar para caracterizar o seu problema ainda mais. Depois de selecionar o subtipo, você agora pode nomear o assunto. Depois que você concluir o processo de seleção, você pode mover para a próxima etapa da criação. No **soluções** seção, você está apontada para a documentação em torno do HANA em instâncias grandes, que pode dar a um ponteiro para uma solução do problema. Se você não encontrar uma solução para o seu problema na documentação do sugerido, vá para a próxima etapa. Na próxima etapa, você pretende solicitado se o problema é com máquinas virtuais ou com as unidades de instância grande do HANA. Essas informações ajudam a direcionar a solicitação de suporte para os especialistas corretos. 

![Detalhes do caso de suporte no portal do Azure](./media/hana-li-portal/portal-support-request-details.png)

Como você respondeu às perguntas e forneceu detalhes adicionais, você pode acessar a próxima etapa para revisar a solicitação de suporte e o envio-lo.

## <a name="next-steps"></a>Próximas etapas

- [Como monitorar o SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoramento e solução de problemas no lado do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

