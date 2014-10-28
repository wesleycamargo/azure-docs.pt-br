<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Como criar uma implantação híbrida do RemoteApp

Há dois tipos de implantação do RemoteApp:

-   Nuvem: reside completamente no Azure e é criada usando a opção **Criação rápida** no Portal de Gerenciamento do Azure.
-   Híbrida: inclue uma rede virtual para acesso local e é criada usando a opção **Criar com VPN** no Portal de Gerenciamento.

Este tutorial explica o processo de criação de uma implantação híbrida. Há sete etapas:

1.  Criar uma imagem do modelo do RemoteApp.
2.  Criar um serviço RemoteApp.
3.  Vincular a uma rede virtual.
4.  Conectar-se a uma imagem do modelo.
5.  Configurar a sincronização do diretório. O RemoteApp exige esta sincronização de usuário, grupos, contatos e senhas do seu Active Directory local para o seu locatário do Active Directory do Azure.
6.  Publicar os programas do RemoteApp.
7.  Configurar o acesso do usuário.

**Antes de começar**

É necessário fazer o seguinte antes de criar o serviço:

-   Instalar as [atualizações necessárias][atualizações necessárias] para melhorar o desempenho do RemoteApp do Azure.
-   Increva-se para a [visualização do RemoteApp][visualização do RemoteApp].
-   Crie uma conta de usuário no Active Directory para usar como a conta de serviço do RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio.
-   Reúna as informações sobre a sua rede local: As informações de endereço IP e detalhes do dispositivo VPN.
-   Instale o módulo [PowerShell do Azure][PowerShell do Azure].
-   Reúna as informações sobre os usuários e os grupos aos quais deseja permitir acesso. Pode ser informações da conta da Microsoft ou da conta organizacional do Active Directory para usuários e grupos.

## **Etapa 1: Criar uma imagem do modelo**

O RemoteApp do Azure usa uma imagem do modelo do Windows Server 2012 R2 para hospedar todos os programas que deseja compartilhar com os seus usuários. Para criar uma imagem do modelo do RemoteApp personalizada, é possível iniciar com uma imagem existente ou criar uma nova. Os requisitos para a imagem passiva de upload para o uso com o RemoteApp do Azure são:

-   Deve estar em um arquivo VHD (arquivos VHDX atualmente não têm suporte).
-   O VHD podem ter tamanho fixo ou expandir dinamicamente. O VHD de expansão dinâmica é recomendado, pois demora menos para fazer o upload do Azure que o arquivo VHD de tamanho fixo.
-   O disco deve ser inicializado usando o estilo de particionamento do Registro mestre de inicialização (MBR). O estilo de particionamento da tabela de partição GUID (GPT) não tem suporte.
-   O VHD deve conter uma instalação única do Windows Server 2012 R2. Ele deve conter vários volumes, mas apenas um que contenha uma instalação do Windows.
-   A função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience devem estar instalados.
-   O Encrypting File System (EFS) deve estar desabilitado.
-   A imagem deve ser SYSPREPed usando os parâmetros **/oobe /generalize /shutdown** (NÃO use o parâmetro **/mode:vm**).

Para criar uma nova imagem do modelo do zero:

1.  Localize um DVD do Windows Server 2012 R2 ou a imagem ISO.
2.  Crie um arquivo VHD.
3.  Instale o Windows Server 2012 R2.
4.  Instale a função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience.
5.  Instale os recursos adicionais necessários pelos seus aplicativos.
6.  Instale e configure os seus aplicativos.
7.  Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
8.  Desabilite o Encrypting File System (EFS).
9.  SYSPREP a imagem.

As etapas detalhadas para a criação de uma nova imagem são:

1.  Localize um DVD do Windows Server 2012 R2 ou a imagem ISO.
2.  Crie um arquivo VHD usando o Disk Management.

    1.  Inicialize o Disk Management (diskmgmt.msc).
    2.  Crie um VHD de expansão dinâmica com 40 GB ou com tamanho maior. (Calcule a quantia de espaço necessário para o Windows, os seus aplicativos e as suas personalizações. O Windows Server com a função RDSH e o recurso Desktop Experience instalados precisará de cerca de 10 GB de espaço).

        1.  Clique em **Ação \> Criar VHD**.
        2.  Especifique a localização, o tamanho e o formato do VHD. Selecione **Expandir dinamicamente** e, em seguida, clique em **OK**.

            A execução demorará vários segundos. Quando a criação do VHD estiver concluída, será possível ver um novo disco sem qualquer letra da unidade e no estado “Não inicializado” no console do Disk Management.

        -   Clique com o botão direito do mouse no disco (não no espaço não alocado) e, em seguida, clique em **Inicializar disco**. Selecione **MBR** (Registro mestre de iniciação) como o estilo de partição e, em seguida, clique em **OK**.
        -   Criar um novo volume: clique com o botão direito do mouse no espaço não alocado e, em seguida, clique em **Novo volume simples**. É possível aceitar os padrões no assistente, mas certifique-se de que tenha designado uma letra da unidade para evitar problemas em potencial quando for fazer o upload da imagem do modelo.
        -   Clique com o botão direito do mouse no disco e clique em **Desanexar VHD**.

