---
title: Gateway de Gerenciamento de Dados para Data Factory | Microsoft Docs
description: Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados.
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9e40eba285aeb1cce6b77311d1b69a6b96967a0b
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="data-management-gateway"></a>Gateway de gerenciamento de dados
O Gateway de Gerenciamento de Dados é um agente cliente que você deve instalar no seu ambiente local para copiar dados entre a nuvem e os repositórios de dados locais. Os repositórios de dados locais compatíveis com o Data Factory estão listados na seção [Fontes de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Este artigo complementa o passo a passo do artigo [Mover dados entre repositórios de dados locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) . Neste passo a passo, você cria um pipeline que usa o gateway para mover dados de um banco de dados SQL Server local para um blob do Azure. Este artigo fornece informações detalhadas sobre o Gateway de Gerenciamento de Dados. 

Você pode escalar horizontalmente um Gateway de Gerenciamento de Dados por meio da associação de vários computadores locais com o gateway. Você pode escalar verticalmente com o aumento do número de trabalhos de movimentação de dados que podem ser executados simultaneamente em um nó. Esse recurso também está disponível para um gateway lógico com um único nó. Consulte o artigo [Escalar o Gateway de Gerenciamento de Dados no Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes.

> [!NOTE]
> Atualmente, o gateway suporta apenas a atividade de cópia e a atividade de procedimento armazenado no Data Factory. Não é possível usar o gateway de uma atividade personalizada para acessar fontes de dados locais.      

## <a name="overview"></a>Visão geral
### <a name="capabilities-of-data-management-gateway"></a>Funcionalidades do Gateway de Gerenciamento de Dados
O Gateway de Gerenciamento de Dados fornece as seguintes funcionalidades:

* Modelar fontes de dados locais e fontes de dados em nuvem na mesma data factory e mover dados.
* Ter um único painel de monitoramento e gerenciamento com visibilidade do status do gateway da página do Data Factory.
* Gerenciar o acesso a fontes de dados locais com segurança.
  * Não são necessárias alterações no firewall corporativo. O gateway faz apenas conexões de saída baseadas em HTTP para a Internet aberta.
  * Criptografar credenciais para seus armazenamentos de dados locais com seu certificado.
* Mover dados com eficiência: os dados são transferidos em paralelo, resilientes a problemas de rede intermitente com lógica de repetição automática.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você usa uma atividade de cópia para copiar dados entre repositórios locais e a nuvem, a atividade usa um gateway para transferir dados da fonte de dados local para a nuvem, e vice-versa.

Aqui está o fluxo de dados de alto nível e o resumo das etapas para a cópia com o gateway de dados: ![Fluxo de dados usando o gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. O desenvolvedor de dados cria um gateway para uma Azure Data Factory usando o [Portal do Azure](https://portal.azure.com) ou [Cmdlet do PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local ao especificar o gateway. Como parte da configuração de dados do serviço vinculado, o desenvolvedor usa o aplicativo Configurando Credenciais para especificar as credenciais e tipos de autenticação.  O diálogo do aplicativo Configurando Credenciais se comunica com o armazenamento de dados para testar a conexão e o gateway para salvar as credenciais.
3. O gateway criptografa credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados) antes de salvar as credenciais na nuvem.
4. O serviço Data Factory se comunica com o gateway para o agendamento e o gerenciamento de trabalhos por meio de um canal de controle que usa uma fila do Barramento de Serviço do Azure compartilhado. Quando o trabalho de atividade de cópia precisa ser inicializado, o Data Factory enfileira a solicitação junto com as informações de credencial. O gateway inicia o trabalho depois de sondar a fila.
5. O gateway descriptografa as credenciais com o mesmo certificado e se conecta ao armazenamento de dados local com o tipo de autenticação e as credenciais adequadas.
6. O gateway copia dados de um repositório local para um armazenamento na nuvem, ou vice-versa, dependendo de como a Atividade de Cópia estiver configurada no pipeline de dados. Para esta etapa, o gateway se comunica diretamente com os serviços de armazenamento baseado na nuvem, como Armazenamento de Blobs do Azure por um canal seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações para o uso do gateway
* Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais. No entanto, **uma única instância do gateway é vinculada apenas a um Azure Data Factory** e não pode ser compartilhada com outro Data Factory.
* Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada em um único computador. Supondo que você tenha dois data factories que precisam acessar fontes de dados locais, você precisará instalar gateways em dois computadores locais. Em outras palavras, um gateway é associado a um data factory específico
* O **gateway não precisa estar no mesmo computador que a fonte de dados**. No entanto, com o gateway mais próximo da fonte de dados, menor é o tempo para o gateway se conectar à fonte de dados. É recomendável instalar o gateway em um computador que seja diferente daquele que hospeda a fonte de dados local. Quando o gateway e a fonte de dados estiverem em computadores diferentes, o gateway não disputará os recursos com a fonte de dados.
* Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas data factories, mas a mesma fonte de dados local é registrada com ambas as data factories.
* Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um gateway **separado para o Azure Data Factory** em outro computador.
* O gateway deve ser usado mesmo quando você usar o **ExpressRoute**.
* Trate a fonte de dados como local (isto é, protegida por um firewall) mesmo quando você usar o **ExpressRoute**. Use o gateway para estabelecer conectividade entre o serviço e a fonte de dados.
* Você deverá **usar o gateway** mesmo se o armazenamento de dados estiver na nuvem em um **VM IaaS do Azure**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* As versões de **Sistema Operacional** com suporte são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Instalação do Gateway de Gerenciamento de Dados em um controlador de domínio não tem suporte atualmente.
* O .NET framework 4.5.1 ou superior é necessário. Se você estiver instalando o gateway em um computador com Windows 7, instale o .NET Framework 4.5 ou posterior. Confira [Requisitos de sistema do .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) para obter detalhes.
* A **configuração** recomendada para o computador do gateway é de, no mínimo, 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
* Se o computador host hibernar, o gateway não responderá às solicitações de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. Se o computador estiver configurado para hibernar, a instalação do gateway exibirá uma mensagem.
* Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de Gerenciamento de Dados. Você pode acrescentar usuários adicionais ao grupo local de usuários do **Gateway de Gerenciamento de dados do Windows**. Os membros desse grupo podem usar a ferramenta **Gerenciador de Configurações do Gateway de Gerenciamento de Dados** para configurar o gateway.

Como as execuções da atividade de cópia ocorrem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso aumentar durante horários de pico.

### <a name="installation-options"></a>Opções de instalação
O Gateway de Gerenciamento de Dados pode ser instalado das seguintes maneiras:

* Baixando um pacote de instalação do MSI no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).  O MSI também pode ser usado para atualizar o Gateway de Gerenciamento de Dados existente para a versão mais recente, com todas as configurações preservadas.
* Clicando no link **Baixar e instalar o gateway de dados** em INSTALAÇÃO MANUAL ou **Instalar diretamente neste computador** em INSTALAÇÃO EXPRESSA. Veja o artigo [Mover dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como usar a instalação expressa. A etapa manual o leva até o centro de download.  As instruções para baixar e instalar o gateway do centro de download estão na próxima seção.

### <a name="installation-best-practices"></a>Práticas recomendadas de instalação:
1. Configure o plano de energia no computador host para o gateway para que o computador não hiberne. Se o computador host hibernar, o gateway não responderá às solicitações de dados.
2. Faça backup do certificado associado ao gateway.

### <a name="install-the-gateway-from-download-center"></a>Instalar o gateway do centro de download
1. Navegue até a [página de download do Gateway de Gerenciamento de Dados da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **Baixar**, selecione a versão apropriada (**32 bits** v. **64 bits**) e clique em **Avançar**.
3. Execute o **MSI** diretamente ou salve-o em seu disco rígido e execute-o.
4. Na página de **Boas-vindas**, selecione um **idioma** e clique em **Avançar**.
5. **Aceite** os Termos de Licença e clique em **Avançar**.
6. Selecione **pasta** para instalar o gateway e clique em **Avançar**.
7. Na página **Pronto para instalar**, clique em **Instalar**.
8. Clique em **Concluir** para finalizar a instalação.
9. Obtenha a chave no portal do Azure. Consulte a próxima seção para obter instruções passo a passo.
10. Na página **Registrar gateway** do **Gerenciador de Configurações do Gateway de Gerenciamento de Dados** em execução no computador, siga estas etapas:
    1. Cole a chave no texto.
    2. Se preferir, clique em **Mostrar chave do gateway** para ver o texto da chave.
    3. Clique em **Registrar**.

### <a name="register-gateway-using-key"></a>Registrar gateway usando chave
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se você ainda não tiver criado um gateway lógico no portal
Para criar um gateway no portal e obter a chave na página **Configurar**, siga as etapas do passo a passo no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md).    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se você já tiver criado o gateway lógico no portal
1. No Portal do Azure, navegue até a página **Data Factory** e clique no bloco **Serviços Vinculados**.

    ![Página Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na página **Serviços Vinculados**, selecione o **gateway** lógico criado no portal.

    ![gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. Na página **Gateway de Dados**, clique em **Baixar e instalar o gateway de dados**.

    ![Baixar o link no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. Na página **Configurar**, clique em **Recriar chave**. Clique em Sim na mensagem de aviso depois de ler com cuidado.

    ![Recriar chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique no botão Copiar ao lado da chave. A chave é copiada para a área de transferência.

    ![Copiar chave](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ícones/notificações da bandeja do sistema
A imagem a seguir mostra alguns dos ícones da bandeja que você vê.

![ícones da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se mover o cursor sobre o ícone de bandeja do sistema/mensagem de notificação, você vê detalhes sobre o estado do gateway/da operação de atualização em uma janela pop-up.

### <a name="ports-and-firewall"></a>Portas e firewall
Há dois firewalls que você precisa levar em consideração: o **firewall corporativo** em execução no roteador central da organização e o **Firewall do Windows** configurado como um daemon no computador local em que o gateway está instalado.  

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

No nível do firewall corporativo, você precisa configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Descrição |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Usado para comunicação com o back-end do Serviço de Movimentação de Dados |
| *.core.windows.net |443 |Usado para cópia em etapas usando Blobs do Azure (se estiver configurado)|
| *.frontend.clouddatahub.net |443 |Usado para comunicação com o back-end do Serviço de Movimentação de Dados |


No nível do Firewall do Windows, essas portas de saída normalmente são habilitadas. Se não forem, você poderá configurar as portas e os domínios adequadamente no computador do gateway.

> [!NOTE]
> 1. Com base em sua origem/coletores, talvez você precise incluir domínios adicionais na lista de permissões e as portas de saída em seu Firewall do Windows/corporativo.
> 2. Para alguns bancos de dados na nuvem (por exemplo: [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access) etc.), talvez seja necessário incluir o endereço IP do computador do Gateway na lista de permissões na configuração do firewall deles.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de um repositório de dados de origem para um repositório de dados de coletor
Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do gateway e no próprio repositório de dados. Habilitar essas regras permite ao gateway se conectar com êxito à fonte e ao coletor. Habilite as regras para cada repositório de dados que esteja envolvido na operação de cópia.

Por exemplo, para copiar de **um repositório de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure**, siga as etapas abaixo:

* Permitir a comunicação **TCP** de saída na porta **1433** para o firewall do Windows e o firewall corporativo.
* Configurar as definições de firewall do SQL Server do Azure para adicionar o endereço IP do computador do gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o Gateway não poderá acessar diretamente o Azure SQL. Nesse caso, use [Cópia em Etapas](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) para o Banco de Dados SQL do Azure/DW SQL do Azure. Neste cenário, você exigiria apenas HTTPS (porta 443) para a movimentação de dados.
>
>


### <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o ambiente de rede corporativo usar um servidor proxy para acessar a Internet, configure o Gateway de Gerenciamento de Dados para usar as definições de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Definir proxy durante o registro](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

O gateway usa o servidor proxy para se conectar ao serviço de nuvem. Clique no link **Alteração** durante a configuração inicial. Você verá a caixa de diálogo **configuração de proxy** .

![Definir proxy usando o gerenciador de configurações](media/data-factory-data-management-gateway/SetProxySettings.png)

Há três opções de configuração:

* **Não usar proxy**: o gateway não usa explicitamente qualquer proxy para se conectar aos serviços de nuvem.
* **Usar proxy do sistema**: o gateway usa a configuração de proxy que é definida em diahost.exe.config e em diawp.exe.config.  Se nenhum proxy estiver configurado em diahost.exe.config e em diawp.exe.config, o gateway se conectará ao serviço de nuvem diretamente sem passar pelo proxy.
* **Usar proxy personalizado**: configure a definição do proxy HTTP a ser usado pelo gateway, em vez de usar as configurações em diahost.exe.config e diawp.exe.config.  Endereço e porta são necessários.  O Nome de Usuário e a Senha são opcionais, dependendo da configuração de autenticação do seu proxy.  Todas as configurações são criptografadas com o certificado de credencial do gateway e armazenadas localmente no computador host do gateway.

O Serviço de Host do Gateway de Gerenciamento de Dados é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o gateway tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use o Gerenciador de Configurações do Gateway de Gerenciamento de Dados.

1. Iniciar o **Gerenciador de Configuração de Gateway de Gerenciamento de Dados**.
2. Alterne para a guia **Configurações** .
3. Clique no link **Alterar** na seção **Proxy HTTP** para iniciar a caixa de diálogo **Configurar Proxy HTTP**.  
4. Depois de clicar no botão **Avançar** , você verá uma caixa de diálogo de aviso solicitando sua permissão para salvar a configuração de proxy e reiniciar o Serviço de Host do Gateway.

Você pode exibir e atualizar o proxy HTTP usando a ferramenta Gerenciador de Configurações.

![Definir proxy usando o gerenciador de configurações](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o Serviço de Host do Gateway será executado sob a conta de domínio. Se você alterar a senha da conta de domínio posteriormente, lembre-se de atualizar as definições de configuração para o serviço e reiniciá-lo adequadamente. Por conta desse requisito, sugerimos o uso de uma conta de domínio dedicada para acessar o servidor proxy que não exija a atualização da senha com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy
Se você escolher a configuração **Usar proxy do sistema** para o proxy HTTP, o gateway usará a configuração de proxy em diahost.exe.config e diawp.exe.config.  Se nenhum proxy for especificado em diahost.exe.config., o gateway se conectará ao serviço de nuvem diretamente sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost.exe.config.  

1. No Explorador de Arquivos, faça uma cópia de segurança de C:\Arquivos de Programas\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Inicie o Notepad.exe executando como administrador e abra o arquivo de texto "C:\Arquivos de Programas\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config." Você pode encontrar a marcação padrão para system.net conforme mostrado no código abaixo:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias como scriptLocation. Confira [proxy Element (Network Settings)](https://msdn.microsoft.com/library/sa91de1e.aspx) (Elemento proxy [Configurações de Rede]) na sintaxe.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Salve o arquivo de configuração no local original e reinicie o Serviço de Host do Gateway de Gerenciamento de Dados, que assimila as alterações. Para reiniciar o serviço: use o miniaplicativo de serviços no painel de controle ou no Gerenciador de **Configurações do Gateway de Gerenciamento de Dados** > clique no botão **Parar Serviço** e depois em **Iniciar Serviço**. Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar **ambos** diahost.exe.config e diawp.exe.config.  


Além dos desses pontos, você também precisa verificar se o Microsoft Azure está lista de autorizados da sua empresa. Baixe a lista de endereços IP válidos do Microsoft Azure no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros similares aos descritos a seguir, eles provavelmente se deverão à configuração incorreta do servidor proxy ou firewall, que impedirá o gateway de se conectar ao Data Factory para se autenticar. Confira a seção anterior para garantir que seu firewall e servidor proxy estejam configurados corretamente.

1. Ao tentar registrar o gateway, você recebe o seguinte erro: "Falha ao registrar a chave do gateway. Antes de tentar registrar a chave do gateway novamente, confirme se o Gateway de Gerenciamento de Dados está em um estado conectado e o Serviço de Host do Gateway de Gerenciamento de Dados está iniciado."
2. Ao abrir o Gerenciador de Configurações, você vê o status "Desconectado" ou "Conectando". Ao exibir os logs de eventos do Windows, em "Visualizar eventos" > "Logs de Aplicativos e Serviços" > "Gateway de Gerenciamento de Dados", você vê mensagens de erro como as do seguinte: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Abrir a porta 8050 para criptografia de credencial
O aplicativo **Definindo Credenciais** usa a porta de entrada **8050** para retransmitir credenciais ao gateway quando você configura um serviço vinculado local no portal do Azure. Durante a instalação do gateway, por padrão, a instalação do gateway o abre no computador do gateway.

Se estiver usando um firewall de terceiros, você poderá abrir manualmente a porta 8050. Se tiver problemas de firewall durante a configuração do gateway, você poderá tentar usar o comando a seguir para instalar o gateway sem configurar o firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 no computador do gateway, use mecanismos diferentes do aplicativo **Definindo Credenciais** para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o cmdlet do PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) . Confira a seção [Definir Credenciais e Segurança](#set-credentials-and-securityy) para saber como as credenciais do armazenamento de dados podem ser definidas.

## <a name="update"></a>Atualização
Por padrão, o Gateway de Gerenciamento de Dados é atualizado automaticamente quando uma versão mais recente do gateway está disponível. O gateway não é atualizado até que todas as tarefas agendadas sejam concluídas. Nenhuma tarefa adicional é processada pelo gateway até que a operação de atualização seja concluída. Se a atualização falhar, o gateway será revertido para a versão antiga.

Você vê a hora de atualização agendada nos seguintes locais:

* Na página de propriedades do gateway no Portal do Azure.
* Na home page do Gerenciador de Configurações do Gateway de Gerenciador de Dados
* Na mensagem de notificação da bandeja do sistema.

A guia Página Inicial do Gerenciador de Configurações do Gateway de Gerenciamento de Dados exibe a agenda de atualização e a última vez em que o gateway foi instalado/atualizado.

![Agende atualizações](media/data-factory-data-management-gateway/UpdateSection.png)

Você pode instalar a atualização imediatamente ou aguardar até que o gateway seja atualizado automaticamente no horário agendado. Por exemplo, a imagem a seguir mostra a mensagem de notificação exibida no Gerenciador de Configurações do Gateway com o botão Atualizar, que pode ser clicado para instalar a atualização imediatamente.

![Atualizar no Gerenciador de Configurações DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria semelhante à mostrada na imagem abaixo:

![Mensagem da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Você vê o status da operação de atualização (manual ou automática) na bandeja do sistema. Ao iniciar o Gerenciador de Configurações do Gateway na próxima vez, você verá uma mensagem na barra de notificação indicando que o gateway foi atualizado, com um link para o [tópico de novidades](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para habilitar/desabilitar o recurso de atualização automática
Você pode habilitar/desabilitar o recurso de atualização automática seguindo as etapas abaixo:

[Para o gateway de nó único]
1. Inicie o Windows PowerShell no computador do gateway.
2. Mude para a pasta C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Execute o seguinte comando para DESATIVAR (desabilitar) o recurso de atualização automática.   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. Para ativá-la novamente:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
[[Para vários nó altamente disponíveis e gateway dimensionável(versão prévia)](data-factory-data-management-gateway-high-availability-scalability.md)]
1. Inicie o Windows PowerShell no computador do gateway.
2. Mude para a pasta C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Execute o seguinte comando para DESATIVAR (desabilitar) o recurso de atualização automática.   

    Para o gateway com o recurso de alta disponibilidade (versão prévia), um parâmetro AuthKey adicional é necessário.
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Para ativá-la novamente:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 


## Configuration Manager
Once you install the gateway, you can launch Data Management Gateway Configuration Manager in one of the following ways:

1. In the **Search** window, type **Data Management Gateway** to access this utility.
2. Run the executable **ConfigManager.exe** in the folder: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### Home page
The Home page allows you to do the following actions:

* View status of the gateway (connected to the cloud service etc.).
* **Register** using a key from the portal.
* **Stop** and start the **Data Management Gateway Host service** on the gateway machine.
* **Schedule updates** at a specific time of the days.
* View the date when the gateway was **last updated**.

### Settings page
The Settings page allows you to do the following actions:

* View, change, and export **certificate** used by the gateway. This certificate is used to encrypt data source credentials.
* Change **HTTPS port** for the endpoint. The gateway opens a port for setting the data source credentials.
* **Status** of the endpoint
* View **SSL certificate** is used for SSL communication between portal and the gateway to set credentials for data sources.  

### Diagnostics page
The Diagnostics page allows you to do the following actions:

* Enable verbose **logging**, view logs in event viewer, and send logs to Microsoft if there was a failure.
* **Test connection** to a data source.  

### Help page
The Help page displays the following information:  

* Brief description of the gateway
* Version number
* Links to online help, privacy statement, and license agreement.  

## Monitor gateway in the portal
In the Azure portal, you can view near-real time snapshot of resource utilization (CPU, memory, network(in/out), etc.) on a gateway machine.  

1. In Azure portal, navigate to the home page for your data factory, and click **Linked services** tile. 

    ![Data factory home page](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Select the **gateway** in the **Linked services** page.

    ![Linked services page](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. In the **Gateway** page, you can see the memory and CPU usage of the gateway.

    ![CPU and memory usage of gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Enable **Advanced settings** to see more details such as network usage.
    
    ![Advanced monitoring of gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

The following table provides descriptions of columns in the **Gateway Nodes** list:  

Monitoring Property | Description
:------------------ | :---------- 
Name | Name of the logical gateway and nodes associated with the gateway. Node is an on-premises Windows machine that has the gateway installed on it. For information on having more than one node (up to four nodes) in a single logical gateway, see [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Status of the logical gateway and the gateway nodes. Example: Online/Offline/Limited/etc. For information about these statuses, See [Gateway status](#gateway-status) section. 
Version | Shows the version of the logical gateway and each gateway node. The version of the logical gateway is determined based on version of majority of nodes in the group. If there are nodes with different versions in the logical gateway setup, only the nodes with the same version number as the logical gateway function properly. Others are in the limited mode and need to be manually updated (only in case auto-update fails). 
Available memory | Available memory on a gateway node. This value is a near real-time snapshot. 
CPU utilization | CPU utilization of a gateway node. This value is a near real-time snapshot. 
Networking (In/Out) | Network utilization of a gateway node. This value is a near real-time snapshot. 
Concurrent Jobs (Running/ Limit) | Number of jobs or tasks running on each node. This value is a near real-time snapshot. Limit signifies the maximum concurrent jobs for each node. This value is defined based on the machine size. You can increase the limit to scale up concurrent job execution in advanced scenarios, where CPU/memory/network is under-utilized, but activities are timing out. This capability is also available with a single-node gateway (even when the scalability and availability feature is not enabled).  
Role | There are two types of roles in a multi-node gateway – Dispatcher and worker. All nodes are workers, which means they can all be used to execute jobs. There is only one dispatcher node, which is used to pull tasks/jobs from cloud services and dispatch them to different worker nodes (including itself).

In this page, you see some settings that make more sense when there are two or more nodes (scale out scenario) in the gateway. See [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md) for details about setting up a multi-node gateway.

### Gateway status
The following table provides possible statuses of a **gateway node**: 

Status  | Comments/Scenarios
:------- | :------------------
Online | Node connected to Data Factory service.
Offline | Node is offline.
Upgrading | The node is being auto-updated.
Limited | Due to Connectivity issue. May be due to HTTP port 8050 issue, service bus connectivity issue, or credential sync issue. 
Inactive | Node is in a configuration different from the configuration of other majority nodes.<br/><br/> A node can be inactive when it cannot connect to other nodes. 


The following table provides possible statuses of a **logical gateway**. The gateway status depends on statuses of the gateway nodes. 

Status | Comments
:----- | :-------
Needs Registration | No node is yet registered to this logical gateway
Online | Gateway Nodes are online
Offline | No node in online status.
Limited | Not all nodes in this gateway are in healthy state. This status is a warning that some node might be down! <br/><br/>Could be due to credential sync issue on dispatcher/worker node. 

## Scale up gateway
You can configure the number of **concurrent data movement jobs** that can run on a node to scale up the capability of moving data between on-premises and cloud data stores. 

When the available memory and CPU are not utilized well, but the idle capacity is 0, you should scale up by increasing the number of concurrent jobs that can run on a node. You may also want to scale up when activities are timing out because the gateway is overloaded. In the advanced settings of a gateway node, you can increase the maximum capacity for a node. 
  

## Troubleshooting gateway issues
See [Troubleshooting gateway issues](data-factory-troubleshoot-gateway-issues.md) article for information/tips for troubleshooting issues with using the data management gateway.  

## Move gateway from one machine to another
This section provides steps for moving gateway client from one machine to another machine.

1. In the portal, navigate to the **Data Factory home page**, and click the **Linked Services** tile.

    ![Data Gateways Link](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Select your gateway in the **DATA GATEWAYS** section of the **Linked Services** page.

    ![Linked Services page with gateway selected](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. In the **Data gateway** page, click **Download and install data gateway**.

    ![Download gateway link](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. In the **Configure** page, click **Download and install data gateway**, and follow instructions to install the data gateway on the machine.

    ![Configure page](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Keep the **Microsoft Data Management Gateway Configuration Manager** open.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. In the **Configure** page in the portal, click **Recreate key** on the command bar, and click **Yes** for the warning message. Click **copy button** next to key text that copies the key to the clipboard. The gateway on the old machine stops functioning as soon you recreate the key.  

    ![Recreate key](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Paste the **key** into text box in the **Register Gateway** page of the **Data Management Gateway Configuration Manager** on your machine. (optional) Click **Show gateway key** check box to see the key text.

    ![Copy key and Register](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Click **Register** to register the gateway with the cloud service.
9. On the **Settings** tab, click **Change** to select the same certificate that was used with the old gateway, enter the **password**, and click **Finish**.

   ![Specify Certificate](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   You can export a certificate from the old gateway by doing the following steps: launch Data Management Gateway Configuration Manager on the old machine, switch to the **Certificate** tab, click **Export** button and follow the instructions.
10. After successful registration of the gateway, you should see the **Registration** set to **Registered** and **Status** set to **Started** on the Home page of the Gateway Configuration Manager.

## Encrypting credentials
To encrypt credentials in the Data Factory Editor, do the following steps:

1. Launch web browser on the **gateway machine**, navigate to [Azure portal](http://portal.azure.com). Search for your data factory if needed, open data factory in the **DATA FACTORY** page and then click **Author & Deploy** to launch Data Factory Editor.   
2. Click an existing **linked service** in the tree view to see its JSON definition or create a linked service that requires a data management gateway (for example: SQL Server or Oracle).
3. In the JSON editor, for the **gatewayName** property, enter the name of the gateway.
4. Enter server name for the **Data Source** property in the **connectionString**.
5. Enter database name for the **Initial Catalog** property in the **connectionString**.    
6. Click **Encrypt** button on the command bar that launches the click-once **Credential Manager** application. You should see the **Setting Credentials** dialog box.

    ![Setting credentials dialog](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. In the **Setting Credentials** dialog box, do the following steps:
   1. Select **authentication** that you want the Data Factory service to use to connect to the database.
   2. Enter name of the user who has access to the database for the **USERNAME** setting.
   3. Enter password for the user for the **PASSWORD** setting.  
   4. Click **OK** to encrypt credentials and close the dialog box.
8. You should see a **encryptedCredential** property in the **connectionString** now.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais possa se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais da fonte de dados teste a conexão à fonte de dados.  

Quando você usa o aplicativo **Definindo Credenciais**, o portal criptografa as credenciais com o certificado especificado na guia **Certificado** do **Gerenciador de Configurações do Gateway** no computador do gateway.

Se estiver procurando uma abordagem baseada em API para criptografar as credenciais, você poderá usar o cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) do PowerShell para criptografar as credenciais. O cmdlet usa o certificado que esse gateway está configurado para usar para criptografar as credenciais. Você adiciona credenciais criptografadas ao elemento **EncryptedCredential** da **connectionString** no JSON. Você usa o JSON com o cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) ou no Editor do Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há mais uma abordagem para definir as credenciais usando o Editor Data Factory. Se você criar um serviço vinculado do SQL Server usando o editor e inserir credenciais em texto sem formatação, as credenciais serão criptografadas usando um certificado que é de propriedade do serviço Data Factory. Ele NÃO usará o certificado que o gateway está configurado para usar. Embora essa abordagem possa ser um pouco mais rápida em alguns casos, ela é menos segura. Portanto, é recomendável que você siga essa abordagem somente para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure.

1. Inicie o **PowerShell do Azure** no modo de administrador.
2. Faça logon na sua conta do Azure executando o seguinte comando e digitando suas credenciais do Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Use o cmdlet **New-AzureRmDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exemplo de comando e saída**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. No Azure PowerShell, mude para a pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Execute **RegisterGateway.ps1** associado à variável local **$Key**, conforme mostrado no comando a seguir. Esse script registra o agente cliente instalado no computador com o gateway lógico criado anteriormente.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Você pode registrar o gateway em um computador remoto usando o parâmetro IsRegisterOnRemoteMachine. Exemplo:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Você pode usar o cmdlet **Get-AzureRmDataFactoryGateway** para obter a lista de gateways no data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Você pode remover um gateway usando o cmdlet **Remove-AzureRmDataFactoryGateway** e atualizar a descrição de um gateway com os cmdlets **Set-AzureRmDataFactoryGateway** cmdlets. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.  

### <a name="list-gateways-using-powershell"></a>Listar gateways usando o PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateways usando o PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Próximas etapas
* Consulte o artigo [Mover os dados entre os armazenamentos de dados local e de nuvem](data-factory-move-data-between-onprem-and-cloud.md) . Neste passo a passo, você cria um pipeline que usa o gateway para mover dados de um banco de dados SQL Server local para um blob do Azure.  

