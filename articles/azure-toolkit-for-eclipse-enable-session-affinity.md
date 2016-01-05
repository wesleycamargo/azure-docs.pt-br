<properties
    pageTitle="Habilitar a Afinidade de Sessão usando o Kit de Ferramentas do Azure para o Eclipse"
    description="Saiba como habilitar a afinidade de sessão usando o Kit de Ferramentas do Azure para o Eclipse."
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# Habilitar a afinidade de sessão #

No Kit de Ferramentas do Azure para o Eclipse, é possível habilitar a afinidade de sessão HTTP, ou “sessões adesivas”, para suas funções. A seguinte imagem mostra o diálogo de propriedades **Balanceamento de Carga** usado para habilitar o recurso de afinidade de sessão:

![][ic719492]

## Para habilitar a afinidade de sessão para sua função ##

1. Clique com o botão direito do mouse na função no Explorador de Projetos do Eclipse, clique em **Azure** e em **Balanceamento de Carga**.
1. No diálogo **Propriedades de balanceamento de carga de WorkerRole1**:
    1. Verifique **Habilitar a afinidade de sessão HTTP (sessões autoadesivas) para esta função.**
    1. Para **Ponto de extremidade de entrada a ser usado**, selecione um ponto de extremidade de entrada a ser usado, por exemplo, **http (pública: 80, privada: 8080)**. Seu aplicativo deve usar esse ponto de extremidade como seu ponto de extremidade HTTP. Você pode habilitar vários pontos de extremidade para a sua função, mas selecionar apenas um para dar suporte às sessões adesivas.
    1. Recompile seu aplicativo.

Quando habilitadas, se você tiver mais de uma instância de função, as solicitações HTTP provenientes de um determinado cliente continuarão sendo manipuladas pela mesma instância de função.

O Kit de Ferramentas para o Eclipse permite isso instalando um módulo IIS especial chamado ARR (Roteamento de Solicitação do Aplicativo) em cada uma de suas instâncias de função. O ARR redireciona as solicitações HTTP para a instância de função apropriada. O kit de ferramentas reconfigura automaticamente o ponto de extremidade selecionado para que o tráfego HTTP de entrada seja primeiramente roteado para o software ARR. O Kit de Ferramentas também cria um novo ponto de extremidade interno que o servidor Java está configurado para escutar. Esse é o ponto de extremidade usado pelo ARR para redirecionar o tráfego HTTP para a instância de função apropriada. Dessa forma, cada instância de função em sua implantação de várias instâncias serve como um proxy reverso para todas as outras instâncias, habilitando as sessões adesivas.

## Observações sobre a afinidade de sessão ##

* A afinidade de sessão não funciona no emulador de computação. As configurações podem ser aplicadas no emulador de computação sem interferir no processo de compilação ou na execução do emulador de computação, mas o recurso em si não funciona no emulador de computação.
* Habilitar a afinidade de sessão resultará em um aumento na quantidade de espaço em disco ocupada pela sua implantação no Azure, já que um software adicional será baixado e instalado em suas instâncias de função quando o serviço for iniciado na nuvem do Azure.
* O tempo para inicializar cada função levará mais tempo.
* Um ponto de extremidade interno, para funcionar como um novo roteador de tráfego, como mencionado acima, será adicionado.

Para ver um exemplo de como manter os dados de sessão quando a afinidade de sessão é habilitada, veja [Como manter os dados da sessão com a afinidade de sessão][].

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Criar um aplicativo Hello World do Azure no Eclipse][]

[Instalação do Kit de Ferramentas do Azure para o Eclipse][]

[Como manter os dados da sessão com a afinidade de sessão][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Como manter os dados da sessão com a afinidade de sessão]: http://go.microsoft.com/fwlink/?LinkID=699539
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!---HONumber=AcomDC_1210_2015-->