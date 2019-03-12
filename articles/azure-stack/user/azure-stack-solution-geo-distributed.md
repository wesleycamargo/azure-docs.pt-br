---
title: Criar uma solução de aplicativo distribuído geograficamente com o Azure e o Azure Stack | Microsoft Docs
description: Saiba como criar uma solução de aplicativo distribuído geograficamente com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 857aa71a4812534030ca638fd8bab11f60535ea0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536939"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Tutorial: Criar uma solução de aplicativo distribuído geograficamente com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como direcionar o tráfego para pontos de extremidade específicos com base em várias métricas usando o padrão de aplicativos distribuídos geograficamente. Criar um Gerenciador de tráfego de perfil com a configuração de ponto de extremidade e roteamento geográfico com base em garante informações são roteadas para os pontos de extremidade com base em requisitos regionais, corporativa e internacional de regulamentação e suas necessidades de dados.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie um aplicativo distribuído geograficamente.
> - Use o Gerenciador de tráfego para seu aplicativo de destino.

## <a name="use-the-geo-distributed-apps-pattern"></a>Usar o padrão de aplicativos distribuídos geograficamente

Com o padrão e distribuídos geograficamente, seu aplicativo pode abranger regiões. Definir como padrão para a nuvem pública, mas alguns dos usuários podem exigir que seus dados permanecerão em sua região. Você pode direcionar usuários para a nuvem mais adequado com base em suas necessidades.

### <a name="issues-and-considerations"></a>Problemas e considerações

#### <a name="scalability-considerations"></a>Considerações sobre escalabilidade

