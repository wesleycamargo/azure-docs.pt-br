---
title: Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf
description: Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990698"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf

O Azure Monitor permite coletar métricas personalizadas via telemetria do aplicativo, agente executado em seus recursos do Azure ou até mesmo sistemas de monitoramento de fora para dentro, e enviá-las diretamente ao Azure Monitor. Este artigo se concentra em fornecer instruções sobre como implantar o agente [InfluxData](https://www.influxdata.com/) Telegraf em uma VM do Linux no Azure e configurar o agente para publicar as métricas no Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agente InfluxData Telegraf 

O [Telegraf](https://docs.influxdata.com/telegraf/v1.7/) é um agente controlado por plug-in que permite a coleta de métricas de mais de 150 fontes diferentes. Dependendo de quais cargas de trabalho estão em execução em sua VM (por exemplo, MySQL, NGINX, Apache etc.), você pode configurar o agente para usar os plug-ins de entrada especializados para coletar métricas. Então, os plug-ins de saída permitem que o agente grave em destinos de sua escolha. O agente Telegraf integrou-se diretamente na API REST das métricas personalizadas do Azure Monitor e dá suporte a um "Plug-in de Saída do Azure Monitor". Isso permite que o agente colete métricas específicas da carga de trabalho em sua VM do Linux e envie-as como métricas personalizadas para o Azure Monitor. 

 ![Visão Geral do Agente Telegraf](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, implantamos uma VM do Linux executando o sistema operacional Ubuntu 16.04 LTS. A maioria dos sistemas operacionais Linux tem suporte para o agente Telegraf. Os pacotes Debian e RPM estão disponíveis junto com binários descompactados do Linux no portal de download do InfluxData. Confira o guia de instalação para obter instruções de instalação adicionais e opções do Telegraf. 

Faça logon no [Portal do Azure](https://portal.azure.com)

Para criar uma nova VM do Linux: 

1. Clique na opção  **Criar um recurso**  no painel de navegação à esquerda. 
1. Pesquise *Máquina Virtual*  
1. Selecione *Ubuntu 16.04 LTS* e clique em **Criar** 
1. Forneça um nome de VM como  *MyTelegrafVM*.  
1. Deixe o tipo de disco como **SSD**, depois forneça um **nome de usuário**, como  *azureuser*. 
1. Para  *Tipo de autenticação*, selecione  **Senha**, depois digite uma senha que você usará posteriormente para o SSH nessa VM. 
1. Escolha  **Criar novo grupo de recursos**, depois forneça um nome, como  *myResourceGroup*.  Escolha seu Local desejado, depois selecione  **OK**. 

     ![Criar uma VM do Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Você pode filtrar por Tipo de computação ou Tipo de disco, por exemplo. 

     ![Visão Geral do Agente Telegraf para o tamanho da Máquina Virtual](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Na  **página Configurações**, em  **Rede** > **Grupo de Segurança de Rede** > **Selecionar portas públicas de entrada**, selecione  *HTTP*  e  *SSH (22)*. Mantenha o restante dos padrões e selecione  **OK**. 

1. Na página de resumo, selecione Criar para iniciar a implantação da VM. 

1. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, a folha de resumo da VM abrirá automaticamente. 

1. Na folha da VM, navegue até a guia **Identidade** e verifique se sua VM tem uma identidade atribuída do sistema *Ativa*. 
 
![preenchimento](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Conectar-se à VM 

Crie uma conexão SSH com a VM. Selecione o botão Conectar na página de visão geral de sua VM. 

![preenchimento](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

Na página Conectar máquina virtual, mantenha as opções padrão para conectar com o nome do DNS na porta 22. Em Logon, usando a conta local da VM, um comando de conexão é mostrado. Clique no botão para copiar o comando. O exemplo a seguir mostra a aparência do comando de conexão SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Cole o comando de conexão SSH em um shell, como o Azure Cloud Shell, Bash no Ubuntu, no Windows ou use um cliente SSH de sua preferência para criar a conexão. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar o Telegraf 

Para instalar o pacote Telegraf Debian na VM, execute os seguintes comandos de sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
O arquivo de configuração do Telegraf define as operações. Por padrão, um arquivo de configuração de exemplo é instalado no caminho “/etc/telegraf/telegraf.conf”. O arquivo de configuração de exemplo lista todos os plug-ins de entrada e saída possíveis. No entanto, vamos criar um arquivo de configuração personalizado e fazer com que o agente use-o executando os comandos a seguir 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> As opções acima só permitem dois plug-ins de entrada “cpu” e “mem”; fique à vontade para adicionar mais plug-ins de entrada (Docker, MySQL, NGINX etc.), dependendo da carga de trabalho em execução no seu computador. Uma lista completa de plug-ins de entrada pode ser encontrada aqui. 

Por fim, para que o agente comece a usar a nova configuração, nós o forçamos a parar e iniciar executando os comandos a seguir 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora o agente coletará as métricas de cada um dos plug-ins de entrada especificados e os emitirá para o Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Plotar as métricas do Telegraf no portal do Azure 

1. Abra o [portal do Azure](https://portal.azure.com) 

1. Navegue até nova guia Monitor, depois selecione  **Métricas**.  
     ![preenchimento](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Selecione sua VM no seletor de recursos

     ![preenchimento](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Selecione o namespace *Telegraf/CPU*, depois selecione a métrica *usage_system*. É possível optar por filtrar conforme as dimensões nessa métrica ou dividi-las.  

     ![preenchimento](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

As etapas acima fornecem informações sobre como configurar o agente Telegraf para coletar as métricas de alguns plug-ins de entrada básicos. O agente Telegraf tem suporte para mais de 150 plug-ins de entrada, com algumas opções de configuração de suporte adicionais. O InfluxData publicou uma [lista de plug-ins com suporte](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) e instruções sobre [como configurá-los](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Além disso, essas etapas permitiram que você usasse o agente Telegraf para emitir métricas sobre a VM na qual o agente está implantado. O agente Telegraf também pode ser usado como um coletor e encaminhador de métricas para outros recursos. Para saber como configurar o agente para emitir métricas para outros recursos do Azure, consulte [Saída de Métrica Personalizada do Azure Monitor para Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar recursos 

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione Excluir, em seguida, confirme o nome do grupo de recursos a excluir. 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


