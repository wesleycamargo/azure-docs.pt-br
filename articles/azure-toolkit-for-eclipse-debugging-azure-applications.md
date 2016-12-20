---
title: "Depuração de aplicativos do Azure no Eclipse"
description: "Saiba mais sobre os Aplicativos de Depuração do Azure usando o Kit de Ferramentas do Azure para Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 3d1b34bf-399e-421e-bdcc-da2d422dbb4f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 06b33c09f5c316f974efde1116cae699d8df1065


---
# <a name="debugging-azure-applications-in-eclipse"></a>Depuração de aplicativos do Azure no Eclipse
Com o Kit de Ferramentas do Azure para Eclipse você pode depurar seus aplicativos, mesmo que eles estejam em execução no Azure ou no emulador de computação, se você estiver usando o Windows como sistema operacional. A imagem a seguir mostra a caixa de diálogo de propriedades de **Depuração** usada para habilitar a depuração.

![][ic719504]

Este tutorial presume que você já tem um aplicativo criado e está familiarizado com o emulador de computação e com a implantação no Azure.

Usaremos o aplicativo do tutorial [Uso da biblioteca de tempo de execução de serviço do Azure no JSP][Uso da biblioteca de tempo de execução de serviço do Azure no JSP] como ponto de partida para este tópico. Antes de prosseguirmos, crie esse aplicativo, caso ainda não tenha feito isso.

## <a name="to-debug-your-application-while-running-in-azure"></a>Para depurar seu aplicativo durante a execução no Azure
> [!WARNING]
> O suporte atual do kit de ferramentas para depuração remota de Java destina-se principalmente a implantações em execução no emulador de computação do Azure. Como a conexão de depuração não é segura, você não deve habilitar a depuração remota em implantações de produção. Se você precisar depurar um aplicativo em execução na nuvem do Azure, use uma implantação de preparo, mas saiba que é possível ocorrer o acesso não autorizado à sua sessão de depuração se alguém souber o endereço IP de sua implantação em nuvem, mesmo se for uma implantação de preparo.
> 
> 

1. Compile o projeto para testar no emulador: no Gerenciador de Projetos do Eclipse, clique com o botão direito em **MyAzureProject**, clique em **Propriedades**, em **Azure** e defina **Compilar para** como **Implantação em nuvem**.
2. Recompile seu projeto: no menu do Eclipse, clique em **Projeto** e em **Compilar Tudo**.
3. Implante seu aplicativo em *preparo* no Azure
    >[AZURE.IMPORTANT] Como mencionado acima, é altamente recomendável que você depure no emulador de computação na maioria dos casos e depure no ambiente de preparo somente se houver a necessidade de mais depuração. Não recomendamos a depuração no ambiente de produção.
4. Quando sua implantação estiver pronta no Azure, obtenha o nome DNS da implantação no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Uma implantação de preparo tem um nome DNS no formato http://*&lt;guid&gt;*.cloudapp.net, em que *&lt;guid&gt;* é um valor de GUID atribuído pelo Azure.
5. No Gerenciador de Projetos do Eclipse, clique com o botão direito em **WorkerRole1**, clique em **Azure** e em **Depuração**.
6. Na caixa de diálogo **Propriedades para Depuração de WorkerRole1** :
   1. Marque **Habilitar a depuração remota para esta função.**
   2. Para **Ponto de extremidade de entrada a ser usado**, use **Depuração (pública:8090, privada:8090)**.
   3. Certifique-se de que **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** esteja desmarcada.
       >[AZURE.IMPORTANT] A opção **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** serve para cenários de depuração avançada apenas no emulador de computação (não para implantações de nuvem). Se a opção **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** for usada, ela suspenderá o processo de inicialização do servidor até que o depurador do Eclipse esteja conectado à JVM. Embora seja possível usar essa opção para uma sessão de depuração com o emulador de computação, não a utilize para uma sessão de depuração em uma implantação de nuvem. A inicialização do servidor ocorre em uma tarefa de inicialização do Azure, e a nuvem do Azure não disponibiliza pontos de extremidade públicos até que a tarefa de inicialização seja concluída. Assim, um processo de inicialização não será concluído com êxito se essa opção estiver habilitada em uma implantação de nuvem, pois ele não será capaz de receber uma conexão de um cliente externo do Eclipse.
   4. Clique em **Criar Configurações de Depuração**.
7. Na caixa de diálogo **Configuração de Depuração do Azure** :
   1. Para **Projeto Java para depuração**, selecione o projeto **MyHelloWorld**.
   2. Em **Configurar depuração para**, marque **Nuvem do Azure (preparo)**.
   3. Certifique-se de que a opção **Emulador de computação do Azure** esteja desmarcada.
   4. Em **Host**, insira o nome DNS de sua implantação em estágios, mas sem o **http://** anterior. Por exemplo (use seu GUID no lugar do GUID exibido aqui): **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
