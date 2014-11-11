<properties linkid="dev-java-vm-application-server" urlDisplayName="Tomcat on Virtual Machine" pageTitle="Tomcat on a virtual machine - Azure tutorial" metaKeywords="Azure vm, creating vm Tomcat, configuring vm Tomcat" description="Learn how to create a Windows Virtual machine and configure the machine to run a Apache Tomcat application server." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="How to run a Java application server on a virtual machine" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Como executar um servidor de aplicativos Java em uma máquina virtual

Com o Azure, você pode usar uma máquina virtual para fornecer recursos de servidor. Como um exemplo, uma máquina virtual em execução no Azure pode ser configurada para hospedar um servidor de aplicativos Java, como o Apache Tomcat. Concluindo este guia, você terá um entendimento de como criar uma máquina virtual em execução no Azure e configurá-la para executar um servidor de aplicativos Java.

Você aprenderá a:

-   Criar uma máquina virtual com um JDK já instalado.
-   Fazer logon remotamente na máquina virtual.
-   Instalar um servidor de aplicativos Java na máquina virtual.
-   Criar um ponto de extremidade para a máquina virtual.
-   Abrir uma porta no firewall para o servidor de aplicativos.

Para os objetivos deste tutorial, um servidor de aplicativos Apache Tomcat será instalado em uma máquina virtual. A instalação concluída irá resultar em uma instalação do Tomcat, como a seguinte.

![Máquina virtual executando o Apache Tomcat][Máquina virtual executando o Apache Tomcat]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Para criar uma máquina virtual

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **Nova**, clique em **Computação**, clique em **Máquina virtual** e, em seguida, clique em **Da Galeria**.
3.  Na caixa de diálogo **Seleção de imagem da máquina virtual**, selecione **JDK 7 Windows Server 2012** .
    Observe que o **JDK 6 Windows Server 2012** está disponível caso você tenha aplicativos legados que ainda não estejam prontos para serem executados no JDK 7.
4.  Clique em **Próximo**.
5.  Na caixa de diálogo **Configuração da máquina virtual**:

    1.  Especifique um nome para a máquina virtual.
    2.  Especifique o tamanho a ser usado para a máquina virtual.
    3.  Digite um nome para o administrador no campo **Nome do Usuário**. Lembre-se do nome e da senha que você digitará a seguir, você irá usá-los ao fazer logon remotamente na máquina virtual.
    4.  Digite uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar**. Esta é a senha da conta do Administrador.
    5.  Clique em **Próximo**.

6.  Na próxima caixa de diálogo **Configuração da máquina virtual**:

    1.  Para **Serviço de Nuvem**, use o padrão **Criar um novo serviço de nuvem**.
    2.  O valor de **Nome DNS do Serviço de Nuvem** deve ser exclusivo no cloudapp.net. Se necessário, modifique esse valor para que o Azure indique que seja exclusivo.
    3.  Especifique uma região, um grupo de afinidade ou uma rede virtual. Para o objetivo deste tutorial, especifique uma região, como **Oeste dos Estados Unidos**.
    4.  Para **Conta de Armazenamento**, selecione **Usar uma conta de armazenamento gerada automaticamente**.
    5.  Para **Conjunto de Disponibilidade**, selecione **(Nenhuma)**.
    6.  Clique em **Próximo**.

7.  Na caixa de diálogo **Configuração da máquina virtual** final:

    1.  Aceite as entradas de ponto de extremidade padrão.
    2.  Clique em **Concluído**.

## Para fazer logon remotamente na máquina virtual

1.  Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento do Azure].
2.  Clique em **Máquinas Virtuais**.
3.  Clique no nome da máquina virtual na qual você deseja fazer logon.
4.  Clique em **Conectar**.
5.  Responda às solicitações conforme necessário para se conectar à máquina virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a máquina virtual.

## Para instalar um servidor de aplicativos Java em sua máquina virtual

Você pode copiar um servidor de aplicativos Java em sua máquina virtual ou instalar um servidor de aplicativos Java por meio de um instalador.

Para o objetivo deste tutorial, o Tomcat será instalado.

1.  Enquanto você estiver conectado à máquina virtual, abra uma sessão do navegador para <http://tomcat.apache.org/download-70.cgi>.
2.  Clique duas vezes no link **Instalador de serviço do Windows de 32 bits/64 bits**. Usando essa técnica, o Tomcat será instalado como um serviço do Windows.
3.  Quando solicitado, opte por executar o instalador.
4.  No assistente de **Configuração do Apache Tomcat**, siga os prompts para instalar o Tomcat. Para o objetivo deste tutorial, aceitar os padrões é suficiente. Quando você chegar à caixa de diálogo **Concluindo o Assistente de instalação do Apache Tomcat**, poderá marcar opcionalmente **Executar o Apache Tomcat**, para que o Tomcat seja iniciado agora. Clique em **Concluir** para concluir o processo de configuração do Tomcat.

