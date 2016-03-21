<properties
	pageTitle="Instalação do Kit de Ferramentas do Azure para o Eclipse"
	description="Saiba como instalar o Kit de Ferramentas do Azure para o Eclipse."
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="03/09/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Instalação do Kit de Ferramentas do Azure para o Eclipse

O Kit de Ferramentas do Azure para Eclipse fornece modelos e funcionalidade que permitem criar, desenvolver, testar e implantar com facilidade aplicativos Azure usando o ambiente de desenvolvimento do Eclipse. Ele é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do Apache 2.0 no site do projeto no GitHub na seguinte URL:

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

As etapas a seguir mostram como instalar o Kit de Ferramentas do Azure para o Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Para instalar o Kit de Ferramentas do Azure para o Eclipse

1. Inicie o Eclipse.
2. No menu do Eclipse, clique em <strong>Ajuda</strong> e em <strong>Instalar Novo Software</strong>, como mostrado no diagrama a seguir. ![Instalação do Kit de Ferramentas do Azure para o Eclipse][ic590123]
3. No diálogo <strong>Software Disponível</strong>, dentro da caixa de texto <strong>Trabalhar com</strong>, digite <strong>http://dl.microsoft.com/eclipse</strong> seguido da tecla <strong>Enter</strong>.
4. No painel <strong>Nome</strong>, marque <strong>Kit de Ferramentas do Azure para o Eclipse</strong> e desmarque <strong>Entrar em contato com todos os sites de atualização durante a instalação para encontrar o software necessário</strong>. Sua tela será semelhante à seguinte: ![Instalação do Kit de Ferramentas do Azure para o Eclipse][ic719482]
5. Se você expandir o <strong>Kit de Ferramentas do Azure para o Eclipse</strong>, verá os seguintes itens:
    * **Filtro dos Serviços de Controle de Acesso do Azure**: esse componente dá suporte à autenticação de usuários do aplicativo com o Azure ACS.
    * **Plug-in comum do Azure**: este componente contém a funcionalidade compartilhada na qual se baseia os outros componentes.
    * **Kit de Ferramentas do Azure para o Eclipse**: este componente contém a lógica de configuração do projeto, o assistente de publicação para a nuvem e a interface do usuário.
    * **Microsoft JDBC Driver 4.0 para SQL Server**: esse componente simplifica o desenvolvimento de aplicativos com o Banco de Dados SQL.
    * **Pacote para Bibliotecas de Cliente do Apache Qpid para JMS**: esse componente fornece a biblioteca de cliente JMS do projeto do Apache Qpid para habilitar seu aplicativo a usar o sistema de mensagens baseado no protocolo AMQP (Advanced Messaging Queuing Protocol) no Azure
    * **Pacote para Bibliotecas do Azure para Java**: este componente permite que você compile aplicativos do Azure em Java que permitem aproveitar os recursos de computação em nuvem escalonáveis do Azure.
    * **Plug-in do Application Insights para Java**: este componente permite que você use os serviços de registro em log e análise de telemetria do Azure para seus aplicativos e instâncias de servidor.
6. Clique em **Próximo**. (Se você experimentar atrasos incomuns ao instalar o kit de ferramentas, certifique-se de que a opção **Contatar todos os sites de atualização durante a instalação para encontrar o software necessário** está desmarcada.)
7. No diálogo **Instalar Detalhes**, clique em **Avançar**.
8. No diálogo **Examinar Licenças**, leia os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **Aceito os termos dos contratos de licença** e clique em **Concluir**. (As etapas restantes supõem que você aceite os termos dos contratos de licença. Se você não aceitar os termos dos contratos de licença, saia do processo de instalação.)
9. Se for solicitado a reiniciar o Eclipse para concluir a instalação, clique em **Reiniciar Agora**.

## Consulte também

[Kit de ferramentas do Azure para Eclipse]

[Criação de um aplicativo Hello World do Azure no Eclipse]

[Novidades no Kit de Ferramentas do Azure para Eclipse]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Novidades no Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0309_2016-->