8. Clique em **OK** para fechar o diálogo **Configuração de Depuração do Azure**.
9. Clique em **OK** para fechar o diálogo **Propriedades para Depuração de WorkerRole1**.
10. Se você não tiver um ponto de interrupção definido em index.jsp, configure-o:
    1. No Gerenciador de Projetos do Eclipse, expanda **MyHelloWorld**, expanda **WebContent** e clique duas vezes em **index.jsp**.
    2. No index.jsp, clique com o botão direito na barra azul à esquerda do código Java e clique em **Ativar/Desativar Pontos de Interrupção**, como exibido a seguir:  ![][ic551537]
11. No menu do Eclipse, clique em **Executar** e clique em **Configurações de Depuração**.
12. Na caixa de diálogo **Configurações de Depuração**, expanda **Aplicativo Java Remoto** no painel esquerdo, selecione **Nuvem do Azure (WorkerRole1)** e clique em **Depurar**.
13. Em seu navegador, execute o aplicativo preparado, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, substituindo o GUID do nome DNS por *&lt;guid&gt;*. Caso receba uma solicitação de uma caixa de diálogo **Confirmar Alternância de Perspectiva**, clique em **Sim**. Agora, a sua sessão de depuração deve ser executada a partir da linha de código na qual o ponto de interrupção foi definido.

> [!NOTE]
> Se você estiver tentando iniciar uma conexão de depuração remota com uma implantação que possui várias instâncias de função em execução, não será possível controlar com qual instância do depurador ocorrerá a conexão inicial, pois o Balanceador de Carga do Azure selecionará aleatoriamente uma instância. Quando você estiver conectado com essa instância, no entanto, continuará depurando a mesma instância. Observe também que, se houver um período de inatividade de mais de quatro minutos (por exemplo, quando você ficar parado em um ponto de interrupção por muito tempo), o Azure poderá fechar a conexão.
> 
> 

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Depuração de uma instância de função específica em uma implantação com várias instâncias
Quando a implantação estiver em execução na nuvem, provavelmente estará em execução em instâncias com vários computadores ou funções. Isso permite que você aproveite a garantia de disponibilidade de 99,95% do Azure e que você escale horizontalmente seu aplicativo.

Nesses cenários, talvez seja necessário depurar remotamente o aplicativo Java em uma instância de função específica. No entanto, se você habilitar apenas um ponto de extremidade de entrada regular para depuração, o Balanceador de Carga do Azure tornará praticamente impossível a conexão do depurador a uma instância de função específica. Em vez disso, ele conectará você a uma instância de função escolhida aleatoriamente.

Esse é o tipo de cenário no qual aproveitar os pontos de extremidade de entrada da instância facilitará a depuração de uma instância de função específica.

Vamos supor que seu plano seja executar até cinco instâncias de sua implantação. Ao usar a página de propriedade dos **Pontos de extremidade** na caixa de diálogo de propriedades de função, crie um ponto de extremidade de entrada da instância e atribua a ele um intervalo de portas públicas, em vez de um único número de porta. Por exemplo, na caixa de entrada **Porta pública**, especifique **81-85**.

Depois de implantar seu aplicativo com esse ponto de extremidade de instância, o Azure atribuirá um número exclusivo de porta desse intervalo a cada uma das instâncias de função. Então, para descobrir qual número porta foi atribuído a cada instância, use a variável de ambiente *InstanceEndpointName***_PUBLICPORT** (em que *InstanceEndpointName* é o nome atribuído ao criar o ponto de extremidade de instância) configurada automaticamente pelo kit de ferramentas em sua implantação (por exemplo, retornando o valor no rodapé da página Web, para que você possa lê-lo ao navegar até a página).

Quando você souber o número de porta pública atribuído a essa instância, faça uma referencia ele em sua configuração de depuração no Eclipse, afixando-o ao nome do host do serviço. Isso permitirá que o depurador do Eclipse se conecte a essa instância específica e não a qualquer outra instância.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Somente Windows: para depurar seu aplicativo durante a execução no emulador de computação
> [!NOTE]
> O emulador do Azure só está disponível no Windows. Ignore esta seção se você estiver usando um sistema operacional diferente do Windows. 
> 
> 