A solução que você criará com este tutorial é não acomodar a escalabilidade. No entanto, se usado em combinação com outras soluções e tecnologias do Azure e no local, você poderá acomodar requisitos de escalabilidade. Para obter informações sobre como criar uma solução híbrida com o dimensionamento automático por meio do tráfego manager, consulte [criar soluções de colocação em escala de nuvem com o Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Considerações sobre disponibilidade

Como é o caso com considerações sobre escalabilidade, essa solução diretamente não aborda a disponibilidade. No entanto, também é semelhante ao nosso considerações sobre escalabilidade, soluções e as tecnologias locais e do Azure, podem ser implementadas nesta solução para garantir a alta disponibilidade para todos os componentes envolvidos.

### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão

- Sua organização tem ramificações internacionais que exigem segurança regional personalizadas e as diretivas de distribuição.

- Cada um dos seus escritórios de organizações efetua pull de funcionário, negócios e dados de recurso, exigindo o relatório de atividade por regulamentos locais e o fuso horário.

- Os requisitos de alta escala podem ser atendidos pela escalação horizontal de aplicativos, com várias implantações de aplicativo feitas dentro de uma única região, bem como entre regiões, para lidar com requisitos de carga extrema.

### <a name="planning-the-topology"></a>Planejar a topologia

Antes de criar uma superfície de aplicativo distribuído, ele ajuda a ter o conhecimento a seguir:

-   **Domínio personalizado para o aplicativo:** Qual é o nome de domínio personalizado que os clientes usarão para acessar o aplicativo? Para o aplicativo de exemplo, o nome de domínio personalizado é *www.scalableasedemo.com.*

-   **Domínio do Gerenciador de Tráfego:** Um nome de domínio deve ser escolhido ao criar uma [perfil do Gerenciador de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Esse nome será combinado com o sufixo *trafficmanager.net* para registrar uma entrada de domínio gerenciada pelo Gerenciador de Tráfego. Para o aplicativo de exemplo, o nome escolhido é *scalable-ase-demo*. Como resultado, é o nome de domínio completo que é gerenciado pelo Gerenciador de tráfego *demo.trafficmanager.net escalonável ase*.

-   **Estratégia para dimensionar o volume do aplicativo:** o volume do aplicativo será distribuído entre vários Ambientes do Serviço de Aplicativo em uma única região? Várias regiões? Uma combinação de ambas as abordagens? A decisão deve se basear nas expectativas da origem do tráfego do cliente e em como o resto da infraestrutura de back-end de suporte de um aplicativo pode ser escalonado. Por exemplo, com um aplicativo 100% sem monitoração de estado, ele pode ser altamente dimensionado usando uma combinação de vários Ambientes de Serviço de Aplicativo por região do Azure, multiplicado por Ambientes de Serviço de Aplicativo implantado em várias regiões do Azure. Com 15 + regiões globais do Azure disponíveis para sua escolha, os clientes podem realmente criar uma superfície de aplicativo de larga escala em todo o mundo. Para o aplicativo de exemplo usado neste artigo, três Ambientes de Serviço de Aplicativo foram criados em uma única região do Azure (centro-sul dos EUA).

-   **Convenção de nomenclatura para os Ambientes do Serviço de Aplicativo:** Cada Ambiente do Serviço de Aplicativo requer um nome exclusivo. Além dos ambientes de serviço de aplicativo de um ou dois, é útil ter uma convenção de nomenclatura para ajudar a identificar cada ambiente do serviço de aplicativo. Para o aplicativo de exemplo, foi usada uma convenção de nomenclatura simples. Os nomes dos três Ambientes de Serviço de Aplicativo são *fe1ase*, *fe2ase* e *fe3ase*.

-   **Convenção de nomenclatura para os aplicativos:** como várias instâncias do aplicativo serão implantadas, é necessário um nome para cada instância do aplicativo implantado. Com os ambientes de serviço de aplicativo o mesmo nome de aplicativo pode ser usado em vários ambientes de serviço de aplicativo. Como cada ambiente do serviço de aplicativo tem um sufixo de domínio exclusivo, os desenvolvedores podem optar reutilizar o mesmo nome de aplicativo em cada ambiente. Por exemplo, um desenvolvedor poderia ter aplicativos nomeados da seguinte maneira: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc. Para o aplicativo nesse cenário, cada instância do aplicativo tem um nome exclusivo. Os nomes de instância de aplicativo usados são *webfrontend1*, *webfrontend2* e *webfrontend3*.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar aplicativos híbridos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.

## <a name="part-1-create-a-geo-distributed-app"></a>Parte 1: Criar um aplicativo distribuído geograficamente

Nesta parte, você criará um aplicativo web.

> [!div class="checklist"]
> - Criar aplicativos web e publicar
> - Adicione código para repositórios do Azure
> - Aponte o build do aplicativo para vários destinos de nuvem.
> - Gerenciar e configurar o processo de CD

### <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure e a instalação do Azure Stack são necessárias.

### <a name="geo-distributed-app-steps"></a>Etapas de aplicativo distribuído geograficamente

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obter um domínio personalizado e configurar o DNS

Atualize o arquivo de zona DNS para o domínio. Azure AD, em seguida, pode verificar a propriedade de nome de domínio personalizado. Use [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para os registros DNS do Azure/Office 365/DNS externo dentro do Azure, ou adicionar a entrada DNS em [um registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Registre um domínio personalizado com um registrador de público.

2. Entre no registrador de nome de domínio para o domínio. Um administrador aprovado talvez precise fazer as atualizações DNS.

3. Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo Azure AD. A entrada DNS não altera os comportamentos, como roteamento de email ou hospedagem na web.

### <a name="create-web-apps-and-publish"></a>Criar aplicativos web e publicar

Configure híbrido CI/CD para implantar aplicativo Web no Azure e o Azure Stack e automático de alterações por push para ambas as nuvens.

> [!Note]  
> O Azure Stack com imagens adequados seja distribuído para execução (Windows Server e SQL) e a implantação do serviço de aplicativo são necessárias. Leia a documentação do serviço de aplicativo [antes de começar com o serviço de aplicativo no Azure Stack](../azure-stack-app-service-before-you-get-started.md) seção para o operador de pilha do Azure.

#### <a name="add-code-to-azure-repos"></a>Adicione código para repositórios do Azure

1. Entrar no Visual Studio com uma **conta que tenha direitos de criação do projeto** em repositórios do Azure.

    Entrega de contínua/integração contínua (CI/CD) do híbrido pode aplicar a código do aplicativo e o código de infraestrutura. Use [modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para ambos os desenvolvimento em nuvem privada e hospedado.

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Clone o repositório** criando e abrindo o aplicativo da web padrão.

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Criar implantação de aplicativo web em ambas as nuvens

1.  Editar o **WebApplication.csproj** arquivo: Selecione **Runtimeidentifier** e adicione **win10-x64**. (Consulte [contained implantação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.)

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Fazer check-in de código para repositórios do Azure** usando o Team Explorer.

2.  Confirme se o **código do aplicativo** foi verificada em repositórios do Azure.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. **Faça logon no Azure Pipelines** para confirmar a capacidade de criar definições de compilação.

2. Adicione **- r win10-x64** código. Isso é necessário para disparar uma implantação autocontida com.Net Core.

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **Executar a compilação**. O [compilação de implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará os artefatos que podem ser executados no Azure e Azure Stack.

**Usando um agente hospedado do Azure**

Usando um agente hospedado em Pipelines do Azure é uma opção conveniente para criar e implantar aplicativos web. Manutenção e atualizações são executadas automaticamente pelo Microsoft Azure, permitindo a implantação, teste e desenvolvimento contínuo e sem interrupções.

### <a name="manage-and-configure-the-cd-process"></a>Gerenciar e configurar o processo de CD

DevOps e o servidor do Azure DevOps do Azure fornecem um pipeline totalmente configurável e gerenciável para as versões em vários ambientes, como desenvolvimento, preparo, QA e ambientes de produção; incluindo a necessidade de aprovações em estágios específicos.

#### <a name="create-release-definition"></a>Criar definição de versão


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1.  Selecione o **plus** botão para adicionar uma nova versão sob o **guia Releases** na página de Build e versão do Visual Studio Online (VSO).

    ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. Aplicar a **implantação de serviço de aplicativo do Azure** modelo.

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. Em Adicionar menu suspenso artefato **adicionar o artefato** para o aplicativo de build de nuvem do Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. Na guia Pipeline, selecione a **fase, a tarefa** vincular do ambiente e definir valores de ambiente de nuvem do Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. Defina as **nome do ambiente** e selecione Azure **assinatura** para o ponto de extremidade de nuvem do Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. Em nome do ambiente, defina exigida **nome do serviço de aplicativo do Azure**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. Insira **VS2017 hospedado** em fila do agente para o ambiente hospedado na nuvem do Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. No menu de implantar o serviço de aplicativo do Azure, selecione válidos **pacote ou pasta** para o ambiente. Selecione Okey para **local da pasta**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. Salve todas as alterações e voltar para **pipeline de lançamento**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. Adicionar um **novo artefato** selecionando a compilação para o aplicativo do Azure Stack.

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. Adicionar um ambiente de mais aplicando o **implantação de serviço de aplicativo do Azure.**

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. Nomeie o novo ambiente **Azure Stack.**

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. Encontre o ambiente do Azure Stack sob **tarefa** guia.

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. Selecione o **assinatura** para o ponto de extremidade do Azure Stack.

  ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. Defina o nome do aplicativo web do Azure Stack como o **nome do serviço de aplicativo**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. Selecione o **agente do Azure Stack**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. Sob o serviço de aplicativo do Azure implantar seção Selecionar válidos **pacote ou pasta** para o ambiente. Selecione Okey para **local da pasta**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. Sob **variável** guia adicione uma variável denominada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, defina seu valor como `true`e definir o escopo para `Azure Stack`.

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. Selecione o **contínuo** ícone de gatilho de implantação em artefatos e habilite a **continua** gatilho de implantação.

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. Selecione o **pré-implantação** ícone de condições no ambiente do Azure Stack e defina o gatilho como **após o lançamento.**

21. Salve todas as alterações.

> [!Note]  
>  Algumas configurações para as tarefas podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) quando você criou uma definição de versão de um modelo. Essas configurações não podem ser modificadas as configurações da tarefa; em vez disso, você deve selecionar o item pai de ambiente para editar essas configurações.

## <a name="part-2-update-web-app-options"></a>Parte 2: Atualizar opções de aplicativo da web

O [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/overview) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Mapear um nome DNS personalizado existente para aplicativos Web do Azure
> - Use um * * gravador CNAME uma **um registro** para mapear um nome DNS personalizado para o serviço de aplicativo.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapear um nome DNS personalizado existente para aplicativos Web do Azure

> [!Note]  
>  Use um CNAME para todos os nomes DNS personalizados, exceto um domínio raiz (para example,northwind.com).

Para migrar um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo, consulte [Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

-   [Criar um aplicativo de serviço de aplicativo](https://docs.microsoft.com/azure/app-service/), ou usar um aplicativo criado para outro tutorial.

-   Comprar um nome de domínio e verifique se o acesso ao registro de DNS para o provedor de domínio.

Atualize o arquivo de zona DNS para o domínio. Azure AD verificará a propriedade de nome de domínio personalizado. Use [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para os registros DNS do Azure/Office 365/DNS externo dentro do Azure, ou adicionar a entrada DNS em [um registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Registre um domínio personalizado com um registrador de público.

-   Entre no registrador de nome de domínio para o domínio. (Um administrador aprovado talvez precise fazer atualizações DNS.)

-   Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo Azure AD.

Por exemplo, para adicionar entradas DNS para northwindcloud.com e www.northwindcloud.com, definir configurações de DNS para o domínio raiz de northwindcloud.com.

> [!Note]  
>  Um nome de domínio pode ser adquirido usando o [portal do Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).  
> Para mapear um nome DNS personalizado para um aplicativo Web, o [plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web deve ser uma camada paga (**Compartilhado**, **Básico**, **Standard** ou **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Criar e mapear registros CNAME e A

#### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

> [!Note]  
>  Use o DNS do Azure para configurar um nome DNS personalizado para aplicativos Web do Azure. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Entre site do provedor do principal.

2.  Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS. Procure áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.

Página de registros DNS pode ser exibida no **Meus domínios**. Localize o link nomeado **arquivo de zona**, **registros de DNS**, ou **configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](media/azure-stack-solution-geo-distributed/image28.png)

1.  No registrador de nome de domínio, selecione **adicionar ou criar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Consulte a documentação do provedor.

2.  Adicione um registro CNAME para mapear um subdomínio para o nome de host do aplicativo padrão.

  Por exemplo, domínio www.northwindcloud.com, adicione um registro CNAME que mapeia o nome para < aplicativo\_nome >. azurewebsites.

Depois de adicionar o CNAME, a página de registros DNS se parece com o exemplo a seguir:

![Navegação no Portal para o aplicativo do Azure](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitar o mapeamento de registro CNAME no Azure

1.  Em uma nova guia, entrar no portal do Azure,

2.  Navegue até os serviços de aplicativo.

3.  Selecione o aplicativo web.

4.  No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

5.  Selecione o ícone **+** ao lado de **Adicionar nome do host**.

1.  Digite o nome de domínio totalmente qualificado, como `www.northwindcloud.com`.

2.  Selecione **Validar**.

3.  Se indicado, adicionar registros adicionais de outros tipos (`A` ou `TXT`) para os registros DNS de registradores de nome de domínio. Azure fornecerá os valores e tipos desses registros:

     a.  Um registro **A** a ser mapeado para o endereço IP do aplicativo.

    b.  Um **TXT** registro para mapear para o nome do host do aplicativo padrão < nome_do_aplicativo >. azurewebsites. O serviço de aplicativo usa esse registro somente em tempo de configuração, para verificar a propriedade de domínio personalizado. Após a verificação, exclua o registro TXT.

4.  Concluir essa tarefa na guia do registrador de domínio e revalide até que o **Adicionar nome do host** botão for ativado.

5.  Certifique-se de que * * tipo de registro de nome de host é definido como **CNAME (www.example.com ou qualquer subdomínio)**.

6.  Selecione **Adicionar nome do host**.

7.  Digite o nome de domínio totalmente qualificado, como `northwindcloud.com`.

8.  Selecione **Validar**.

9.  O **adicionar** está ativado.

10. Certifique-se de que * * tipo de registro de nome de host é definido como **um registro a (example.com)**.

11. **Adicionar nome de host**.

  Pode levar algum tempo para que os novos nomes de host sejam refletidas no aplicativo do **domínios personalizados** página. Tente atualizar o navegador para atualizar os dados.
  
  ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  No caso de um erro, uma notificação de erro de verificação aparecerá na parte inferior da página. ![Erro de verificação](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  As etapas acima podem ser repetidas para mapear um domínio curinga (\*. northwindcloud.com)... Isso permite a adição de todos os subdomínios adicionais para esse serviço de aplicativo sem precisar criar um registro CNAME separado para cada uma delas. Siga as instruções de registrador para definir essa configuração.

#### <a name="test-in-a-browser"></a>Teste em um navegador

Navegue até o nome DNS configurado anteriormente (por exemplo, `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>Parte 3: Associar um certificado SSL personalizado

Nesta parte:

> [!div class="checklist"]
> - Associar o certificado SSL personalizado ao serviço de aplicativo
> - Impor HTTPS para o aplicativo
> - Automatizar a associação de certificado SSL com scripts

> [!Note]  
> Se necessário, obtenha um cliente certificado SSL no portal do Azure e associá-lo para o aplicativo web. Siga o [tutorial de Certificados do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

-   [Crie um aplicativo do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/)
-   [Mapear um nome DNS personalizado para o aplicativo Web](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Adquirir um certificado SSL de uma autoridade de certificação confiável e usar a chave para assinar a solicitação

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos para o certificado SSL

Para usar o certificado no Serviço de Aplicativo, o certificado deve atender a todos os seguintes requisitos:

-   Assinado por uma autoridade de certificado confiável

-   Exportado como um arquivo PFX protegido por senha

-   Conter chave privada com pelo menos 2.048 bits de extensão

-   Conter todos os certificados intermediários na cadeia de certificados

> [!Note]  
>  **Certificados de criptografia de curva (ECC) elíptica** funcionam com o serviço de aplicativo, mas não estão incluídos neste guia. Consulte uma autoridade de certificação para obter assistência na criação de certificados ECC. 

#### <a name="prepare-the-web-app"></a>Preparar o aplicativo web

Para associar um certificado SSL personalizado para o aplicativo web, o [plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) deve estar na **básica**, **padrão**, ou **Premium** camada.

#### <a name="sign-in-to-azure"></a>Entrar no Azure

1.  Abra o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo web.

2.  No menu à esquerda, selecione **serviços de aplicativos**e, em seguida, selecione o nome do aplicativo web.

![Selecionar aplicativo Web](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

1.  No painel de navegação à esquerda da página de aplicativo da web, role até a **as configurações** seção e selecione **escalar verticalmente (plano do serviço de aplicativo)**.

    ![Menu Escalar verticalmente](media/azure-stack-solution-geo-distributed/image34.png)

1.  Verifique se o aplicativo web não está no **livre** ou **compartilhado** camada. A camada do aplicativo web atual é realçada em uma caixa azul escuro.

    ![Verificar tipo de preço](media/azure-stack-solution-geo-distributed/image35.png)

Não há suporte para SSL personalizado nas camadas **Gratuito** ou **Compartilhado**. Para aumentar, siga as etapas na próxima seção, ou **escolha o tipo de preço** página e vá para [carregar e associar o certificado SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu Plano do Serviço de Aplicativo

1.  Selecione uma das camadas **Básico**, **Standard** ou **Premium**.

2.  Selecione **Selecionar**.

![Escolha um tipo de preço](media/azure-stack-solution-geo-distributed/image36.png)

A operação de escala é concluída quando a notificação é exibida.

![Escalar verticalmente a notificação](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Associar o certificado SSL e certificados intermediários de mesclagem

Mescle vários certificados na cadeia.

1. **Abra cada certificado** recebida em um editor de texto.

2. Crie um arquivo para o certificado mesclado, chamado *mergedcertificate.crt*. Em um editor de texto, copie o conteúdo de cada certificado para esse arquivo. A ordem de seus certificados deve seguir a ordem na cadeia de certificados, começando com o seu certificado e terminando com o certificado raiz. Ela se parece com o seguinte exemplo:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL mesclado com a chave privada gerada pelo certificado.

Um arquivo de chave privada é criado por meio do OpenSSL. Para exportar o certificado para PFX, execute o seguinte comando, substituindo os espaços reservados *< private-key-file >* e *< merged-certificate-file >* com caminhos de chave privados e mesclada arquivo de certificado.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando solicitado, defina uma senha de exportação para carregar seu certificado SSL para o serviço de aplicativo mais tarde.

Quando o IIS ou **Certreq.exe** são usados para gerar a solicitação de certificado, instale o certificado para um computador local e, em seguida [exportar o certificado para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Carregar o certificado SSL

1.  Selecione **configurações de SSL** no painel de navegação à esquerda do aplicativo web.

2.  Selecione **carregar certificado**.

3.  Na **arquivo de certificado PFX**, selecione arquivo PFX.

4.  4. Na **senha do certificado**, digite a senha que criou ao exportar o arquivo PFX.

5.  Escolha **Carregar**.

![Carregar um certificado](media/azure-stack-solution-geo-distributed/image38.png)

Quando o serviço de aplicativo terminar de carregar o certificado, ele aparece na **configurações de SSL** página.

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Associar o certificado SSL

1.  No **associações SSL** seção, selecione **Adicionar associação**.

    > [!Note]  
    >  Se o certificado foi carregado, mas não aparecer em nomes de domínio na **Hostname** menu suspenso, tente atualizar a página do navegador.

1.  No **Adicionar associação SSL** página, use as listas suspensas para selecionar o nome de domínio para proteger e o certificado a ser usado.

2.  Na **tipo de SSL**, selecione se deseja usar [ **indicação de nome de servidor (SNI)**](https://en.wikipedia.org/wiki/Server_Name_Indication)ou SSL baseado em IP.

-   **SSL baseado em SNI**-SSL baseado em SNI várias associações podem ser adicionadas. Esta opção permite que vários certificados SSL protejam vários domínios no mesmo endereço IP. Navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte ao SNI (encontre informações de suporte ao navegador mais abrangentes em [Indicação de Nome de Servidor](https://wikipedia.org/wiki/Server_Name_Indication)).

-   **SSL baseado em IP**-apenas uma associação de SSL com base em IP pode ser adicionada. Esta opção permite apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, protegê-los todos usando o mesmo certificado SSL. Essa é a opção tradicional para associação de SSL.

    1.  Selecione **Adicionar associação**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

Quando o serviço de aplicativo terminar de carregar o certificado, ele aparece na **associações SSL** seções.

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Remapeie o registro para IP SSL

Se o SSL baseado em IP não é usado no aplicativo web, vá para [testar o HTTPS para seu domínio personalizado](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Por padrão, o aplicativo web usa um endereço IP público compartilhado. Quando o certificado está associado com o SSL baseado em IP, o serviço de aplicativo cria um endereço IP dedicado e novo para o aplicativo web.

Quando um registro é mapeado para o aplicativo web, o registro de domínio deve ser atualizado com o endereço IP dedicado.

O **domínio personalizado** página é atualizada com o novo endereço IP dedicado. Copie essa [endereço IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), então remapear o [um registro](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) para esse novo endereço IP.

#### <a name="test-https"></a>Testar HTTPS

Em vários navegadores, navegue até https://<your.custom.domain>to, verifique se o aplicativo web é atendido.

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Se ocorrerem erros de validação de certificado, a causa pode ser um certificado autoassinado ou certificados intermediários podem ter ficados fora ao exportar para o arquivo PFX.

#### <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa pode acessar o aplicativo web usando HTTP. todas as solicitações HTTP para a porta HTTPS poderão ser redirecionadas.

Na página de aplicativo da web, selecione **configurações SL**. Depois, em **HTTPS somente**, selecione **Ligado**.

![Impor HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Quando a operação for concluída, navegue até qualquer uma das URLs HTTP que apontam para o aplicativo. Por exemplo: 

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Impor o TLS 1.1/1.2

O aplicativo permite [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 por padrão, que não é mais considerado seguro pelos padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões superiores do TLS, execute estas etapas:

1.  Na página de aplicativo web, no painel de navegação esquerdo, selecione **configurações de SSL**.

2.  Na **versão do TLS**, selecione a versão mínima do TLS.

![Impor o TLS 1.1 ou 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

1.  Selecione **criar um recurso** > **Networking** > **perfil do Traffic Manager** > **criar**.

2.  Em **Criar perfil do Gerenciador de Tráfego**, preencha os seguintes campos:

    1.  Na **nome**, forneça um nome para o perfil. Esse nome precisa ser exclusivo dentro da zona de manager.net de tráfego e resultados no nome DNS, manager.net de tráfego, que é usado para acessar o perfil do Gerenciador de tráfego.

    2.  Na **método de roteamento**, selecione o **Geographicrouting método**.

    3.  Na **assinatura**, selecione a assinatura sob a qual criar este perfil.

    4.  Em **Grupo de Recursos**, crie um novo grupo de recursos no qual colocar esse perfil.

    5.  Em **Local do grupo de recursos**, selecione o local do grupo de recursos. Essa configuração refere-se para o local do grupo de recursos e não tem impacto sobre o perfil do Gerenciador de tráfego que será implantado globalmente.

    6.  Selecione **Criar**.

    7.  Quando a implantação global do perfil do Traffic Manager for concluída, ele é listado no respectivo grupo de recursos como um dos recursos.

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

1.  Na barra de pesquisa de portais, pesquise o * * perfil do Traffic Manager * * nome criado na seção anterior e selecione o perfil do Gerenciador de tráfego nos resultados que exibidos.

2.  Na **perfil do Gerenciador de tráfego**, no **configurações** seção, selecione **pontos de extremidade**.

3.  Selecione **Adicionar**.

4.  Adicionando o ponto de extremidade do Azure Stack.

5.  Para **tipo**, selecione **ponto de extremidade externo**.

6.  Fornecer um **nome** para esse ponto de extremidade, o ideal é que o nome do Azure Stack.

7.  Para nome de domínio totalmente qualificado (**FQDN**), a URL externa de uso para o aplicativo de Web do Azure Stack.

8.  Em mapeamento geográfico, selecione um região/continente onde o recurso está localizado, por exemplo, **na Europa.**

9.  Sob a país/região lista suspensa que aparece, selecione o país em que se aplicam a esse ponto de extremidade, por exemplo, **Alemanha**.

10. Mantenha a opção **Adicionar como desabilitado** desmarcada.

11. Selecione **OK**.

12. Adicionando o ponto de extremidade do Azure:

    1.  Para **tipo**, selecione **ponto de extremidade do Azure**.

    2.  Fornecer um **nome** para esse ponto de extremidade.

    3.  Para **tipo de recurso de destino**, selecione **serviço de aplicativo**.

    4.  Para **recurso de destino**, selecione **escolha um serviço de aplicativo** para mostrar a lista de aplicativos Web sob a mesma assinatura. Na **recurso**, escolher o uso do serviço de aplicativo como o primeiro ponto de extremidade.

13. Em mapeamento geográfico, selecione um região/continente onde o recurso está localizado, por exemplo, **América América do Norte/Central/Caribe.**

14. Sob a país/região lista suspensa que aparece, deixe em branco para selecionar todo o agrupamento regional acima.

15. Mantenha a opção **Adicionar como desabilitado** desmarcada.

16. Selecione **OK**

  > [!Note]  
  >  Crie pelo menos um ponto de extremidade com um escopo geográfico de todos os (mundo) para servir como ponto de extremidade padrão para o recurso.

1.  Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos em **Perfil do Gerenciador de Tráfego** com seu status de monitoramento como **Online**.

  ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**Empresa global depende dos recursos do Azure a distribuição geográfica**

Direcionar o tráfego de dados por meio do Gerenciador de tráfego do Azure e pontos de extremidade específicos de Geografia permite que as empresas globais cumprir as normas regionais e manter os dados em conformidade e seguro crucial para o sucesso dos negócios local e entre locais remotos.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
