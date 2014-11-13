<properties title="Como criar uma implanta&ccedil;&atilde;o de nuvem do RemoteApp" pageTitle="Como criar uma implanta&ccedil;&atilde;o de nuvem do RemoteApp" description="Saiba como criar uma implanta&ccedil;&atilde;o de nuvem do RemoteApp que salva dados na nuvem do Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

# Como criar uma implantação de nuvem do RemoteApp

Há dois tipos de implantação do RemoteApp:

-   Nuvem: reside completamente no Azure e é criada usando a opção **Criação rápida** no Portal de Gerenciamento do Azure.
-   Híbrida: inclue uma rede virtual para acesso local e é criada usando a opção **Criar com VPN** no Portal de Gerenciamento.

Este tutorial explica o processo de criação de uma implantação de nuvem. Há quatro etapas:

1.  Criar um serviço RemoteApp.
2.  Opcionalmente, configure a sincronização do diretório. O RemoteApp exige esta sincronização de usuário, grupos, contatos e senhas do seu Active Directory local para o seu locatário do Active Directory do Azure.
3.  Publicar os programas do RemoteApp.
4.  Configurar o acesso do usuário.

**Antes de começar**

É necessário fazer o seguinte antes de criar o serviço:

-   Inscreva-se para a visualização do RemoteApp. É possível fazer isso em <http://azure.microsoft.com/pt-br/services/remoteapp/>.
-   Reúna as informações sobre os usuários e os grupos aos quais deseja permitir acesso. Podem ser informações da conta da Microsoft ou da conta corporativa do Active Directory para usuários ou grupos.
-   Este procedimento pressupõe que você irá usar a imagem do modelo fornecida como parte da sua assinatura ou que já tenha feito o upload da imagem do modelo que deseja usar. Se desejar fazer o upload da imagem do modelo, é possível fazer isso na página Imagens do modelo. Apenas clique em **Fazer o upload de uma imagem do modelo** e siga as etapas do assistente.
-   Deseja fornecer aplicativos personalizados ou programas de LOB? Crie uma nova [imagem de modelo personalizada][imagem de modelo personalizada] e use-a na sua implantação de nuvem.

## **Etapa 1: Crie um serviço RemoteApp**

1.  No [Portal de Gerenciamento do Microsoft Azure][Portal de Gerenciamento do Microsoft Azure], vá para a página do RemoteApp.
2.  Clique em **Novo \> Criação Rápida**.

3.  Digite um nome para o seu serviço e selecione a sua região.
4.  Escolha a assinatura que deseja usar para criar este serviço.
5.  Escolha o modelo para usar neste serviço.

    **Dica:** A sua assinatura do RemoteApp acompanha uma imagem do modelo que contém os programas Office 2013, alguns publicados (como o Word) e outros prontos para a publicação. Você também pode criar uma nova [imagem de modelo personalizada][imagem de modelo personalizada] e usá-la na sua implantação de nuvem.

6.  Clique em **Criar o serviço RemoteApp**.

    **Importante:** Pode demorar até 30 minutos para provisionar o seu serviço.

Após a criação do serviço RemoteApp, vá para a página **Início Rápido** do RemoteApp para continuar as etapas de configuração.

## **Etapa 2: Configurar a sincronização de diretório do Active Directory (Opcional)**

Se desejar usar o Active Directory, o RemoteApp exige a sincronização de diretório entre o Active Directory do Azure e o Active Directory local para sincronizar usuários, grupos, contatos e senhas com o seu locatário do Active Directory do Azure. Consulte o [Roteiro de sincronização do diretório][Roteiro de sincronização do diretório] para as informações de planejamento e etapas detalhadas.

## **Etapa 3: Publicar os programas do RemoteApp**

Um programa do RemoteApp é um aplicativo ou um programa fornecido para os seus usuários. Ele está localizado na imagem do modelo no qual foi feito o upload do serviço. Quando o usuário acessa um programa do RemoteApp, o programa parece estar em execução no seu ambiente local mas, na verdade, está em execução no Azure.

Antes que os seus usuários possam acessar os programas do RemoteApp, é necessário publicá-los nos comentários do usuário final – uma lista de programas disponíveis que os seus usuários acessam através do portal do Azure.

É possível publicar vários programas em seu serviço do RemoteApp. Na página de prohramas do RemoteApp, clique em **Publicar** para adicionar um programa. É possível publicar no menu Iniciar da imagem do modelo ou especificar o caminho na imagem do modelo para o programa. Se optar por adicionar a partir do menu Iniciar, escolha o programa para publicar. Se optar por fornecer o caminho para o programa, forneça um nome para o programa e o caminha no qual o programa está instalado na imagem do modelo.

## **Etapa 4: Configurar o acesso do usuário**

Agora que o seu serviço do RemoteApp foi criado, é necessário adicionar os usuários e grupos que deseja habilitar para usar os seus recursos remotos. Se estiver usando o Active Directory, os usuários ou grupos que possuem acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar este serviço do RemoteApp.

1.  Na página Início Rápido, clique em **Configurar o acesso do usuário**.
2.  Digite a conta corporativa ou o nome do grupo (do Active Directory) ou a conta da Microsoft para a qual deseja conceder acesso.

    Para os usuários, certifique-se de que você use o formato “user@domain.com”. Para grupos, digite o nome do grupo.

3.  Uma vez que os usuários ou grupos forem validades, clique em **Salvar**.

## Próximas etapas

É isso - a sua implantação de nuvem do RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL para o cliente na página Início Rápido do RemoteApp. Em seguida, faça com que os usuários façam logon no cliente e acessem os programas do RemoteApp publicados.

  [imagem de modelo personalizada]: http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/
  [Portal de Gerenciamento do Microsoft Azure]: http://manage.windowsazure.com
  [Roteiro de sincronização do diretório]: http://msdn.microsoft.com/pt-br/library/azure/hh967642.aspx
