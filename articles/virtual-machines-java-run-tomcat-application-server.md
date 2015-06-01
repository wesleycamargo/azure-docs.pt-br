<properties 
	pageTitle="Tomcat em uma máquina virtual - tutoria do Azure" 
	description="Saia como criar uma máquina virtual Windows e configurá-la para executar um servidor de aplicativos Apache Tomcat." 
	services="virtual-machines" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Como executar um servidor de aplicativos Java em uma máquina virtual

Com o Azure, você pode usar uma máquina virtual para fornecer recursos de servidor. Como um exemplo, uma máquina virtual em execução no Azure pode ser configurada para hospedar um servidor de aplicativos Java, como o Apache Tomcat. Concluindo este guia, você terá um entendimento de como criar uma máquina virtual em execução no Azure e configurá-la para executar um servidor de aplicativos Java.

Você aprenderá:

* Como criar uma máquina virtual com um JDK já instalado.
* Como fazer logon remotamente na máquina virtual.
* Como instalar um servidor de aplicativos Java na máquina virtual.
* Como criar um ponto de extremidade para a máquina virtual.
* Como abrir uma porta no firewall para o servidor de aplicativos.

Para os objetivos deste tutorial, um servidor de aplicativos Apache Tomcat será instalado em uma máquina virtual. A instalação concluída resultará em uma instalação do Tomcat, como a seguinte.

