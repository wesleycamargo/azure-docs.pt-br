<properties 
	pageTitle="Gateway de Gerenciamento de Dados para Data Factory | Microsoft Azure"
	description="Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="spelluru"/>

# Gateway de gerenciamento de dados
O Gateway de Gerenciamento de Dados é um agente cliente que você deve instalar em seu ambiente local para habilitar a movimentação de dados entre armazenamentos de dados locais e de nuvem que têm [suporte da Atividade de Cópia](data-factory-data-movement-activities.md##supported-data-stores).

Este artigo complementa o conteúdo no artigo [Mover dados entre armazenamentos de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md), que tem um passo a passo para criar um pipeline de Data Factory que usa o gateway para mover dados de um banco de dados do SQL Server local para um blob do Azure. Este artigo fornece informações detalhadas sobre o Gateway de Gerenciamento de Dados.

## Funcionalidades do Gateway de Gerenciamento de Dados
O Gateway de Gerenciamento de Dados fornece as seguintes funcionalidades:

- Modelar fontes de dados locais e fontes de dados em nuvem na mesma data factory e mover dados.
- Ter um único painel de monitoramento e gerenciamento com visibilidade do status do gateway da folha do Data Factory.
- Gerenciar o acesso a fontes de dados locais com segurança.
	- Não são necessárias alterações no firewall corporativo. O gateway faz apenas conexões de saída baseadas em HTTP para a Internet aberta.
	- Criptografar credenciais para seus armazenamentos de dados locais com seu certificado.
- Mover dados com eficiência: os dados são transferidos em paralelo, resilientes a problemas de rede intermitente com lógica de repetição automática.

## Fluxo de dados usando o gateway
Quando você usa uma atividade de cópia em um pipeline de dados para incluir dados locais na nuvem para processamento adicional, ou exportar dados de resultado na nuvem de volta para um armazenamento de dados local, a atividade de cópia usa internamente um gateway para transferir os dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e o resumo das etapas para a cópia com o gateway de dados: ![Fluxo de dados usando o gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	O desenvolvedor de dados cria um novo gateway para uma Azure Data Factory usando o [Portal do Azure](https://portal.azure.com) ou [Cmdlet do PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2.	O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local ao especificar o gateway. Como parte da configuração de dados do serviço vinculado, o desenvolvedor usa o aplicativo Configurando Credenciais para especificar as credenciais e tipos de autenticação. O diálogo do aplicativo Configurando Credenciais se comunica com o armazenamento de dados para testar a conexão e o gateway para salvar as credenciais.
3. O gateway criptografa credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados) antes de salvar as credenciais na nuvem.
4. O serviço Data Factory se comunica com o gateway para o agendamento e o gerenciamento de trabalhos por meio de um canal de controle que usa uma fila do Barramento de Serviço do Azure compartilhado. Quando o trabalho de atividade de cópia precisa ser inicializado, o Data Factory enfileira a solicitação junto com as informações de credencial. O gateway inicia o trabalho depois de sondar a fila.
5.	O gateway descriptografa as credenciais com o mesmo certificado e se conecta ao armazenamento de dados local com o tipo de autenticação e as credenciais adequadas.
6.	O gateway copia dados do armazenamento local para um armazenamento em nuvem ou de um armazenamento em nuvem para um armazenamento de dados local dependendo de como a atividade de cópia é configurada no pipeline de dados. Observação: para esta etapa, o gateway se comunica diretamente com o serviço de armazenamento baseado em nuvem (por exemplo, Blob do Azure, SQL do Azure etc.) por um canal seguro (HTTPS).

## Considerações para o uso do gateway
- Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais, mas observe que **uma única instância de gateway está associada a apenas uma Azure Data Factory** e não pode ser compartilhada com outra data factory.
- Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada em um único computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
- O **gateway não precisa estar no mesmo computador que a fonte de dados**, mas ficar mais próximo à fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local para que o gateway não dispute os recursos com a fonte de dados.
- Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas data factories, mas a mesma fonte de dados local é registrada com ambas as data factories.
- Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **gateway separado para o Azure Data Factory** em outro computador.
- Você deve **usar o gateway, mesmo quando usar a Rota Expressa**.
- Você deve tratar a fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando usar a **Rota Expressa** e **usar o gateway** para estabelecer a conectividade entre o serviço e a fonte de dados.
- Você deverá **usar o gateway** mesmo se o armazenamento de dados estiver na nuvem em um **VM IaaS do Azure**.

## Instalar o Gateway de Gerenciamento de Dados

### Instalação do gateway - pré-requisitos
- As versões de **Sistema Operacional** com suporte são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Instalação do Gateway de Gerenciamento de Dados em um controlador de domínio não tem suporte atualmente.
- A **configuração** recomendada para o computador do gateway é no mínimo 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
- Se o computador host hibernar, o gateway não poderá responder a solicitações de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. A instalação do gateway exibe uma mensagem se o computador estiver configurado para hibernar.
- Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de Gerenciamento de Dados. Você pode acrescentar usuários adicionais ao grupo local de usuários do **Gateway de Gerenciamento de dados do Windows**. Os membros desse grupo poderão usar a ferramenta Gerenciador de configuração de Gateway de gerenciamento de dados para configurar o gateway.

Devido ao fato de as execuções da atividade de cópia ocorrerem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você observará o uso do recurso aumentar durante horários de pico. Embora o indicado acima seja a configuração mínima, é sempre melhor ter uma configuração com mais recursos do que as configurações mínimas descritas acima dependendo da sua carga específica para movimentação de dados.

### Instalação
O Gateway de Gerenciamento de Dados pode ser instalado das seguintes maneiras:

- Baixando um pacote de instalação do MSI no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar o Gateway de Gerenciamento de Dados existente para a versão mais recente, com todas as configurações preservadas.
- Clicando no link **Baixar e instalar o gateway de dados** em INSTALAÇÃO MANUAL ou **Instalar diretamente neste computador** em INSTALAÇÃO EXPRESSA. Consulte o artigo [Mover dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como usar a instalação expressa. A etapa manual leva você para o centro de download e as instruções para baixar e instalar o gateway do centro de download estão na próxima seção.


### Práticas recomendadas de instalação:
1.	Configure o plano de energia no computador host para o gateway para que o computador não hiberne. Se o computador host hibernar, o gateway não poderá responder a solicitações de dados.
2.	Você deve fazer backup do certificado associado ao gateway.

### Instalar o gateway no centro de download
1. Navegue até a [página de download do Gateway de Gerenciamento de Dados da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **Baixar**, selecione a versão apropriada (**32 bits** v. **64 bits**) e clique em **Avançar**.
3. Execute o **MSI** diretamente ou salve-o em seu disco rígido e execute-o.
4. Na página de **Boas-vindas**, selecione um **idioma** e clique em **Avançar**.
5. **Aceite** os Termos de Licença e clique em **Avançar**.
6. Selecione **pasta** para instalar o gateway e clique em **Avançar**.
7. Na página **Pronto para instalar**, clique em **Instalar**.
8. Clique em **Concluir** para finalizar a instalação.
9. Obtenha a chave no portal do Azure. Consulte a próxima seção para obter instruções passo a passo.
10. Na página **Registrar gateway** no **Gerenciador de Configuração do Gateway de Gerenciamento de Dados** e execução no seu computador e cole a chave no texto. Opcionalmente, clique em **Mostrar chave de gateway** para ver o texto da chave e clique em **Registrar**.

### Obter a chave

#### Se você ainda não tiver criado um gateway lógico no portal
Siga as etapas do passo a passo no artigo [Mover dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para criar um novo gateway no portal e obter a chave da folha **Configurar**.

#### Se você já tiver criado o gateway lógico no portal
1. No Portal do Azure, navegue até a folha **Data Factory** e clique no bloco **Serviços Vinculados**.

	![Folha Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na folha **Serviços Vinculados**, selecione o **gateway** lógico criado no portal.

	![gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. Na folha **Gateway de Dados**, clique em **Baixar e instalar o gateway de dados**.

	![Baixar o link no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. Na folha **Configurar**, clique em **Recriar chave**. Clique em Sim na mensagem de aviso depois de ler com cuidado.

	![Recriar chave](media/data-factory-data-management-gateway/recreate-key-button.png)
4. Clique no botão Copiar ao lado da chave para copiá-lo para a área de transferência.
	
	![Copiar chave](media/data-factory-data-management-gateway/copy-gateway-key.png)

## Gerenciador de Configuração de Gateway de Gerenciamento de Dados 
Depois de instalar o gateway, você pode iniciar o Gerenciador de Configuração de Gateway de Gerenciamento de Dados de uma das seguintes maneiras:

- Na janela de **Pesquisa**, digite **Gateway de Gerenciamento de Dados** para acessar esse utilitário.
- Execute o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Gateway de Gerenciamento de Dados Microsoft\\1.0\\Shared**
 
### Página inicial
A home page permite que você faça o seguinte:

- Veja o status do gateway, esteja ele conectado ou não ao serviço de nuvem.
- **Registre** usando uma chave do portal.
- **Pare** e inicie o **serviço Host de Gateway de Gerenciamento de Dados** no computador do gateway.
- **Agende atualizações** em um momento específico de dias.
- Veja a data em que o gateway foi **atualizado pela última vez**.

### Página Configurações
A página Configurações permite que você faça o seguinte:

- Veja, altere e exporte o **certificado** usado pelo gateway. Esse certificado é usado para criptografar credenciais de fonte de dados.
- Altere a **porta HTTPS** para o ponto de extremidade. O gateway abre uma porta para definir as credenciais de fonte de dados.
- **Status** do ponto de extremidade
- Exibir **certificado SSL** é usado para comunicação SSL entre o portal e o gateway na definição de credenciais de fontes de dados.

### Página de Diagnóstico
A página de Diagnóstico permite que você faça o seguinte:

- Habilite o **registro em log** detalhado, veja logs no visualizador de eventos e envie logs para a Microsoft em caso de falha.
- **Teste a conexão** para uma fonte de dados.

### Página de ajuda
A página de Ajuda exibe o seguinte:

- Breve descrição do gateway
- Número de versão
- Links para a ajuda online, política de privacidade e termos de licença.

## Ícones/notificações da bandeja do sistema
A imagem a seguir mostra alguns dos ícones da bandeja que você verá.

![ícones da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se mover o cursor sobre o ícone de bandeja do sistema/mensagem de notificação, você verá detalhes sobre o estado do gateway/da operação de atualização em uma janela pop-up.

## Atualizar o Gateway de Gerenciamento de Dados
Por padrão, o Gateway de Gerenciamento de Dados é atualizado automaticamente quando uma versão mais recente do gateway está disponível. O gateway não é atualizado até que todas as tarefas agendadas sejam concluídas. Nenhuma tarefa adicional é processada pelo gateway até que a operação de atualização seja concluída. Se a atualização falhar, o gateway será revertido para a versão antiga.

Você verá o horário de atualização agendado no portal na folha de propriedades do gateway, na home page do Gerenciador de Configurações do Gateway de Gerenciamento de Dados, e na mensagem de notificação da bandeja do sistema.

A guia Página Inicial do Gerenciador de Configurações do Gateway de Gerenciamento de Dados exibe a agenda de atualização, bem como a última vez em que o gateway foi instalado/atualizado.

![Agendar atualizações](media/data-factory-data-management-gateway/UpdateSection.png)

Você tem a opção de instalar a atualização imediatamente ou aguardar até que o gateway seja atualizado automaticamente no horário agendado. Por exemplo, a captura de tela a seguir mostra a mensagem de notificação do Gerenciador de Configurações do Gateway de Gerenciamento de Dados, juntamente com o botão Atualizar no qual você clica para instalá-lo imediatamente.

![Atualizar no Gerenciador de Configurações DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria semelhante ao seguinte:

![Mensagem da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Você verá o status da operação de atualização (manual ou automática) na bandeja do sistema. Ao abrir o Gerenciador de Configurações do Gateway de Gerenciamento de Dados posteriormente, você verá uma mensagem na barra de notificação indicando que o gateway foi atualizado, com um link para o [tópico de novidades](data-factory-gateway-release-notes.md).

### Para habilitar/desabilitar o recurso de atualização automática
Você pode habilitar/desabilitar o recurso de atualização automática fazendo o seguinte:

1. Inicie o Windows PowerShell no computador do gateway.
2. Alterne para a pasta C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Execute o seguinte comando para DESATIVAR (desabilitar) o recurso de atualização automática.

		.\GatewayAutoUpdateToggle.ps1  -off

4. Para ativá-la novamente:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Considerações sobre a porta e segurança
Há dois firewalls que você precisa levar em consideração: o **firewall corporativo** em execução no roteador central da organização e o **Firewall do Windows** configurado como um daemon no computador local em que o gateway está instalado.

![firewalls](./media/data-factory-data-management-gateway/firewalls.png)

## Gateway de conexão com serviços de nuvem
Para manter a conectividade do gateway com o Azure Data Factory e outros serviços de nuvem, você precisa verificar se a regra de saída para as portas **TCP** **80** e **443** está configurada. Opcionalmente, habilite as portas **9350** a **9354**, que são usadas pelo Barramento de Serviço do Microsoft Azure para estabelecer a conexão entre o Azure Data Factory e o Gateway de Gerenciamento de Dados e podem melhorar o desempenho da comunicação entre eles.

No nível do firewall corporativo, você precisa configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Descrição |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443, 80 | Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) | 
| *.servicebus.windows.net | 9350-9354, 5671 | Retransmissão do barramento de serviço opcional sobre TCP | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

No nível do firewall do windows, essas portas de saída normalmente são habilitadas. Se não forem, você poderá configurar as portas e os domínios adequadamente no computador do gateway.

## Definir credenciais de gateway
A porta de entrada **8050** será usada pelo aplicativo **Definindo Credenciais** para retransmitir as credenciais ao gateway quando você configurar um serviço vinculado local no Portal do Azure (detalhes mais adiante neste artigo). Durante a instalação do gateway, por padrão, a instalação do Gateway de Gerenciamento de Dados o abre no computador do gateway.
 
Caso seja usado um firewall de terceiros, você poderá abrir manualmente a porta 8050. Se tiver problemas de firewall durante a instalação do gateway, você poderá tentar usar o comando a seguir para instalar o gateway sem configurar o firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 no computador do gateway, para configurar um serviço vinculado local, você precisará usar mecanismos diferentes do aplicativo **Definindo Credenciais** para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o cmdlet do PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Confira a seção [Definir Credenciais e Segurança](#set-credentials-and-securityy) para saber como as credenciais do armazenamento de dados podem ser definidas.

**Para copiar dados de um armazenamento de dados de origem para um armazenamento de dados coletor:**

Você precisa certificar-se de que as regras de firewall estejam habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do gateway e no armazenamento de dados em si. Isso permite que o gateway se conecte à origem e ao coletor com êxito. Você precisa habilitar regras para cada repositório de dados que está envolvido na operação de cópia.

Por exemplo, para copiar de **um armazenamento de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure**, você precisa permitir a comunicação **TCP** de saída na porta **1433** para o Firewall do Windows e o firewall corporativo e definir as configurações de firewall do SQL Server do Azure para adicionar o endereço IP do computador do gateway à lista de endereços IP permitidos.

### Considerações do servidor proxy
Por padrão, o Gateway de Gerenciamento de Dados utilizará as configurações de proxy do Internet Explorer e usará as credenciais padrão para acessá-lo. Se isso não se adequar ao seu caso, você poderá definir as **configurações do servidor proxy**, conforme mostrado abaixo, para garantir que o gateway possa se conectar ao Azure Data Factory:

1.	Depois de instalar o Gateway de Gerenciamento de Dados, no Explorador de Arquivos, faça uma cópia de segurança de “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config” para fazer backup do arquivo original.
2.	Inicie o Notepad.exe executando como administrador e abra o arquivo de texto “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”. Você encontrará a marca padrão para system.net da seguinte forma:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Você pode adicionar os detalhes do servidor proxy, como o endereço proxy dentro dessa marca pai, por exemplo:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias como scriptLocation. Confira [proxy Element (Network Settings)](https://msdn.microsoft.com/library/sa91de1e.aspx) (Elemento proxy [Configurações de Rede]) na sintaxe.

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Salve o arquivo de configuração no local original e reinicie o serviço de Gateway de Gerenciamento de Dados para acompanhar as alterações. Você pode fazer isso em **Iniciar** > **Services.msc** ou, no **Gerenciador de Configurações do Gateway de Gerenciamento de Dados**, clique no botão **Parar Serviço** e em **Iniciar Serviço**. Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

Além dos pontos acima, você também precisa certificar-se de que o Microsoft Azure esteja na lista branca da sua empresa. Baixe a lista de endereços IP válidos do Microsoft Azure no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros como os descritos a seguir, eles provavelmente se devem à configuração incorreta do servidor proxy ou firewall, que impede o Gateway de Gerenciamento de Dados de se conectar ao Azure Data Factory para se autenticar. Consulte a seção acima para garantir que seu firewall e servidor proxy estejam configurados corretamente.

1.	Ao tentar registrar o gateway, você recebe o seguinte erro: "Falha ao registrar a chave do gateway. Antes de tentar registrar a chave do gateway novamente, confirme se o Gateway de Gerenciamento de Dados está em um estado conectado e o Serviço de Host do Gateway de Gerenciamento de Dados está iniciado."
2.	Ao abrir o Gerenciador de Configurações, você vê o status “Desconectado” ou “Conectando”. Ao exibir os logs de eventos do Windows, em "Visualizador de Eventos" > "Logs de Aplicativos e Serviços" > "Gateway de Gerenciamento de Dados" você vê mensagens de erro como "Impossível conectar-se ao servidor remoto" ou "Um componente do Gateway de Gerenciamento de Dados parou de responder e será reiniciado automaticamente. Nome do componente: Gateway."

## Solucionar problemas de gateway

- Você pode encontrar informações detalhadas nos logs de gateway nos logs de eventos do Windows. Você pode encontrá-los usando o **Visualizador de Eventos** do Windows em **Logs de Aplicativos e Serviços** > **Gateway de Gerenciamento de Dados**. Enquanto soluciona problemas relacionados ao gateway, procure por eventos de nível de erro no Visualizador de Eventos.
- Se o gateway parar de funcionar depois que você **alterar o certificado**, reinicie (pare e inicie) o **Serviço de Gateway de Gerenciamento de Dados** usando a ferramenta Gerenciador de Configurações do Gateway de Gerenciamento de Dados Microsoft ou o miniaplicativo do painel de controle Serviços. Se você vir um erro, você precisará conceder permissões explícitas para o usuário do serviço de Gateway de Gerenciamento de Dados acessar o certificado no Gerenciador de Certificados (certmgr.msc). A conta de usuário padrão do serviço é: **NT Service\\DIAHostService**.
- Se o aplicativo **Gerenciador de Credenciais** falhar ao **criptografar** credenciais quando você clicar no botão Criptografar no Editor do Data Factory, verifique se está executando esse aplicativo no computador em que o gateway está instalado. Caso contrário, execute o aplicativo no computador do gateway e tente criptografar as credenciais.
- Se você encontrar erros relacionados ao driver ou à conexão com o armazenamento de dados, inicie o **Gerenciador de Configurações do Gateway de Gerenciamento de Dados** no computador do gateway, mude para a guia **Diagnóstico**, selecione/insira valores adequados para os campos no grupo **Testar a conectividade com a fonte de dados local usando este gateway** e clique em **Testar conectividade** para ver se é possível conectar-se à fonte de dados local no computador do gateway usando as informações e as credenciais de conexão. Se a conexão de teste continuar falhando depois que você instalar um driver, reinicie o gateway para que ele assimile a alteração mais recente.

	![Testar conectividade](./media/data-factory-data-management-gateway/TestConnection.png)

### Enviar logs de gateway para a Microsoft
Em casos em que você enfrentar problemas de gateway e precisar entrar em contato com o Suprote da Microsoft, você poderá ser solicitado a compartilhar seus logs do gateway. A versão do gateway permite que você compartilhe facilmente logs do gateway necessários por meio de dois cliques de botão no gerenciador de configuração de gateway.

1. Mudar para a guia **diagnóstico** do gerenciador de configuração de gateway.
 
	![Gateway de Gerenciamento de Dados – guia Diagnóstico](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. Clique no link **Enviar logs** para ver a caixa de diálogo a seguir.

	![Gateway de Gerenciamento de Dados – Enviar logs](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (opcional) Clique em **Exibir logs** para examinar logs no visualizador de eventos.
4. (opcional) Clique em **Privacidade** para examinar a política de privacidade dos serviços online da Microsoft.
3. Quando estiver satisfeito com o que você está prestes a carregar, clique em **Enviar logs** para realmente enviar logs dos últimos 7 dias para a Microsoft para solucionar problemas. Você deve ver o status da operação Enviar logs conforme mostrado abaixo.

	![Gateway de Gerenciamento de Dados – status de Enviar logs](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. Quando a operação for concluída, você verá uma caixa de diálogo conforme mostrado abaixo.
	
	![Gateway de Gerenciamento de Dados – status de Enviar logs](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. Anote a **ID do relatório** e compartilhe-a com o Suporte da Microsoft. A ID do relatório é usada para localizar os logs de gateway carregados para solução de problemas. A ID do relatório também será salva no visualizador de eventos para sua referência. Você pode encontrá-la ao observar a ID do evento "25" e verificar a data e a hora.
	
	![Gateway de Gerenciamento de Dados – ID de relatório de Enviar logs](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### Arquivar logs de gateway no computador host de gateway
Existem alguns cenários em que há problemas de gateway e não é possível compartilhar logs do gateway diretamente:

- Quando os usuários instalarem manualmente o gateway e o registrarem;
- Quando os usuários tentam registrar o gateway com uma chave gerada novamente no gerenciador de configuração;
- Quando os usuários tentam enviar logs, mas o serviço de host do gateway não pode ser conectado;

Nesses casos, você pode salvar os logs de gateway como um arquivo zip e compartilhá-los ao contatar o suporte da Microsoft posteriormente. Por exemplo, se você receber um erro ao registrar o gateway, conforme mostrado abaixo:

![Gateway de gerenciamento de dados – Erro de registro](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

Clique no link **Arquivar logs de gateway** para arquivar e salvar os logs e compartilhar o arquivo zip com o suporte da Microsoft.

![Gateway de Gerenciamento de Dados – Arquivar logs](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## Mover o gateway de um computador para outro
Esta seção fornece etapas para movimentação do cliente do gateway de um computador para outro.

2. No portal, navegue até a **home page do Data Factory** e clique no bloco **Serviços Vinculados**.

	![Link de Gateways de Dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. Selecione seu gateway na seção **GATEWAYS DE DADOS** da folha **Serviços Vinculados**.
	
	![Folha Serviços Vinculados com gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. Na folha **Gateway de dados**, clique em **Baixar e instalar o gateway de dados**.
	
	![Baixar o link do gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. Na folha **Configurar**, clique em **Baixar e instalar o gateway de dados** e siga as instruções para instalar o gateway de dados no computador.

	![Configurar folha](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. Mantenha o **Gerenciador de Configuração de Gateway do Gerenciamento de Dados da Microsoft**
 
	![Gerenciador de Configurações](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. Na folha **Configurar** no portal, clique em **Recriar chave** na barra de comandos e clique em **Sim** para a mensagem de aviso. Clique no **botão copiar** ao lado do texto da chave para copiar a chave para a área de transferência. Observe que o gateway do computador antigo deixará de funcionar assim que você recriar a chave.
	
	![Recriar chave](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. Cole a **chave** na caixa de texto na página **Registrar Gateway** do **Gerenciador de Configuração de Gateway do Gerenciamento de Dados** em seu computador. (Opcional) Clique na caixa de seleção **Mostrar chave do gateway** para ver o texto da chave.
 
	![Copiar chave e Registrar-se](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. Clique em **Registrar** para registrar o gateway no serviço de nuvem.
10. Na guia **Configurações**, clique em **Alterar** para selecionar o mesmo certificado que foi usado com o gateway antigo, insira a **senha** e clique em **Concluir**.
 
	![Especificar Certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	Você pode exportar um certificado do gateway antigo da seguinte maneira: inicie o Gerenciador de Configuração de Gateway do Gerenciamento de Dados na máquina antiga, alterne para a guia **Certificado**, clique em **Exportar** e siga as instruções.
10. Após a conclusão do registro do gateway, você deverá ver o **Registro** definido como **Registrado**, e o **Status** definido como **Iniciado** na Home page do Gerenciador de Configuração de Gateway.

## Configurar credenciais e segurança
Para criptografar credenciais no Editor do Data Factory, faça o seguinte:

1. Inicie o navegador da Web no **computador de gateway**, navegue até o [Portal do Azure](http://portal.azure.com), procure sua data factory, se necessário, abra a data factory na folha **DATA FACTORY** e clique em **Criar e Implantar** para iniciar o Editor de Data Factory.
1. Clique em um **serviço vinculado** existente no modo de exibição de árvore para ver sua definição JSON ou criar um novo serviço vinculado que exija um Gateway de Gerenciamento de Dados (por exemplo: SQL Server ou Oracle).
2. No editor de JSON, para a propriedade **gatewayName**, insira o nome do gateway.
3. Insira o nome do servidor para a propriedade **Data Source** em **connectionString**.
4. Insira o nome do banco de dados para a propriedade **Initial Catalog** em **connectionString**.
5. Clique no botão **Criptografar** na barra de comandos para iniciar o aplicativo de clique único **Gerenciador de Credenciais**. Você verá a caixa de diálogo **Definindo Credenciais**. ![Caixa de diálogo de Configurando Credenciais](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. Na caixa de diálogo **Definindo Credenciais**, faça o seguinte:
	1.	Selecione a **autenticação** que você deseja que o serviço de Data Factory use para se conectar ao banco de dados.
	2.	Insira o nome do usuário que tem acesso ao banco de dados para a configuração **NOME DE USUÁRIO**.
	3.	Insira a senha do usuário para a configuração **SENHA**.
	4.	Clique em **OK** para criptografar credenciais e fechar a caixa de diálogo.
5.	Agora, você verá uma propriedade **encryptedCredential** em **connectionString**.
		
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

Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais possa se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais da fonte de dados teste a conexão à fonte de dados.

Quando você usa o aplicativo **Definindo Credenciais** iniciado pelo Portal do Azure para definir credenciais para uma fonte de dados local, o portal criptografa as credenciais com o certificado especificado na guia **Certificado** do **Gerenciador de Configuração de Gateway de Gerenciamento de Dados** no computador do gateway.

Se você estiver procurando uma abordagem baseada em API para criptografar as credenciais, poderá usar o cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) do PowerShell para criptografar as credenciais. O cmdlet usa o certificado que esse gateway está configurado para usar para criptografar as credenciais. Você pode obter as credenciais criptografadas retornadas por esse cmdlet e adicioná-las ao elemento **EncryptedCredential** de **connectionString** do arquivo JSON que será usado com o cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) ou no trecho de código JSON no Editor do Data Factory no portal.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Há mais uma abordagem para definir as credenciais usando o Editor Data Factory. Se você criar um serviço vinculado SQL Server usando o editor e inserir credenciais em texto sem formatação, as credenciais são criptografadas usando um certificado que é de propriedade do serviço Data Factory, NÃO o certificado que o gateway está configurado para usar. Embora essa abordagem possa ser um pouco mais rápida em alguns casos, ela é menos segura. Portanto, é recomendável que você siga essa abordagem somente para fins de desenvolvimento/teste.


## Cmdlets do PowerShell 
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure.

1. Inicie o **PowerShell do Azure** no modo de administrador.
2. Faça logon na sua conta do Azure executando o seguinte comando e digitando suas credenciais do Azure.

	Login-AzureRmAccount
2. Use o cmdlet **New-AzureRmDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemplo de comando e saída**:


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

	
4. No Azure PowerShell, mude para a pasta **C:\\Arquivos de Programas\\Gateway de Gerenciamento de Dados Microsoft\\1.0\\PowerShellScript** e execute o script **RegisterGateway.ps1** associado à variável local **$Key**, conforme mostrado no comando a seguir, para registrar o agente cliente instalado no computador com o gateway lógico criado anteriormente.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Você pode registrar o gateway em um computador remoto usando o parâmetro IsRegisterOnRemoteMachine. Exemplo:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Você pode usar o cmdlet **Get-AzureRmDataFactoryGateway** para obter a lista de gateways no data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Você pode remover um gateway usando o cmdlet **Remove-AzureRmDataFactoryGateway** e atualizar a descrição de um gateway com os cmdlets **Set-AzureRmDataFactoryGateway**. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.

### Listar gateways usando o PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### Remover gateways usando o PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Próximas etapas
- Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter informações detalhadas sobre o gateway.

<!---HONumber=AcomDC_0803_2016-->