3.  Instalar o Windows Server 2012 R2:

    1.  Criar uma nova máquina virtual. Use o Novo assistente da máquina virtual no Hyper-V Manager ou no Client Hyper-V.

        1.  Na página Conectar o disco virtual, selecione **Usar um disco virtual existente** e procure pelo VHD criado na etapa anterior.
        2.  Na página Opções de instalação, selecione **Instalar um sistema operacional de um CD/DVD\_ROM de inicialização** e, em seguida, selecione o local da mídia de instalação do seu Windows Server 2012 R2.
        3.  Escolha outras opções necessárias no assistente para instalar o Windows e os seus aplicativos. Conclua o assistente.

    2.  Após a conclusão do assistente, edite as configurações da VM e faça outras alterações necessárias para instalar o Windows e os seus programas, como o número de processadores virtuais, e clique em **OK**.
    3.  Conecte-se à VM e instale o Windows Server 2012 R2.

4.  Instale a função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience:

    1.  Inicialize o Gerenciador do Servidor.
    2.  Clique em **Adicionar funções e recursos** na tela Bem-vindo ou do menu **Gerenciar**.
    3.  Clique em **Avançar** na página Antes de começar.
    4.  Selecione **Instalação baseada em função ou recurso** e clique em **Avançar**.
    5.  Selecione o computador local na lista e clique em **Avançar**.
    6.  Selecione **Serviços da Área de Trabalho Remota** e clique em **Avançar**.
    7.  Expanda **Interfaces e Infraestrutura do Usuário** e selecione **Desktop Experience**.
    8.  Clique em **Adicionar recursos** e clique em **Avançar**.
    9.  Na página Serviços da Área de Trabalho Remota e clique em **Avançar**.
    10. Clique em **Host da Sessão da Área de Trabalho Remota**.
    11. Clique em **Adicionar recursos** e clique em **Avançar**.
    12. Na página de Confirmar as seleções de instalação, selecione **Reiniciar o servidor de destino automaticamente, se necessário** e, em seguida, clique em **Sim** no aviso de reinício.
    13. Clique em **Instalar**. O computador será reiniciado.

5.  Instale os recursos adicionais necessários pelos seus aplicativos, como .NET Framework 3.5. Para instalar os recursos, execute Adicionar funções e Assistente de recursos.
6.  Instale e configure os programas e os aplicativos que deseja publicar através do RemoteApp.

    **Importante:** A Microsoft recomenda instalar a função RDSH antes da instalação dos aplicativos para garantir que quasiquer problemas de compatibilidade do aplicativo sejam descobertos antes de fazer o upload da imagem no RemoteApp.

7.  Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
8.  Desabilite o Encrypting File System (EFS). Execute o comando a seguir em uma janela de comandos com privilégios elevados:

        Fsutil behavior set disableencryption 1

    Alternativamente, é possível configurar ou adicionar o valor DWORD a seguir no Registro:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Se estiver construindo a sua imagem em uma **Máquina Virtual do Azure**, será necessário excluir ou renomear o arquivo **\\Windows\\Panther\\Unattend.xml**, já que este arquivo bloqueará o upload do script usado posteriormente do trabalho
10. SYSPREP a imagem. Em um prompt de comandos com privilégios elevados, execute o seguinte comando:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Observação:** Não use o comutador **/mode:vm** do comando SYSPREP, mesmo que seja uma máquina virtual.

## **Etapa 2: Crie um serviço RemoteApp**

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], vá para a página do RemoteApp.
2.  Clique em **Novo \> Criar com VPN**.
3.  Digite um nome para o seu serviço e clique em **Criar o serviço RemoteApp**.

Após a criação do serviço RemoteApp, vá para a página **Início Rápido** do RemoteApp para continuar as etapas de configuração.

## **Etapa 3: Conectar-se a uma rede local**

Uma rede virtual permite que os seus usuários acessem os dados da sua rede local através dos recursos remotos do RemoteApp. Há quatro etapas para a configuração do seu link de rede virtual:

1.  Na página de Início Rápido, clique em **Conectar-se a uma rede virtual do RemoteApp**.
2.  Opte por criar uma nova rede virtual ou usar uma existente. Para este tutorial, será criada uma nova rede.
3.  Forneça as seguintes informações para a sua rede:

    -   Nome
    -   Espaço de endereço da rede virtual
    -   Espaço de endereço local
    -   Endereço IP do servidor DNS
    -   Endereço IP do VPN

    Consulte [Configurar um VPN site a site no Portal de Gerenciamento][Configurar um VPN site a site no Portal de Gerenciamento] para obter mais informações.

4.  Em seguida, de volta à página de Início Rápido, clique em **obter script** para baixar um script para a configuração do seu dispositivo VPN para conectar-se à rede virtual recém-criada. As informações sobre o dispositivo VPN a seguir serão necessárias:

    -   Fornecedor
    -   Plataforma
    -   Sistema operacional

    Salve o script e execute-o no dispositivo VPN.

    **Observação:** Após a execução deste script, a rede virtual mudará para o estado Pronto - o que pode demorar de 5 à 7 minutos. Até que a rede esteja pronta, não será possível usá-la.

5.  Finalmente, novamente na página Início Rápido, clique em **increver-se no domínio local**. Adicione a conta de serviço RemoteApp ao domínio do Active Directory local. Serão necessários o nome do domínio, a unidade organizacional, o nome do usuário da conta de serviço e a senha.

## **Etapa 4: Conecte-se a uma imagem do modelo do RemoteApp**

Uma imagem do modelo do RemoteApp contém os programas que deseja compartilhar com os usuários. É possível fazer o upload da nova imagem do modelo criada no dia 1º de setembro ou conectar-se a uma imagem existente (a qual já foi feito o upload no Azure).

Se estiver fazendo o upload da nova imagem, será necessário inserir o nome e escolher o local para a imagem. Na próxima página do assistente, você verá um conjunto de cmdlets do PowerShell - copie e execute esses cmdlets em um prompt elevado do PowerShell do Azure para fazer o upload da imagem específica.

Se estiver conectando-se a uma imagem do modelo existente, especifique o nome da imagem, o local e a assinatura do zure associada.

## **Etapa 5: Configurar a sincronização de diretório do Active Directory**

O RemoteApp exige a sincronização de diretório entre o Active Directory do Azure e o Active Directory local para sincronizar usuários, grupos, contatos e senhas com o seu locatário do Active Directory do Azure. Consulte o [Roteiro de sincronização do diretório][Roteiro de sincronização do diretório] para as informações de planejamento e etapas detalhadas.

## **Etapa 6: Publicar os programas do RemoteApp**

Um programa do RemoteApp é um aplicativo ou um programa fornecido para os seus usuários. Ele está localizado na imagem do modelo no qual foi feito o upload do serviço. Quando o usuário acessa um programa do RemoteApp, o programa parece estar em execução no seu ambiente local mas, na verdade, está em execução no Azure.

Antes que os seus usuários possam acessar os programas do RemoteApp, é necessário publicá-los nos comentários do usuário final – uma lista de programas disponíveis que os seus usuários acessam através do portal do Azure.

É possível publicar vários programas em seu serviço do RemoteApp. Na página de prohramas do RemoteApp, clique em **Publicar** para adicionar um programa. É possível publicar no menu Iniciar da imagem do modelo ou especificar o caminho na imagem do modelo para o programa. Se optar por adicionar a partir do menu Iniciar, escolha o programa para publicar. Se optar por fornecer o caminho para o programa, forneça um nome para o programa e o caminha no qual o programa está instalado na imagem do modelo.

## **Etapa 7: Configurar o acesso do usuário**

Agora que o seu serviço do RemoteApp foi criado, é necessário adicionar os usuários e grupos que deseja habilitar para usar os seus recursos remotos. Os usuários ou grupos que possuem acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar este serviço do RemoteApp.

1.  Na página Início Rápido, clique em **Configurar o acesso do usuário**.
2.  Digite a conta organizacional ou o nome do grupo (do Active Directory) ou a conta da Microsoft que deseja liberar o acesso.
	Para os usuários, certifique-se de usar o formato “usuário@domínio.com”. Para grupos, digite o nome do grupo.

    </p>
3.  Uma vez que os usuários ou grupos forem validades, clique em **Salvar**.

## Próximas etapas

É isso - a sua implantação híbrida do RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL para o cliente na página Início Rápido do RemoteApp. Em seguida, faça com que os usuários façam logon no Azure e acessem os programas do RemoteApp publicados.

  [atualizações necessárias]: http://support.microsoft.com/kb/2977219
  [visualização do RemoteApp]: http://azure.microsoft.com/pt-br/services/remoteapp/
  [PowerShell do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Configurar um VPN site a site no Portal de Gerenciamento]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [Roteiro de sincronização do diretório]: http://msdn.microsoft.com//library/azure/hh967642.aspx