![Virtual machine running Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Para criar uma máquina virtual

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Nova**, clique em **Computação**, clique em **Máquina virtual** e, em seguida, clique em **Da Galeria**.
3. Na caixa de diálogo **Seleção de imagem da máquina virtual**, selecione **JDK 7 Windows Server 2012**.
Observe que o **JDK 6 Windows Server 2012** está disponível caso você tenha aplicativos legados que ainda não estejam prontos para serem executados no JDK 7.
4. Clique em **Avançar**.
5. Na <strong>Configuração de máquina virtual</strong> :
    1. Especifique um nome para a máquina virtual.
    2. Especifique o tamanho a ser usado para a máquina virtual.
    3. Digite um nome para o administrador no campo **Nome de Usuário**. Lembre-se do nome e da senha que você digitará a seguir, você vai usá-los ao fazer logon remotamente na máquina virtual.
    4. Digite uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar**. Esta é a senha da conta do Administrador.
    5. Clique em **Avançar**.
6. No próximo diálogo <strong>Configuração de máquina virtual</strong> :
    1. Para **Serviço de Nuvem**, use o padrão **Criar um novo serviço de nuvem**.
    2. O valor de **Nome DNS do Serviço de Nuvem** deve ser exclusivo no cloudapp.net. Se necessário, modifique esse valor para que o Azure indique que ele é exclusivo.
    2. Especifique uma região, um grupo de afinidade ou uma rede virtual. Para o objetivo deste tutorial, especifique uma região, como **Oeste dos Estados Unidos**.
    2. Para **Conta de Armazenamento**, selecione **Usar uma conta de armazenamento gerada automaticamente**.
    3. Para **Conjunto de Disponibilidade**, selecione **(Nenhuma)**.
    4. Clique em **Avançar**.
7. No diálogo final <strong>Configuração de máquina virtual</strong> :
    1. Aceite as entradas de ponto de extremidade padrão.
    2. Clique em **Concluído**.

## Para fazer logon remotamente na máquina virtual

1. Faça logon no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da máquina virtual na qual você deseja fazer logon.
4. Depois que a máquina virtual for iniciada, um menu pop-up aparecerá na parte inferior da página para permitir as conexões.
5. Clique em **Conectar**.
6. Responda às solicitações conforme necessário para se conectar à máquina virtual. Isso deve incluir salvar ou abrir o arquivo .rdp que contém os detalhes de conexão. Pode ser necessário copiar url:port como último parte da primeira linha do arquivo .rdp e colar essa informações em um aplicativo de logon remoto.

## Para instalar um servidor de aplicativos Java em sua máquina virtual

Você pode copiar um servidor de aplicativos Java em sua máquina virtual ou instalar um servidor de aplicativos Java por meio de um instalador. 

Para o objetivo deste tutorial, o Tomcat será instalado.

1. Enquanto você estiver conectado à máquina virtual, abra uma sessão do navegador para <http://tomcat.apache.org/download-70.cgi>.
2. Clique duas vezes no link **Instalador de serviço Windows de 32 bits/64 bits**. Usando essa técnica, o Tomcat será instalado como um serviço Windows.
3. Quando solicitado, opte por executar o instalador.
4. No assistente de **Configuração do Apache Tomcat**, siga os prompts para instalar o Tomcat. Para o objetivo deste tutorial, aceitar os padrões é suficiente. Quando você chegar à caixa de diálogo **Concluindo o Assistente de instalação do Apache Tomcat**, poderá marcar opcionalmente **Executar o Apache Tomcat**, para que o Tomcat seja iniciado agora. Clique em **Concluir** para concluir o processo de configuração do Tomcat.

## Para iniciar o Tomcat
Se você não optou por executar o Tomcat na caixa de diálogo **Concluindo o Assistente de Instalação do Apache Tomcat**, inicie-o abrindo um prompt de comando em sua máquina virtual e executando **net start-Tomcat7**.

Agora você deverá ver o Tomcat em execução se executar o navegador da máquina virtual e abrir <http://localhost:8080>.

Para ver o Tomcat em execução em máquinas externas, você precisará criar um ponto de extremidade e abrir uma porta.

## Para criar um ponto de extremidade para sua máquina virtual
1. Faça logon no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da máquina virtual que está executando o servidor de aplicativos Java.
4. Clique em **Pontos de Extremidade**.
5. Clique em **Adicionar**.
6. Na caixa de diálogo **Adicionar ponto de extremidade**, verifique se a opção **Adicionar ponto de extremidade autônomo** está selecionada e clique em **Avançar**.
7. Na caixa de diálogo <strong>Detalhes do novo ponto de extremidade</strong> :
    1. Especifique um nome para o ponto de extremidade. Por exemplo, **HttpIn**.
    2. Especifique **TCP** para o protocolo.
    3. Especifique **80** para a porta pública.
    4. Especifique **8080** para a porta privada.
    5. Clique no botão **Concluído** para fechar a caixa de diálogo. O ponto de extremidade será criado.

## Para abrir uma porta no firewall para sua máquina virtual
1. Faça logon na máquina virtual.
2. Clique em **Iniciar do Windows**.
3. Clique em **Painel de Controle**.
4. Clique em **Sistema e Segurança**, clique em **Firewall do Windows** e, em seguida, clique em **Configurações Avançadas**.
5. Clique em **Regras de Entrada** e, em seguida, clique em **Nova Regra**.

 ![New inbound rule][NewIBRule]

6. Para a nova regra, selecione **Porta** para o **Tipo de Regra** e, em seguida, clique em **Avançar**.

 ![New inbound rule port][NewRulePort]

7. Selecione **TCP** para o protocolo e especifique **8080** para a porta e clique em **Avançar**.

 ![New inbound rule ][NewRuleProtocol]

8. Escolha **Permitir a conexão** e, em seguida, clique em **Avançar**.

 ![New inbound rule action][NewRuleAction]

9. Verifique se **Domínio**, **Particular** e **Público** estão selecionados para o perfil e clique em **Avançar**.

 ![New inbound rule profile][NewRuleProfile]

10. Especifique um nome para a regra, como **HttpIn** (no entanto, o nome da regra não precisa corresponder ao nome do ponto de extremidade) e clique em **Concluir**.  

 ![New inbound rule name][NewRuleName]

A esta altura, o site do Tomcat deve ser visível em um navegador externo usando uma URL no formato **http://*seu_DNS_usado*.cloudapp.net**, onde ***seu_DNS_usado***, é o nome DNS que você especificou ao criar a máquina virtual.

## Considerações sobre o ciclo de vida do aplicativo
* Você pode criar o próprio arquivo Web do aplicativo (WAR) e adicioná-lo à pasta **webapps**. Por exemplo, crie um projeto Web dinâmico JSP (página de serviço Java) básico e o exporte como um arquivo WAR, copie o WAR para a pasta **webapps** do Apache Tomcat na máquina virtual e o execute em um navegador.
* Por padrão, quando o serviço Tomcat é instalado, ele será definido para iniciar manualmente. Você pode mudá-lo para iniciar automaticamente, usando o snap-in Serviços. Inicie o snap-in Serviços clicando em **Iniciar do Windows, ****Ferramentas Administrativas** e em **Serviços**. Para definir o Tomcat para iniciar automaticamente, clique duas vezes no serviço **Apache Tomcat** no snap-in Serviços e defina **Tipo de Inicialização** como **Automática**, conforme mostrado a seguir.

    ![Setting a service to start automatically][service_automatic_startup]

    O benefício de fazer o Tomcat ser iniciado automaticamente é que ele será iniciado novamente se a máquina virtual for reinicializada (por exemplo, depois que atualizações de software que exijam uma reinicialização forem instaladas).

## Próximas etapas
* Saiba mais sobre outros serviços, como o Armazenamento do Azure, o Barramento de Serviço, o Banco de Dados SQL e muito mais, que convém incluir com os aplicativos Java, exibindo as informações disponíveis em <http://www.windowsazure.com/develop/java/>.

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!--HONumber=47-->
