<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="Como criar uma implantação híbrida do RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#Como criar uma implantação híbrida do RemoteApp

Há dois tipos de implantação do RemoteApp: 

- Nuvem: reside completamente no Azure e é criada usando a opção **Criação rápida** no Portal de Gerenciamento do Azure.  
- Híbrida: inclui uma rede virtual para acesso local e é criada usando a opção **Criar com VPN** no Portal de Gerenciamento.

Este tutorial explica o processo de criação de uma implantação híbrida. Há sete etapas: 

1.	Crie uma [imagem de modelo personalizada para o RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/).
2.	Criar um serviço RemoteApp.
2.	Vincular a uma rede virtual.
3.	Conectar-se a uma imagem do modelo.
4.	Configurar a sincronização do diretório. O RemoteApp requer que você integre ao Active Directory do Azure 1) configurando o DirSync com a opção de sincronização de senha, ou 2) configurando o DirSync sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS.
5.	Publicar os programas do RemoteApp.
6.	Configurar o acesso do usuário.

**Antes de começar**

É necessário fazer o seguinte antes de criar o serviço:

- Inscreva-se para a visualização do RemoteApp. É possível fazer isso em [http://azure.microsoft.com/pt-br/services/remoteapp/](http://azure.microsoft.com/pt-br/services/remoteapp/).
- Crie uma conta de usuário no Active Directory para usar como a conta de serviço do RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio.
- Reúna as informações sobre a sua rede local: As informações de endereço IP e detalhes do dispositivo VPN.
- Instale o módulo [PowerShell do Azure](http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/).
- Reúna as informações sobre os usuários e os grupos aos quais deseja permitir acesso. Podem ser informações da conta da Microsoft ou da conta corporativa do Active Directory para usuários ou grupos.
- Crie sua imagem de modelo. Uma imagem de modelo do RemoteApp contém os aplicativos e programas que você deseja publicar para seus usuários. Consulte [Como criar uma imagem do modelo personalizada para o RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/) para ver as etapas detalhadas. 






## **Etapa 1: Criar um serviço RemoteApp** ##



1. No [Portal de Gerenciamento do Azure](http://manage.windowsazure.com), vá para a página do RemoteApp.
2. Clique em **Novo > Criar com VPN**.
3. Digite um nome para o seu serviço e clique em **Criar o serviço RemoteApp**.

Após a criação do serviço RemoteApp, vá para a página **Início Rápido** do RemoteApp para continuar as etapas de configuração.

## **Etapa 2: Conectar-se a uma rede virtual** ##

Uma rede virtual permite que os seus usuários acessem os dados da sua rede local através dos recursos remotos do RemoteApp. Há quatro etapas para a configuração do seu link de rede virtual:

1. Na página de Início Rápido, clique em **Conectar-se a uma rede virtual do RemoteApp**. 
2. Opte por criar uma nova rede virtual ou usar uma existente. Para este tutorial, será criada uma nova rede.
3. Forneça as seguintes informações para a sua rede:  
      - Name
      - Espaço de endereço da rede virtual
      - Espaço de endereço local
      - Endereço IP do servidor DNS
      - Endereço IP do VPN

	Consulte [Configurar um VPN site a site no Portal de Gerenciamento](http://msdn.microsoft.com/library/azure/dn133795.aspx) para obter mais informações.

4. Em seguida, de volta à página de Início Rápido, clique em **obter script** para baixar um script para a configuração do seu dispositivo VPN para conectar-se à rede virtual recém-criada. As informações sobre o dispositivo VPN a seguir serão necessárias: 
	- Fornecedor
	- Plataforma
	- Sistema operacional

	Salve o script e execute-o no dispositivo VPN. 

	**Observação:** Após a execução deste script, a rede virtual mudará para o estado Pronto - o que pode demorar de 5 à 7 minutos. Até que a rede esteja pronta, não será possível usá-la.

5. Por fim, novamente na página Início Rápido, clique em **inscrever-se no domínio local**. Adicione a conta de serviço RemoteApp ao domínio do Active Directory local. Serão necessários o nome do domínio, a unidade organizacional, o nome de usuário da conta de serviço e a senha.


## **Etapa 3: Conectar-se a uma imagem do modelo do RemoteApp** ##

Uma imagem do modelo do RemoteApp contém os programas que deseja compartilhar com os usuários. É possível carregar a nova imagem do modelo criado (das instruções em [Como criar uma imagem do modelo personalizada para o RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/)) ou conectar a uma imagem existente (uma já carregada no Azure).

Se estiver fazendo o upload da nova imagem, será necessário inserir o nome e escolher o local para a imagem. Na próxima página do assistente, você verá um conjunto de cmdlets do PowerShell - copie e execute esses cmdlets em um prompt elevado do Windows PowerShell para fazer o upload da imagem específica.

Se estiver conectando-se a uma imagem do modelo existente, especifique o nome da imagem, o local e a assinatura do zure associada.



## **Etapa 4: Configurar a sincronização de diretório do Active Directory** ##

O RemoteApp requer que você integre ao Active Directory do Azure 1) configurando o DirSync com a opção de sincronização de senha, ou 2) configurando o DirSync sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS. Consulte o [Roteiro de sincronização do diretório](http://msdn.microsoft.com//library/azure/hh967642.aspx) para as informações de planejamento e etapas detalhadas.

## **Etapa 5: Publicar os programas do RemoteApp** ##

Um programa do RemoteApp é um aplicativo ou um programa fornecido para os seus usuários. Ele está localizado na imagem do modelo no qual foi feito o upload do serviço. Quando o usuário acessa um programa do RemoteApp, o programa parece estar em execução no seu ambiente local mas, na verdade, está em execução no Azure. 

Antes que os seus usuários possam acessar os programas do RemoteApp, é necessário publicá-los nos comentários do usuário final - uma lista de programas disponíveis que os seus usuários acessam através do portal do Azure.
 
É possível publicar vários programas em seu serviço do RemoteApp. Na página de prohramas do RemoteApp, clique em **Publicar** para adicionar um programa. É possível publicar no menu Iniciar da imagem do modelo ou especificar o caminho na imagem do modelo para o programa. Se optar por adicionar a partir do menu Iniciar, escolha o programa para publicar. Se optar por fornecer o caminho para o programa, forneça um nome para o programa e o caminha no qual o programa está instalado na imagem do modelo.

## **Etapa 6: Configurar o acesso do usuário** ##

Agora que o seu serviço do RemoteApp foi criado, é necessário adicionar os usuários e grupos que deseja habilitar para usar os seus recursos remotos. Os usuários ou grupos que possuem acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar este serviço do RemoteApp.

1.	Na página Início Rápido, clique em **Configurar o acesso do usuário**. 
2.	Digite a conta corporativa ou o nome do grupo (do Active Directory) ou a conta da Microsoft para a qual deseja conceder acesso.

Para os usuários, certifique-se de que você use o formato "user@domain.com". Para grupos, digite o nome do grupo.

3.	Uma vez que os usuários ou grupos forem validades, clique em **Salvar**.


## Próximas etapas ##
É isso - a sua implantação híbrida do RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL para o cliente na página Início Rápido do RemoteApp. Em seguida, faça com que os usuários façam logon no cliente e acessem os programas do RemoteApp publicados.


