---
title: Solucionar problemas das Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos problemas conhecidos no serviço de Migrações para Azure e dicas de solução de erros comuns.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: dff3c96cf3ac8eea7c1160ee1834cc70390c0333
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652630"
---
# <a name="troubleshoot-azure-migrate"></a>Solucionar problemas das Migrações para Azure

## <a name="troubleshoot-common-errors"></a>Solução de problemas comuns

As [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure. Use este artigo para solucionar problemas ao implementar e usar as Migrações para Azure.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Estou usando o OVA que descobre continuamente meu ambiente local, mas as VMs excluídas em meu ambiente local ainda estão sendo mostradas no portal.

O dispositivo de descoberta contínua apenas coleta dados de desempenho continuamente, ele não detectar qualquer alteração de configuração no ambiente local (ou seja, adição de VM, exclusão, a adição de disco etc.). Se houver uma alteração de configuração no ambiente local, você poderá fazer o seguinte para refletir as alterações no portal:

- Adição de itens (VMs, discos, núcleos, etc.): Para refletir essas alterações no portal do Azure, você pode interromper a descoberta do dispositivo e iniciá-la novamente. Isso garantirá que as alterações sejam atualizadas no projeto de Migrações para Azure.

   ![Interromper descoberta](./media/troubleshooting-general/stop-discovery.png)

- Exclusão de VMs: Devido à maneira como o dispositivo é projetado, a exclusão de VMs não é refletida, mesmo se você parar e iniciar a descoberta. Isso ocorre porque os dados das descobertas subsequentes são anexados a descobertas antigas e não substituídos. Nesse caso, você pode simplesmente ignorar a VM no portal, removendo-a do grupo e recalculando a avaliação.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Exclusão de projetos de Migrações para Azure e workspace associado do Log Analytics

Quando você exclui um projeto de Migrações para Azure, o projeto de migração é excluído juntamente com todos os grupos e avaliações. No entanto, se você anexou um workspace do Log Analytics ao projeto, ele não exclui automaticamente o workspace do Log Analytics. Isso ocorre porque o mesmo workspace do Log Analytics pode ser usado para vários casos de uso. Se você desejar excluir o workspace do Log Analytics, precisará fazer isso manualmente.

1. Navegue até o workspace do Log Analytics associado ao projeto.
    a. Se você ainda não excluiu o projeto de migração, poderá encontrar o link para o workspace na página de visão geral do projeto, na seção Essenciais.

   ![Workspace do LA](./media/troubleshooting-general/LA-workspace.png)

   b. Se você já excluiu o projeto de migração, clique em **Grupos de recursos** no painel à esquerda no portal do Azure e acesse o grupo de recursos no qual o workspace foi criado; em seguida, navegue até ele.
2. Siga as instruções [neste artigo](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para excluir o workspace.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Falha na criação do projeto de migração com o erro *As solicitações devem conter cabeçalhos de identidade do usuário*

Esse problema pode acontecer para usuários que não têm acesso ao locatário do Azure AD (Azure Active Directory) da organização. Quando um usuário é adicionado a um locatário do Azure AD pela primeira vez, ele recebe um convite por email para ingressar no locatário. Os usuários precisam ir até o email e aceitar o convite para serem adicionados com êxito ao locatário. Se não for possível ver o email, entre em contato com um usuário que já tenha acesso ao locatário e solicite que ele envie novamente o convite novamente para você usando as etapas especificadas [aqui](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Depois que o email de convite for recebido, você precisará abri-lo e clicar no link contido nele para aceitar o convite. Depois de fazer isso, você precisará sair do portal do Azure e entrar novamente, atualizar o navegador não funcionará. Em seguida, você pode tentar criar o projeto de migração.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Não consigo exportar o relatório de avaliação

Se você não conseguir exportar o relatório de avaliação do portal, tente usar a API REST para obter uma URL de download para o relatório de avaliação abaixo.

1. Instale *armclient* em seu computador (se você já não tiver instalado):

    a. Em uma janela de Prompt de Comando do administrador, execute o seguinte comando: ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

   b. Em uma janela do Windows PowerShell do administrador, execute o seguinte comando: ```choco install armclient```

2. Obter a URL de download para o relatório de avaliação usando a API REST de Migrações para Azure

    a.    Em uma janela do Windows PowerShell do administrador, execute o seguinte comando: ```armclient login```

        This opens the Azure login pop-up where you need to sign in to Azure.

   b.    Na janela do PowerShell, execute o seguinte comando para obter a URL de download para o relatório de avaliação (substitua os parâmetros URI pelos valores apropriados, exemplo de solicitação de API abaixo)

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

      Exemplo de solicitação e de saída:

      ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
   esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
   018_12_16_21/downloadUrl?api-version=2018-02-02
   {
   "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
   "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Copie a URL da resposta e abra-a em um navegador para baixar o relatório de avaliação.

4. Depois que o relatório for baixado, use o Excel para navegar até a pasta baixada e abra o arquivo no Excel para exibi-lo.

### <a name="performance-data-for-cpu-memory-and-disks-is-showing-up-as-zeroes"></a>Dados de desempenho para CPU, memória e discos está aparecendo como zeros

As migrações para Azure perfis continuamente o ambiente local para coletar dados de desempenho das VMs locais. Se você acabou de iniciar a descoberta do seu ambiente, você precisará aguardar pelo menos um dia para a coleta de dados de desempenho a ser feito. Se uma avaliação é criada sem esperar por um dia, as métricas de desempenho mostrará como zeros. Depois de aguardar um dia, você pode criar uma nova avaliação ou atualizar a avaliação existente usando a opção 'Recalculate' no relatório de avaliação.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Especifiquei uma geografia do Azure, durante a criação de um projeto de migração, como descobrir a região do Azure exata em que os metadados descobertos serão armazenados?

Você pode acessar a seção **Essentials** na página **Visão geral** do projeto para identificar a localização exata em que os metadados são armazenados. A localização é selecionada aleatoriamente na geografia pelas Migrações para Azure e você não pode modificá-la. Se você quiser criar um projeto em apenas uma região específica, use as APIs REST para criar o projeto de migração e passar a região desejada.

   ![Localização do projeto](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Problemas no coletor

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>A implantação do Coletor de Migrações para Azure falhou com o erro: O arquivo de manifesto fornecido é inválido: Entrada inválida de manifesto de OVF.

1. Verifique se o arquivo OVA do Coletor de Migrações para Azure será baixado corretamente verificando seu valor de hash. Consulte o [artigo](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) para verificar o valor de hash. Se o valor de hash não corresponde, baixe o arquivo OVA novamente e repita a implantação.
2. Se ele ainda falhar e se você estiver usando o VMware vSphere Client para implantar o OVF, tente implantá-lo por meio do vSphere Web Client. Se ele ainda falhar, tente usar outro navegador da Web.
3. Se você estiver usando o cliente Web do vSphere e tentar implantá-lo no vCenter Server 6.5 ou 6.7, tente implantar o arquivo OVA diretamente no host ESXi, seguindo as etapas a seguir:
   - Conecte-se ao host ESXi diretamente (em vez do vCenter Server) usando o cliente Web (https://<*endereço IP do host*>/ui)
   - Acesse Página Inicial > Inventário
   - Clique em Arquivo > Implantar modelo OVF > navegue até o arquivo OVA e conclua a implantação
4. Se a implantação ainda falhar, entre em contato com o suporte do Migrações para Azure.

### <a name="unable-to-select-the-azure-cloud-in-the-appliance-fails-with-error-azure-cloud-selection-failed"></a>Não é possível selecionar o Azure na nuvem no dispositivo, falha com o erro "Falha na seleção de nuvem do Azure"

Esse é um problema conhecido e uma correção está disponível para o problema. Baixe o [mais recente atualização bits](https://docs.microsoft.com/azure/migrate/concepts-collector-upgrade#continuous-discovery-upgrade-versions) para o dispositivo e o dispositivo para aplicar a correção de atualização.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>O coletor não é capaz de se conectar à Internet

Isso pode acontecer quando a máquina que você está usando estiver atrás de um proxy. Certifique-se de fornecer as credenciais de autorização se precisar de um proxy.
Se você estiver usando qualquer proxy firewall baseado em URL para controlar a conectividade de saída, certifique-se de permitir estes URLs exigidos:

**URL** | **Finalidade**  
--- | ---
*. portal.azure.com | É necessário verificar a conectividade com o serviço do Azure e validar problemas sincronização de hora.
*. oneget.org | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI.

**O coletor não é capaz de se conectar à Internet devido a uma falha de validação do certificado**

Isso pode acontecer se você estiver usando um proxy de interceptação para se conectar à Internet e não tiver importado o certificado de proxy para a VM do coletor. Você pode importar o certificado de proxy usando as etapas detalhadas [aqui](https://docs.microsoft.com/azure/migrate/concepts-collector).

**O coletor não consegue se conectar ao projeto usando a ID e a chave do projeto copiados do portal.**

Confira se você copiou e colou as informações corretas. Para solucionar o problema, instale o Microsoft Monitoring Agent (MMA) e verifique se o MMA pode se conectar ao projeto da seguinte maneira:

1. Na VM do coletor, baixe o [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Para iniciar a instalação, clique duas vezes no arquivo baixado.
3. Na instalação, na página **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
4. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
5. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
6. Clique em **Adicionar** para adicionar um workspace do Log Analytics. Cole a ID do projeto e a chave que você copiou. Em seguida, clique em **Próximo**.
7. Verifique se o agente pode se conectar ao projeto. Se não for possível, verifique as configurações. Se o agente pode se conectar, mas não o coletor, contate o suporte.


### <a name="error-802-date-and-time-synchronization-error"></a>Erro 802: erro de sincronização de data e hora

O relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute w32tm /tz.
3. Para sincronizar a hora, execute w32tm /resync.

### <a name="vmware-powercli-installation-failed"></a>Falha na instalação do PowerCLI do VMware

O Coletor de Migrações para Azure baixa o PowerCLI e o instala no dispositivo. A falha na instalação do PowerCLI pode ser devido a pontos de extremidade inacessíveis para o repositório do PowerCLI. Para solucionar o problema, tente instalar manualmente o PowerCLI na VM do coletor usando a etapa a seguir:

1. Abra o Windows PowerShell no modo de administrador
2. Vá para o diretório C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Execute o script InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Ocorreu um erro Interno de UnhandledException: System.IO.FileNotFoundException

Esse problema pode ocorrer devido a um problema com a instalação do VMware PowerCLI. Siga as etapas abaixo para resolver o problema:

1. Se você não estiver usando a versão mais recente do dispositivo coletor, [atualize o coletor para a versão mais recente](https://aka.ms/migrate/col/checkforupdates) e verifique se o problema for resolvido.
2. Se você já tiver a última versão do coletor, siga as etapas abaixo para fazer uma instalação limpa do PowerCLI:

    a. Feche o navegador da Web no dispositivo.

   b. Interrompa o serviço 'Coletor de Migrações para Azure' acessando o Windows Service Manager (Abra 'Executar' e digite services.msc para abrir o Windows Service Manager). Clique com o botão direito do mouse no serviço Coletor de Migrações para Azure e clique em Parar.

   c. Exclua todas as pastas que começam com 'VMware' nos seguintes locais: C:\Arquivos de Programas\WindowsPowerShell\Modules  
        C:\Arquivos de Programas (x86)\WindowsPowerShell\Modules

   d. Reinicie o serviço 'Coletor de Migrações para Azure' no Windows Service Manager (Abra 'Executar' e digite services.msc para abrir o Windows Service Manager). Clique com o botão direito do mouse no serviço Coletor de Migrações para Azure e clique em Iniciar.

   e. Clique duas vezes o atalho da área de trabalho 'Executar coletor' para iniciar o aplicativo de coletor. O aplicativo coletor deve baixar automaticamente e instale a versão necessária do PowerCLI.

3. Se as opções acima não resolver o problema, siga as etapas um a c acima e, em seguida, instale manualmente o PowerCLI no dispositivo usando as seguintes etapas:

    a. Limpar PowerCLI incompleta todos os arquivos de instalação, seguindo as etapas #a para #c na etapa #2 acima.

   b. Vá para Iniciar > Executar > PowerShell(x86) aberta do Windows no modo de administrador

   c. Execute o comando:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (tipo de 'A' quando ele solicita confirmação)

   d. Reinicie o serviço 'Coletor de Migrações para Azure' no Windows Service Manager (Abra 'Executar' e digite services.msc para abrir o Windows Service Manager). Clique com o botão direito do mouse no serviço Coletor de Migrações para Azure e clique em Iniciar.

   e. Clique duas vezes o atalho da área de trabalho 'Executar coletor' para iniciar o aplicativo de coletor. O aplicativo coletor deve baixar automaticamente e instale a versão necessária do PowerCLI.

4. Se não for possível baixar o módulo no dispositivo devido a problemas de firewall, baixe e instale o módulo em um computador que tenha acesso à internet usando as seguintes etapas:

     a. Limpar PowerCLI incompleta todos os arquivos de instalação, seguindo as etapas #a para #c na etapa #2 acima.

    b. Vá para Iniciar > Executar > PowerShell(x86) aberta do Windows no modo de administrador

    c. Execute o comando:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (tipo de 'A' quando ele solicita confirmação)

    d. Copie todos os módulos, começando com "VMware" de "C:\Program Files (x86) \WindowsPowerShell\Modules" no mesmo local na VM do coletor.

    e. Reinicie o serviço 'Coletor de Migrações para Azure' no Windows Service Manager (Abra 'Executar' e digite services.msc para abrir o Windows Service Manager). Clique com o botão direito do mouse no serviço Coletor de Migrações para Azure e clique em Iniciar.

    f. Clique duas vezes o atalho da área de trabalho 'Executar coletor' para iniciar o aplicativo de coletor. O aplicativo coletor deve baixar automaticamente e instale a versão necessária do PowerCLI.

### <a name="error-unabletoconnecttoserver"></a>Erro UnableToConnectToServer

Não é possível se conectar ao vCenter Server "Servername.com:9443" devido ao erro: "Não havia nenhum ponto de extremidade escutando em https://Servername.com:9443/sdk para aceitar a mensagem." Isso geralmente é causado por um endereço incorreto ou ação SOAP.

Verifique se está executando a versão mais recente do dispositivo do coletor, caso não esteja, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se o problema ainda ocorrer na versão mais recente, poderá ser porque o computador do coletor não consegue resolver o nome do vCenter Server especificado ou a porta especificada está errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

1. Tente executar ping para Servername.com do computador do coletor.
2. Se a etapa 1 falhar, tente conectar o servidor vCenter no endereço IP.
3. Identifique o número correto da porta para conectar o vCenter.
4. Finalmente, verifique se o servidor vCenter está em execução.

### <a name="antivirus-exclusions"></a>Exclusões de antivírus

Para proteger o dispositivo de Migrações para Azure, você precisará excluir as pastas a seguir no dispositivo da verificação antivírus:

- Pasta que contém os binários para o Serviço de Migrações para Azure. Exclua todas as subpastas.
  %ProgramFiles%\ProfilerService  
- Aplicativo Web de Migrações para Azure. Exclua todas as subpastas.
  %SystemDrive%\inetpub\wwwroot
- Cache local para arquivos de log e Banco de Dados. O serviço Migrações para Azure precisa de acesso RW a essa pasta.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Problemas de visualização de dependência

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Não consigo encontrar a funcionalidade de visualização de dependência para projetos do Azure Governamental.

As Migrações para Azure dependem do Mapa do Serviço para a funcionalidade de visualização de dependência e, uma vez que o Mapa do Serviço não está disponível no momento no Azure Governamental, essa funcionalidade não está disponível no Azure Governamental.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Instalei o MMA (Microsoft Monitoring Agent) e o Dependency Agent em minhas VMs locais, mas as dependências agora estão aparecendo no portal de Migrações para Azure.

Depois de instalar os agentes, as Migrações para Azure normalmente levam de 15 a 30 minutos para exibir as dependências no portal. Se você precisar esperar mais de 30 minutos, verifique se o agente MMA é capaz de comunicar-se com o workspace do OMS seguindo as etapas a seguir:

Para VM do Windows:
1. Acesse o **Painel de Controle** e inicie o **Microsoft Monitoring Agent**
2. Acesse a guia **Azure Log Analytics (OMS)** no pop-up de propriedades do MMA
3. Verifique se o **Status** do workspace está verde.
4. Se o status não estiver verde, tente remover o workspace e adicioná-lo novamente no MMA.
        ![Status do MMA](./media/troubleshooting-general/mma-status.png)

Para VM do Linux, verifique se os comandos de instalação do agente do MMA e do Dependency Agent foram executados com êxito.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quais são os sistemas operacionais com suporte no MMA?

A lista de sistemas de operacionais Windows com suporte no MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
A lista de sistemas operacionais Linux com suporte no MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quais são os sistemas operacionais com suporte no Dependency Agent?

A lista de sistemas de operacionais Windows com suporte no Dependency Agent está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
A lista de sistemas de operacionais Linux com suporte no Dependency Agent está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Não consigo visualizar as dependências nas Migrações para Azure por um período maior de uma hora?
As Migrações para Azure permitem que você visualize as dependências por um período de até uma hora. Embora as Migrações para Azure permitam que você volte para uma data específica no histórico de até um mês atrás, a duração máxima em que você pode visualizar as dependências é de até uma hora. Por exemplo, você pode usar a funcionalidade de duração de tempo no mapa de dependências para exibir as dependências de ontem, mas só pode exibi-las durante o período de uma hora. No entanto, você pode usar os logs do Azure Monitor [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) em um período mais longo.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Não consigo visualizar as dependências para grupos com mais de 10 VMs?
Você pode [visualizar as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de até 10 VMs. No caso de um grupo com mais de 10 VMs, é recomendado dividir o grupo em grupos menores e visualizar as dependências.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Eu instalei agentes e usei a visualização de dependência para criar grupos. Agora, após o failover, as máquinas mostram a ação de "Instalar o agente" em vez de "Exibir dependências"
* O failover pós-planejado ou não planejado, as máquinas locais são desativadas e as máquinas equivalentes são criadas no Azure. Essas máquinas adquirem um endereço MAC diferente. Elas podem adquirir um endereço IP diferente com base em se o usuário optou por reter o endereço IP local ou não. Se os endereços IP e MAC forem diferentes, as Migrações para Azure não associarão as máquinas locais com os dados de dependência do Mapa do Serviço e solicitarão que o usuário instale os agentes em vez de exibir as dependências.
* Após o failover de teste, as máquinas locais permanecem ativadas conforme o esperado. As máquinas equivalentes criadas no Azure adquirem um endereço MAC diferente e podem adquirir um endereço IP diferente. A menos que os blocos do usuário a saída do Azure Monitor registra o tráfego dessas máquinas, migrações para Azure não associar as máquinas locais com os dados de dependência do mapa do serviço e solicitarão que o usuário para instalar agentes em vez de exibir as dependências.

## <a name="troubleshoot-azure-readiness-issues"></a>Solucionar problemas de preparação para o Azure

**Problema** | **Correção**
--- | ---
Tipo de inicialização sem suporte | O Azure não oferece suporte a VMs com o tipo de inicialização EFI. É recomendável converter o tipo de inicialização em BIOS antes de executar uma migração. <br/><br/>Você pode usar o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) para fazer a migração de tais VMs conforme ele converter o tipo de inicialização da VM em BIOS durante a migração.
SO Windows com suporte condicional | O sistema operacional atingiu o fim da data de suporte e precisa de um Contrato de Suporte Personalizado (CSA) para [suporte no Azure](https://aka.ms/WSosstatement), considere o upgrade do sistema operacional antes de migrar para o Azure.
SO Windows sem suporte | O Azure suporta apenas as [versões de SO Windows selecionadas](https://aka.ms/WSosstatement), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas as [versões de SO Linux selecionadas](../virtual-machines/linux/endorsed-distros.md), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux não endossado | O computador pode ser inicializado no Azure, mas nenhum suporte de sistema operacional é fornecido pelo Azure, considere atualizar o sistema operacional para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como 'Outro' no vCenter Server. Por esse motivo, o Migrações para Azure não consegue identificar a prontidão do Azure da VM. Certifique-se de que o sistema operacional em execução na máquina seja [compatível](https://aka.ms/azureoslist) com o Azure antes de migrar a máquina.
Número de bit do sistema operacional sem suporte | VMs com sistemas operacionais de 32 bits podem ser inicializadas no Azure, mas é recomendável atualizar o sistema operacional da VM de 32 bits para 64 bits antes de migrar para o Azure.
Exige uma assinatura do Visual Studio. | Os computadores têm um sistema operacional cliente Windows em execução com suporte apenas na assinatura do Visual Studio.
A VM não foi encontrada para o desempenho de armazenamento necessário. | O desempenho do armazenamento (IOPS/taxa de transferência) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
A VM não foi encontrada para o desempenho de rede necessário. | O desempenho de rede (entrada/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede para a máquina.
A VM não foi encontrada no local especificado. | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados. | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Para cada disco anexado à VM, certifique-se de que o tamanho do disco seja < 4 TB, caso contrário, reduza o tamanho de disco antes de migrar para o Azure. Certifique-se de que o desempenho (IOPS/taxa de transferência) necessário para cada disco seja compatível com os [discos de máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) do Azure.   
Um ou mais adaptadores de rede inadequados. | Remova os adaptadores de rede não utilizados do computador antes da migração.
A contagem de disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | O Azure oferece suporte a discos com tamanho de até 4 TB. Reduza discos para menos de 4 TB antes da migração.
O disco não está disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
O disco não está disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo adequado de VM. Reduza a memória e o número de núcleos da máquina local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.


## <a name="collect-logs"></a>Coletar logs

**Como faço para coletar logs na VM do coletor?**

O registro em logs é habilitado por padrão. Os logs estão localizados da seguinte maneira:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Para coletar Rastreamento de Eventos para Windows, faça o seguinte:

1. Na VM do coletor, abra uma janela de comando do PowerShell.
2. Execute **Aplicativo Get-EventLog -LogName | export-csv eventlog.csv**.

**Como faço para coletar logs de tráfego da rede do portal?**

1. Abra o navegador e navegue e faça logon [no portal](https://portal.azure.com).
2. Pressione F12 para iniciar as Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação**.
3. Clique na guia **Rede** e inicie a captura do tráfego de rede:
   - No Chrome, selecione **Preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se não for exibido, clique no círculo preto para iniciar
   - No Microsoft Edge/IE, a gravação deve ser iniciada automaticamente. Se não estiver, clique no botão verde para executar.
4. Tente reproduzir o erro.
5. Depois que você tiver encontrado o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade registrada:
   - No Chrome, clique com o botão direito e clique em **Salvar como HAR com conteúdo**. Isso compacta e exporta os logs como um arquivo .har.
   - No IE/Microsoft Edge, clique no ícone **Exportar tráfego capturado**. Isso compacta e exporta o log.
6. Navegue até a guia **Console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito em qualquer lugar no log do console. Selecione **Salvar como**, para exportar e compactar o log.
   - No Microsoft Edge/IE, clique com o botão direito do mouse nos erros e selecione **Copiar tudo**.
7. Fechar as Ferramentas para Desenvolvedores.

## <a name="collector-error-codes-and-recommended-actions"></a>Códigos de erro de coletor e ações recomendadas

| Código do Erro | Nome do erro   | Mensagem   | Possíveis causas | Ação recomendada  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | O coletor expirou.                                                        | O coletor expirou.                                                                                    | Faça o download de uma nova versão do coletor e tente novamente.                                                                                      |
| 751       | UnableToConnectToServer        | Não foi possível se conectar ao vCenter Server '%Name;' devido ao erro: %ErrorMessage;     | Verifique a mensagem de erro para obter mais detalhes.                                                             | Resolva o problema e tente novamente.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | O servidor '%Name;' não é um vCenter Server.                                  | Forneça os detalhes do vCenter Server.                                                                       | Tente a operação novamente com os detalhes corretos do vCenter Server.                                                                                   |
| 753       | InvalidLoginCredentials        | Não foi possível se conectar ao vCenter Server '%Name;' devido ao erro: %ErrorMessage; | A conexão com o vCenter Server falhou devido a credenciais de logon inválidas.                             | Certifique-se de que as credenciais de logon fornecidas estejam corretas.                                                                                    |
| 754       | NoPerfDataAvailable           | Os dados de desempenho não estão disponíveis.                                               | Verifique o nível de estatísticas no vCenter Server. Ele deve ser definido como 3 para que os dados de desempenho estejam disponíveis. | Altere o nível de Estatísticas para 3 (para 5 minutos, 30 minutos e a duração de 2 horas) e tente depois de aguardar pelo menos um dia.                   |
| 756       | NullInstanceUUID               | Encontrada uma máquina com InstanceUUID nulo                                  | O vCenter Server pode ter um objeto inadequado.                                                      | Resolva o problema e tente novamente.                                                                                                           |
| 757       | VMNotFound                     | A máquina virtual não foi encontrada                                                  | A Máquina virtual pode ser excluída: %VMID;                                                                | Certifique-se de que as máquinas virtuais selecionadas ao investigar o inventário do vCenter existam durante a descoberta                                      |
| 758       | GetPerfDataTimeout             | A solicitação do VCenter atingiu o tempo limite. Mensagem %Message;                                  | As credenciais do vCenter Server estão incorretas                                                              | Verifique as credenciais do vCenter Server e certifique-se de que esse vCenter Server esteja acessível. Repita a operação. Se o problema persistir, contate o suporte. |
| 759       | VmwareDllNotFound              | DLL VMWare.Vim não encontrada.                                                     | O PowerCLI não está instalado corretamente.                                                                   | Verifique se o PowerCLI está instalado corretamente. Repita a operação. Se o problema persistir, contate o suporte.                               |
| 800       | ServiceError                   | O serviço Coletor de Migrações para Azure não está em execução.                               | O serviço Coletor de Migrações para Azure não está em execução.                                                       | Use services.msc para iniciar o serviço e repita a operação.                                                                             |
| 801       | PowerCLIError                  | Falha na instalação do PowerCLI do VMware.                                          | Falha na instalação do PowerCLI do VMware.                                                                  | Repita a operação. Se o problema persistir, instale-o manualmente e repita a operação.                                                   |
| 802       | TimeSyncError                  | O horário não está sincronizado com o servidor de horário na Internet.                            | O horário não está sincronizado com o servidor de horário na Internet.                                                    | Certifique-se de que o tempo no computador esteja definido corretamente em relação ao fuso horário do computador e repita a operação.                                 |
| 702       | OMSInvalidProjectKey           | A chave especificada do projeto é inválida.                                                | A chave especificada do projeto é inválida.                                                                        | Tente a operação novamente com a chave de projeto correta.                                                                                              |
| 703       | OMSHttpRequestException        | Erro ao enviar a solicitação. Mensagem %Message;                                | Confira a ID e a chave do projeto e verifique se o ponto de extremidade é acessível.                                       | Repita a operação. Se o problema persistir, contate o Suporte da Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | A solicitação de HTTP atingiu o tempo limite. Mensagem %Message;                                     | Confira a ID e a chave do projeto e verifique se o ponto de extremidade é acessível.                                       | Repita a operação. Se o problema persistir, contate o Suporte da Microsoft.                                                                     |
