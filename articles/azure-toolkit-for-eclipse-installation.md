<properties
    pageTitle="Instalando o Kit de Ferramentas do Azure para Eclipse"
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
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/pt-BR/library/azure/hh690946.aspx -->

# Instalando o Kit de Ferramentas do Azure para Eclipse #

O Kit de Ferramentas do Azure para o Eclipse fornece modelos e funcionalidade que permitem criar, desenvolver, testar e implantar com facilidade aplicativos do Azure usando o ambiente de desenvolvimento do Eclipse. Ele é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do Apache 2.0 no site do projeto no GitHub na seguinte URL:

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

As etapas a seguir mostram como instalar o Kit de Ferramentas do Azure para o Eclipse.

## Pré-requisitos ##

* Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>.
* Um JDK (Java Developer Kit), v1.7 ou posterior. 
* Sistemas operacionais - o Kit de Ferramentas do Azure para Eclipse foi testado nos seguintes sistemas operacionais:
    * Windows 10
    * Windows 8 e Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT]Se você estiver usando o Kit de Ferramentas do Azure para o Eclipse no Windows, o kit de ferramentas requer a instalação do SDK do Azure 2.7 ou posterior. Você tem duas opções para instalar o SDK do Azure:
> 
> * Você pode baixar e instalar o SDK do Azure usando o [Web Platform Installer (WebPI)][].
> * Se você não tem o SDK do Azure instalado, quando criar seu primeiro projeto de implantação do Azure, você será solicitado a baixar automaticamente e instalar a versão necessária do SDK do Azure.
> 
> Observe que o SDK do Azure só é necessário no Windows.

## Para instalar o Kit de Ferramentas do Azure para o Eclipse ##

1. Inicie o Eclipse.
2. No menu do Eclipse, clique em <strong>Ajuda</strong> e em <strong>Instalar Novo Software</strong>, como mostrado no diagrama a seguir. ![][ic590123]
3. No diálogo <strong>Software Disponível</strong>, dentro da caixa de texto <strong>Trabalhar com</strong>, digite <strong>http://dl.msopentech.com/eclipse</strong> seguido da tecla <strong>Enter</strong>.
4. No painel <strong>Nome</strong>, marque <strong>Kit de Ferramentas do Azure para o Eclipse</strong> e desmarque <strong>Entrar em contato com todos os sites de atualização durante a instalação para encontrar o software necessário</strong>. Sua tela será semelhante à seguinte: ![][ic719482]
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

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Criando um aplicativo do Hello World para o Azure no Eclipse][]

[Novidades no Kit de Ferramentas do Azure para o Eclipse][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Criando um aplicativo do Hello World para o Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_1203_2015-->