1. Compile o projeto para testar no emulador: no Gerenciador de Projetos do Eclipse, clique com o botão direito do mouse em **MyAzureProject**, clique em **Propriedades**, clique em **Azure** e defina **Compilar para** como **Testando no emulador**.
2. Recompile seu projeto: no menu do Eclipse, clique em **Projeto** e em **Compilar Tudo**.
3. No Gerenciador de Projetos do Eclipse, clique com o botão direito em **WorkerRole1**, clique em **Azure** e em **Depuração**.
4. Na caixa de diálogo **Propriedades para Depuração de WorkerRole1** :
   1. Marque **Habilitar a depuração remota para esta função.**
   2. Em **Ponto de extremidade de entrada** a ser usado, use o ponto de extremidade padrão gerado automaticamente pelo kit de ferramentas, listado como **Depuração (pública: 8090, privada: 8090)**.
   3. Certifique-se de que a opção **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** esteja desmarcada.
       >[AZURE.IMPORTANT] A opção **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** serve para cenários de depuração avançada apenas no emulador de computação (não para implantações de nuvem). Se a opção **Iniciar JVM no modo suspenso, aguardando uma conexão do depurador** for usada, ela suspenderá o processo de inicialização do servidor até que o depurador do Eclipse esteja conectado à JVM. Embora seja possível usar essa opção para uma sessão de depuração com o emulador de computação, não a utilize para uma sessão de depuração em uma implantação de nuvem. A inicialização do servidor ocorre em uma tarefa de inicialização do Azure, e a nuvem do Azure não disponibiliza pontos de extremidade públicos até que a tarefa de inicialização seja concluída. Assim, um processo de inicialização não será concluído com êxito se essa opção estiver habilitada em uma implantação de nuvem, pois ele não será capaz de receber uma conexão de um cliente externo do Eclipse.
   4. Clique em **Criar Configurações de Depuração**.
5. Na caixa de diálogo **Configuração de Depuração do Azure** :
   1. Para **Projeto Java para depuração**, selecione o projeto **MyHelloWorld**.
   2. Em **Configurar depuração para**, marque **Emulador de computação do Azure**.
6. Clique em **OK** para fechar o diálogo **Configuração de Depuração do Azure**.
7. Clique em **OK** para fechar o diálogo **Propriedades para Depuração de WorkerRole1**.
8. Defina um ponto de interrupção em index.jsp:
   
   1. No Gerenciador de Projetos do Eclipse, expanda **MyHelloWorld**, expanda **WebContent** e clique duas vezes em **index.jsp**.
   2. No index.jsp, clique com o botão direito na barra azul à esquerda do código Java e clique em **Ativar/Desativar Pontos de Interrupção**, como exibido a seguir:  ![][ic551537]
      
      Um ponto de interrupção será definido se você vir um ícone de ponto de interrupção na barra azul à esquerda do código Java.
9. Inicie o aplicativo no emulador de computação clicando no botão **Executar no Emulador do Azure** na barra de ferramentas do Azure.
10. No menu do Eclipse, clique em **Executar** e clique em **Configurações de Depuração**.
11. Na caixa de diálogo **Configurações de Depuração**, expanda **Aplicativo Java Remoto** no painel esquerdo, selecione **Emulador do Azure (WorkerRole1)** e clique em **Depurar**.
12. Após o emulador de computação indicar que seu aplicativo está em execução, em seu navegador, execute **http://localhost:8080/MyHelloWorld**.
     Caso receba uma solicitação de uma caixa de diálogo **Confirmar Alternância de Perspectiva**, clique em **Sim**.
     Agora, a sua sessão de depuração deve ser executada a partir da linha de código na qual o ponto de interrupção foi definido.

Este artigo mostrou como depurar no emulador de computação; a próxima seção mostrará como depurar um aplicativo implantado no Azure.

## <a name="debugging-notes"></a>Notas de depuração
* Após a depuração, você pode alternar a perspectiva de **Depurar** para **Java** clicando no menu do Eclipse, clicando em **Janela**, **Abrir Perspectiva** e selecionando a perspectiva que você deseja usar.
* Para habilitar a depuração remota no GlassFish, não use o recurso de configuração de depuração remota do Kit de Ferramentas do Azure para Eclipse. Em vez disso, configure o GlassFish manualmente. Devido à forma como a GlassFish trata as opções predefinidas de Java nas variáveis de ambiente, o recurso configuração de depuração remota do kit de ferramentas não funciona corretamente com o GlassFish. Se o recurso de configuração de depuração remota do kit de ferramentas estiver habilitado, ele poderá impedir a inicialização do GlassFish.

## <a name="see-also"></a>Consulte também
[Kit de ferramentas do Azure para Eclipse][Kit de ferramentas do Azure para Eclipse]

[Criação de um aplicativo Hello World do Azure no Eclipse][Criação de um aplicativo Hello World do Azure no Eclipse]

[Instalação do Kit de Ferramentas do Azure para Eclipse][Instalação do Kit de Ferramentas do Azure para Eclipse] 

Para obter mais informações sobre o uso do Azure com Java, consulte o [Central de desenvolvedores de Java no Azure][Central de desenvolvedores de Java no Azure].

<!-- URL List -->

[Central de desenvolvedores de Java no Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Uso da biblioteca de tempo de execução de serviço do Azure no JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->



<!--HONumber=Nov16_HO3-->