## Para iniciar o Tomcat

Se você não optou por executar o Tomcat na caixa de diálogo **Concluindo o Assistente de Instalação do Apache Tomcat**, inicie-o abrindo um prompt de comando em sua máquina virtual e executando **net start-Tomcat7**.

Agora você deverá ver o Tomcat em execução se executar o navegador da máquina virtual e abrir <http://localhost:8080>.

Para ver o Tomcat em execução em máquinas externas, você precisará criar um ponto de extremidade e abrir uma porta.

## Para criar um ponto de extremidade para sua máquina virtual

1.  Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento do Azure].
2.  Clique em **Máquinas Virtuais**.
3.  Clique no nome da máquina virtual que está executando o servidor de aplicativos Java.
4.  Clique em **Pontos de Extremidade**.
5.  Clique em **Adicionar**.
6.  Na caixa de diálogo **Adicionar ponto de extremidade**, verifique se a opção **Adicionar ponto de extremidade autônomo** está selecionada e clique em **Avançar**.
7.  Na caixa de diálogo **Detalhes do novo ponto de extremidade**:

    1.  Especifique um nome para o ponto de extremidade. Por exemplo, **HttpIn**.
    2.  Especifique **TCP** para o protocolo.
    3.  Especifique **80** para a porta pública.
    4.  Especifique **8080** para a porta privada.
    5.  Clique no botão **Concluído** para fechar a caixa de diálogo. O ponto de extremidade será criado.

## Para abrir uma porta no firewall para sua máquina virtual

1.  Faça logon na máquina virtual.
2.  Clique em **Iniciar do Windows**.
3.  Clique em **Painel de Controle**.
4.  Clique em **Sistema e Segurança**, clique em **Firewall do Windows** e, em seguida, clique em **Configurações Avançadas**.
5.  Clique em **Regras de Entrada** e, em seguida, clique em **Nova Regra**.

![Nova regra de entrada][Nova regra de entrada]

1.  Para a nova regra, selecione **Porta** para o **Tipo de Regra** e, em seguida, clique em **Avançar**.

![Nova porta de regra de entrada][Nova porta de regra de entrada]

1.  Selecione **TCP** para o protocolo e especifique **8080** para a porta e clique em **Avançar**.

![Nova regra de entrada][1]

1.  Escolha **Permitir a conexão** e, em seguida, clique em **Avançar**.

![Nova ação de regra de entrada][Nova ação de regra de entrada]

1.  Verifique se **Domínio**, **Particular** e **Público** estão selecionados para o perfil e clique em **Avançar**.

![Novo perfil de regra de entrada][Novo perfil de regra de entrada]

1.  Especifique um nome para a regra, como **HttpIn** (no entanto, o nome da regra não precisa corresponder ao nome do ponto de extremidade) e clique em **Concluir**.

![Nome da nova regra de entrada][Nome da nova regra de entrada]

Neste ponto, o site do Tomcat deve ser visível de um navegador externo usando uma URL no formato **http://_your\_DNS\_name_.cloudapp.net**, em que ***your\_DNS\_name*** é o nome DNS que você especificou ao criar a máquina virtual.

## Considerações sobre o ciclo de vida do aplicativo

-   Você pode criar o próprio arquivo web do aplicativo (WAR) e adicioná-lo à pasta **webapps**. Por exemplo, crie um projeto Web dinâmico JSP (página de serviço Java) básico e o exporte como um arquivo WAR, copie o WAR para a pasta **webapps** do Apache Tomcat na máquina virtual e o execute em um navegador.
-   Por padrão, quando o serviço Tomcat é instalado, ele será definido para iniciar manualmente. Você pode mudá-lo para iniciar automaticamente, usando o snap-in Serviços. Inicie o snap-in Serviços clicando em **Iniciar do Windows**, **Ferramentas Administrativas** e em **Serviços**. Para definir o Tomcat para iniciar automaticamente, clique duas vezes no serviço **Apache Tomcat** no snap-in Serviços e defina **Tipo de Inicialização** como **Automática**, conforme mostrado a seguir.

    ![Configurando um serviço para iniciar automaticamente][Configurando um serviço para iniciar automaticamente]

    O benefício de fazer o Tomcat ser iniciado automaticamente é que ele será iniciado novamente se a máquina virtual for reinicializada (por exemplo, depois que atualizações de software que exijam uma reinicialização forem instaladas).

## Próximas etapas

-   Saiba mais sobre outros serviços, como o Armazenamento do Azure, o Barramento de Serviço, o Banco de Dados SQL e muito mais, que convém incluir com os aplicativos Java, exibindo as informações disponíveis em <http://www.windowsazure.com/pt-br/develop/java/>.

  [Máquina virtual executando o Apache Tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Nova regra de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
  [Nova porta de regra de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
  [1]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
  [Nova ação de regra de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
  [Novo perfil de regra de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png
  [Nome da nova regra de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
  [Configurando um serviço para iniciar automaticamente]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png
