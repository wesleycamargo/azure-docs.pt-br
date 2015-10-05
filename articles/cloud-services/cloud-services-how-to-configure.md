<properties 
	pageTitle="Como configurar um serviço de nuvem | Microsoft Azure" 
	description="Saiba como configurar serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar acesso remoto às instâncias de função." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015"
	ms.author="adegeo"/>




# Como configurar serviços de nuvem

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no Portal de Gerenciamento do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

O Portal de Gerenciamento do Azure também permite que você [habilite a Conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure](cloud-services-role-enable-remote-desktop.md)

O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante as atualizações de configuração se você tiver, pelo menos, duas instâncias de função para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/).

## Alterar um serviço de nuvem

1. No [Portal do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**, no nome do serviço de nuvem e depois em **Configurar**.

    ![Página de configuração](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Na página **Configurar**, é possível configurar o monitoramento, atualizar as configurações da função e escolher o sistema operacional convidado e a família para instâncias de função.

2. Em **monitoramento**, defina o nível de monitoramento como Detalhado ou Mínimo e configure as cadeias de conexão de diagnóstico necessárias para o monitoramento detalhado.

3. Para as funções de serviço (agrupadas por função), você pode atualizar as seguintes configurações:
    
    >**Configurações** Modifique os valores das diversas especificadas nos elementos *ConfigurationSettings* do arquivo de configuração do serviço (.cscfg).
    >
    >**Certificados** Altere a impressão digital do certificado usado na criptografia SSL para uma função. Para alterar um certificado, você deve primeiro carregar o novo certificado (na página **Certificados**). Em seguida, atualize a impressão digital na cadeia de caracteres do certificado exibida nas configurações da função.

4. Em **sistema operacional**, você pode alterar a família ou a versão do sistema operacional para instâncias de função ou escolher **Automático** para habilitar as atualizações automáticas da versão atual do sistema operacional. As configurações do sistema operacional aplicam-se às funções Web e às funções de trabalho, mas não afetam as máquinas virtuais.

    Durante a implantação, a versão mais recente do sistema operacional é instalada em todas as instâncias de função e os sistemas operacionais são atualizados automaticamente por padrão.
    
    Se precisar que o Serviço de Nuvem seja executado em outra versão do sistema operacional devido a requisitos de compatibilidade com seu código, você poderá escolher uma família e versão do sistema operacional. Ao escolher uma versão específica do sistema operacional, as atualizações automáticas do sistema operacional são suspensas para o serviço de nuvem. Você precisará garantir que os sistemas operacionais recebam atualizações.
    
    Se você resolver todos os problemas de compatibilidade de seus aplicativos com a versão mais recente do sistema operacional, poderá habilitar as atualizações automáticas do sistema operacional definindo a versão do sistema operacional como **Automático**.
    
    ![Definições do sistema operacional](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para salvar as configurações e enviá-las por push para as instâncias de função, clique em **Salvar**. (Clique em **Descartar** para cancelar as alterações.) **Salvar** e **Descartar** são adicionados à barra de comandos após a alteração de uma configuração.

## Atualizar um arquivo de configuração de serviço de nuvem

1. Baixe um arquivo de configuração do Serviço de Nuvem (.cscfg) com a configuração atual. Na página **Configurar** do Serviço de Nuvem, clique em **Baixar**. Em seguida, clique em **Salvar** ou em **Salvar como** para salvar o arquivo.

2. Após atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações da configuração:

    1. Na página **Configurar**, clique em **Carregar**.
    
        ![Carregamento da configuração](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. Em **Arquivo de configuração**, use **Procurar** para selecionar o arquivo .cscfg atualizado.
    
    3. Se seu Serviço de Nuvem contiver alguma função com apenas uma instância, marque a caixa de seleção **Aplicar configuração mesmo se uma ou mais funções contiverem uma única instância** para habilitar as atualizações de configuração para que funções continuem.
    
        A menos que você defina no mínimo duas instâncias de cada função, o Azure não poderá garantir ao menos 99,95 por cento de disponibilidade do seu Serviço de Nuvem durante as atualizações da configuração do serviço. Para obter mais informações, consulte [Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/).
    
    4. Clique em **OK** (marca de seleção).


## Próximas etapas

* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage.md).
* [Habilitar a conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure](cloud-services-role-enable-remote-desktop.md)
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate.md).

<!---HONumber=Sept15_HO4-->