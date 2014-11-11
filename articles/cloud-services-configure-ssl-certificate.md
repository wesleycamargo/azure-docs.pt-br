<properties linkid="dev-net-commons-tasks-enable-ssl" urlDisplayName="Enable SSL" pageTitle="Configure SSL for a cloud service - Azure" metaKeywords="Azure SSL, Azure HTTPS, Azure SSL, Azure HTTPS, .NET Azure SSL, .NET Azure HTTPS, C# Azure SSL, C# Azure HTTPS, VB Azure SSL, VB Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Configuring SSL for an application in Azure" authors="timlt" solutions="" manager="timlt" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Configurando SSL para um aplicativo no Azure

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

A criptografia SSL (Secure Socket Layer) é o método mais usado para proteger dados enviados pela Internet. Esta tarefa comum aborda como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo.

<div class="dev-callout">Observa&ccedil;&atilde;o
<p>Os procedimentos nesta tarefa se aplicam aos Servi&ccedil;os de Nuvem do Azure; para Sites, consulte <a href="../web-sites-configure-ssl-certificate/">Configurando um certificado SSL para um site do Azure</a>.</p>
</div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Obter um Certificado SSL][Etapa 1: Obter um Certificado SSL]
-   [Etapa 2: Modificar a definição do serviço e os arquivos de configuração][Etapa 2: Modificar a definição do serviço e os arquivos de configuração]
-   [Etapa 3: Carregar o pacote de implantação e certificado][Etapa 3: Carregar o pacote de implantação e certificado]
-   [Etapa 4: Conectar-se à instância da função usando HTTPS][Etapa 4: Conectar-se à instância da função usando HTTPS]

Esta tarefa usará uma implantação da produção; as informações sobre como usar uma implantação de preparo ao final deste tópico.

## <a name="step1"> </a><span class="short-header">Obter um certificado SSL</span>Etapa 1: Obter um Certificado SSL

Para configurar SSL de um aplicativo, você deve primeiro obter um certificado SSL assinado por uma Autoridade de Certificação (CA), um terceiro confiável que emita certificados com essa finalidade. Se ainda não tiver um, precisará obter junto a uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

-   O certificado deve conter uma chave privada.
-   O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
-   O nome de assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para acessar o aplicativo. Por exemplo, se o nome de domínio personalizado for **contoso.com**, você pode solicitar um certificado da autoridade de certificação para ***.contoso.com** ou **www.contoso.com**.
-   O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

Para finalidades de teste, você pode criar e usar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma autoridade de certificação e pode usar o domínio cloudapp.net como a URL do site. Por exemplo, a tarefa a seguir usa um certificado autoassinado na qual o nome comum (CN) utilizado no certificado é **sslexample.cloudapp.net**. Para obter detalhes sobre como criar um certificado autoassinado usando o Gerenciador do IIS, consulte [Como criar um certificado para uma função][Como criar um certificado para uma função].

Em seguida, você deve incluir informações sobre o certificado nos arquivos de definição e configuração do serviço.

## <a name="step2"> </a><span class="short-header">Modificar arquivos svc/config</span>Etapa 2: Modificar a definição do serviço e os arquivos de configuração

O aplicativo deve ser configurado para usar o certificado, e um ponto de extremidade HTTPS deve ser adicionado. Dessa forma, os arquivos de definição e configuração do serviço precisam ser atualizados.

1.  No ambiente de desenvolvimento, abra o arquivo de definição do serviço(CSDEF), adicione uma seção **Certificados** dentro da seção **WebRole** e inclua as seguintes informações sobre o certificado:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    A seção **Certificados** define o nome do nosso certificado, seu local e o nome do repositório no qual está localizado. Optamos por armazenar o certificado no repositório CA, mas é possível escolher outras opções também. Consulte [Como associar um certificado a um serviço][Como associar um certificado a um serviço] para obter mais informações.

2.  No arquivo de definição do serviço, adicione um elemento **InputEndpoint** dentro da seção **Pontos de extremidade** para habilitar HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  No arquivo de definição do serviço, adicione uma **Associação** dentro da seção **Sites**. Isso adiciona uma associação HTTP para mapear o ponto de extremidade para o site:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Todas as alterações obrigatórias no arquivo de definição do serviço foram concluídas, mas você continua precisando adicionar as informações de certificado ao arquivo de configuração do serviço.

4.  No arquivo de configuração do serviço (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione uma seção **Certificados** na seção **Função**, substituindo o valor de exemplo thumbprint mostrado abaixo pelo seu certificado:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(O exemplo acima usa **sha1** para o algoritmo de impressão digita. Especifique o valor apropriado ao algoritmo thumbprint do certificado.)

Agora que os arquivos de definição e configuração do serviço foram atualizados, empacote a implantação para carregamento no Azure. Se você está usando **cspack**, certifique-se de que você não use o sinalizador **/generateConfigurationFile**, pois ele substituirá as informações de certificado que você acabou de inserir.

## <a name="step3"> </a><span class="short-header">Carregar no Azure</span>Etapa 3: Carregar o pacote de implantação e certificado

O pacote de implantação foi atualizado para usar o certificado, e um ponto de extremidade HTTPS foi adicionado. Agora é possível carregar o pacote e o certificado no Azure com o Portal de Gerenciamento.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **Novo**, em **Serviço de Nuvem** e em **Criação Personalizada**.
3.  Na caixa de diálogo **Criar um serviço de nuvem**, insira valores para o URL, grupo de região/afinidade e assinatura. Verifique se **Implantar um pacote de serviço de nuvem agora** está marcado e clique no botão **Avançar**.
4.  Na caixa de diálogo **Publicar o serviço de nuvem**, insira as informações obrigatórias do serviço de nuvem, selecione **Produção** para o ambiente e verifique se **Adicionar certificados agora** está marcado. (Se alguma das funções contiver uma única instância, verifique se **Implantar mesmo se uma ou mais funções contiverem uma única instância** está marcado.)

    ![Publicar o serviço de nuvem][Publicar o serviço de nuvem]

5.  Clique no botão **Avançar**.
6.  Na caixa de diálogo **Adicionar certificado**, insira o local do arquivo do certificado .pfx, a senha do certificado e clique em **anexar certificado**.

    ![Adicionar certificado][Adicionar certificado]

7.  Verifique se o certificado está listado na seção **Certificados anexados**.

    ![Certificados anexados][Certificados anexados]

8.  Clique no botão **Concluir para criar o serviço de nuvem**. Quando a implantação alcançar o status **Pronto**, será possível passar às próximas etapas.

## <a name="step4"> </a><span class="short-header">Conectar usando o HTTPS</span>Etapa 4: Conectar-se à instância da função usando HTTPS

Agora que sua implantação está em funcionamento no Azure, você pode conectar-se a ela usando HTTPS.

1.  No Portal de Gerenciamento, selecione a implantação e clique no link em **URL do Site**.

    ![Determinar URL do site][Determinar URL do site]

2.  No navegador da Web, modifique o link para usar **https** em vez de **http**, e visite a página.

    **Observação:** Se estiver usando um certificado autoassinado, ao navegar até um ponto de extremidade HTTPS associado com o certificado autoassinado, você verá um erro de certificado no navegador. O uso de um certificado assinado por uma certificação confiável eliminará esse problema; enquanto isso, você poderá ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao repositório de certificado da autoridade de certificado confiável do usuário.)

    ![Site de exemplo SSL][Site de exemplo SSL]

Se quiser usar SSL em uma implantação de preparação em lugar de uma implantação da produção, você precisará determinar primeiro o URL usado na implantação de preparação. Implante o serviço de nuvem no ambiente de preparação sem incluir um certificado ou qualquer informação de certificado. Depois de implantado, você poderá determinar a URL baseada em GUID, listada no campo **URL do Site** do portal de gerenciamento. Crie um certificado com o nome comum (CN) igual a URL baseada em GUID (por exemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), use o portal de gerenciamento para adicionar o certificado ao serviço de nuvem de preparação, adicione as informações de certificado aos arquivos CSDEF e CSCFG, reempacote o aplicativo e atualize a implantação de preparação para usar o novo pacote e o arquivo CSCFG.

## <a name="additional_resources"> </a><span class="short-header">Recursos adicionais</span>Recursos adicionais

-   [Como associar um certificado a um serviço][Como associar um certificado a um serviço]

-   [Como configurar um certificado SSL em um ponto de extremidade HTTPS][Como configurar um certificado SSL em um ponto de extremidade HTTPS]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [Etapa 1: Obter um Certificado SSL]: #step1
  [Etapa 2: Modificar a definição do serviço e os arquivos de configuração]: #step2
  [Etapa 3: Carregar o pacote de implantação e certificado]: #step3
  [Etapa 4: Conectar-se à instância da função usando HTTPS]: #step4
  [Como criar um certificado para uma função]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg432987.aspx
  [Como associar um certificado a um serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg465718.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Publicar o serviço de nuvem]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [Adicionar certificado]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [Certificados anexados]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png
  [Determinar URL do site]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [Site de exemplo SSL]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx
