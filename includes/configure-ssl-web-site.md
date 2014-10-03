#Habilitar HTTPS para um site do Azure

Quando alguém visita seu site utilizando HTTPS, a comunicação entre o site e o navegador é protegida pelo uso de criptografia SSL. Esse é o método de proteção de dados enviados pela internet usado com maior frequência e garante aos visitantes que as transações com o seu site são seguras. Este artigo descreve como habilitar HTTPS para um site do Azure.

> [WACOM.NOTE] Para habilitar HTTPS para nomes de domínio personalizados, você deve configurar seus sites no modo padrão. Isso pode resultar em custos adicionais se você estiver usando modo gratuito ou compartilhado. Para obter mais informações sobre os preços do modo compartilhado e padrão, consulte [Detalhes de preços][]. Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure][].

[][]

##O domínio \*.azurewebsites.net

Se você não estiver planejando usar um nome de domínio personalizado, e sim o domínio \*.azurewebsites.net atribuído ao seu site pelo Azure (por exemplo, contoso.azurewebsites.net), seu site já estará protegido por um certificado fornecido pela Microsoft. Você pode usar **https://mywebsite.azurewebsites.net** para acessar seu site de maneira segura. Todavia, \*.azurewebsites.net é um domínio compartilhado e, como todos os domínios compartilhados, não é tão seguro quanto utilizar um domínio personalizado com seu próprio certificado.

O restante deste documento fornece detalhes sobre a habilitação de HTTPS para nomes de domínio personalizados, tais como **contoso.com**, **www.contoso.com** ou **\*.contoso.com**

[][1]

##Nomes de domínio personalizados

Para habilitar HTTPS para um nome de domínio personalizado, como **contoso.com**, você deve registrar um nome de domínio personalizado em um registro de nomes de domínio. Para obter mais informações sobre como configurar o nome de domínio de um site do Azure, consulte [Configurando um nome de domínio personalizado para um site do Azure][]. Depois de registrar o nome de domínio personalizado e configurar seu site para responder ao nome personalizado, solicite um certificado SSL para o domínio.

Registrar um nome de domínio também permite que você crie subdomínios como **www.contoso.com** ou **mail.contoso.com** Antes de solicitar um certificado SSL, você deve primeiro determinar quais nomes de domínio serão protegidos pelo certificado. Isso determinará o tipo de certificado que você precisa ter. Se você precisar proteger um único nome de domínio como **contoso.com** ou **www.contoso.com**, um certificado básico provavelmente será suficiente. Se você precisar proteger vários nomes de domínio, como **contoso.com**, **www.contoso.com** e **mail.contoso.com**, um certificado curinga ou um certificado com o nome alternativo da entidade (subjectAltName, SAN) será necessário.

> [WACOM.NOTE] A maioria dos navegadores exibirá um aviso se o nome de domínio especificado no certificado não coincidir com o nome de domínio que foi inserido no navegador. Por exemplo, se o certificado listar apenas www.contoso.com, mas login.contoso.com for o nome de domínio usado para acessar o site no Internet Explorer, você receberá um aviso informando que "o certificado de segurança apresentado pelo site foi emitido para um outro endereço."

**Certificados básicos** são certificados em que o CN (nome comum) do certificado é definido para um domínio específico ou subdomínio que os clientes usarão para visitar o site. Por exemplo, **www.contoso.com**. Esses certificados protegem apenas o nome de domínio especificado pelo CN.

**Certificados curinga** são certificados em que o CN do certificado contém um caractere curinga '\*' no nível do subdomínio. Isso permite que o certificado corresponda a um único nível de subdomínios de um determinado domínio. Por exemplo, um certificado curinga para **\*.contoso.com** seria válido para **www.contoso.com**, **payment.contoso.com** e **login.contoso.com**. Ele não seria válido para **test.login.contoso.com**, já que adiciona um nível extra de subdomínio. Ele também não é válido para **contoso.com**, pois esse é o nível do domínio raiz e não um subdomínio.

Um certificado curinga é o que a Microsoft fornece para o nome de domínio *. azurewebsites.net criado automaticamente para seu site.

**subjectAltName** é uma extensão de certificado que permite a associação de vários valores ou nomes alternativos de entidade a um certificado. Para certificados SSL, isso permite que você adicione outros nomes DNS que também serão válidos com o certificado. Por exemplo, um certificado usando subjectAltName pode ter um CN de **contoso.com**, mas também pode ter nomes alternativos como **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com** e até mesmo **fabrikam.com** Esse tipo de certificado será válido para todos os nomes de domínio especificados no nome comum e no subjectAltName.

É possível que um certificado ofereça suporte a caracteres curinga e subjectAltName.

[][2]

##Obter um certificado

Certificados SSL usados com os Sites do Azure devem ser assinados por uma AC (autoridade de certificação), um terceiro confiável que emite certificados para essa finalidade. Se ainda não tiver um, precisará obter junto a uma empresa que venda certificados SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][] no Microsoft TechNet Wiki.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

-   O certificado deve conter uma chave privada.

-   O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).

-   O nome da entidade do certificado deve corresponder ao domínio usado para acessar o site. Se for necessário atender a vários domínios com esse certificado, você precisará usar um valor curinga ou especificar valores subjectAltName conforme discutido anteriormente.

    -   Para obter informações sobre a configuração de um nome de domínio personalizado para um site do Azure, consulte [Configurando um nome de domínio personalizado para um Site do Azure][Configurando um nome de domínio personalizado para um site do Azure].

    > [WACOM.NOTE] Não tente obter ou gerar um certificado para o domínio azurewebsites.net.

-   O certificado deve usar, no mínimo, uma criptografia de 2.048 bits.

> [WACOM.NOTE] Os Sites do Azure não dão suporte a certificados emitidos por servidores de AC privados.

Para obter um certificado SSL de uma autoridade de certificação, é necessário gerar uma CSR (Solicitação de Assinatura de Certificado), que é enviada para a AC. A AC retornará um certificado que é usado para complementar a CSR. Duas maneiras de gerar um CSR são usando o certmgr.exe ou aplicativos [OpenSSL][]. Certmgr.exe só está disponível para Windows, enquanto que o OpenSSL está disponível para a maioria das plataformas. A seguir estão as etapas para usar esses recursos.

> [WACOM.NOTE] Há suporte aos Certificados ECC (Criptografia de Curva Elíptica) com os Sites do Azure; todavia, eles são relativamente novos e é necessário planejar junto com sua AC as etapas exatas para a criação da CSR. Depois que tiver obtido um certificado ECC, você poderá carregá-lo em seu site, conforme descrito nas etapas abaixo.

Talvez você também precise obter **certificados intermediários** (também conhecidos como certificados de cadeia), caso eles sejam usados por sua AC. O uso de certificados intermediários é considerado mais seguro do que “certificados sem cadeia”, por isso é comum que uma AC os use. Certificados intermediários geralmente são fornecidos como um download separado do site das ACs. As etapas neste artigo mostram como garantir que todos os certificados intermediários sejam mesclados com o certificado carregado em seu site do Azure.

> [WACOM.NOTE] Ao seguir qualquer uma das séries de etapas, será solicitado que você insira um **nome comum**. Se você obter um certificado curinga para uso em vários domínios (www.contoso.com, sales.contoso.com), esse valor deverá ser \*.domainname (por exemplo, \*.contoso.com). Se você obtiver certificado para um único nome de domínio, o valor deverá ser o valor exato que os usuários inserirão no navegador para visitar seu site. Por exemplo, www.contoso.com.
>
> Se você precisar dar suporte tanto a um nome curinga como \*.contoso.com quanto a um nome de domínio raiz como contoso.com, será possível usar um certificado SAN (nome alternativo da entidade) curinga. Para ver exemplos de criação de uma solicitação de certificado que usa as extensões SubjectAltName, consulte [Certificado SubjectAltName][].
>
> Para obter mais informações sobre como configurar o nome de domínio de um site do Azure, consulte [Configurando um nome de domínio personalizado para um Site do Azure][Configurando um nome de domínio personalizado para um site do Azure].

###Obter um certificado usando Certreq.exe (somente Windows)

O certreq.exe é um utilitário do Windows para criar solicitações de certificado. Ele faz parte da instalação básica do Windows desde o Windows XP/Windows Server 2000, por isso deve estar disponível nos sistemas Windows recentes. Siga as etapas para obter um certificado SSL usando o certreq.exe.

Se você deseja criar um certificado autoassinado para fins de teste, consulte a seção [Certificados autoassinados][] deste documento.

Se você desejar usar o Gerenciador do IIS para criar uma solicitação de certificado, consulte a seção [Obter um certificado usando o Gerenciador do IIS][].

1.  Abra o **Bloco de notas** e crie um novo documento com o seguinte. Substitua **mysite.com** na linha de Assunto pelo nome de domínio personalizado de seu site. Por exemplo, assunto = "CN=www.contoso.com".

        [NewRequest]
        Subject = "CN=mysite.com"
        Exportable = TRUE
        KeyLength = 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        RequestType = CMC

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1

    Para obter mais informações sobre as opções especificadas acima, bem como outras opções disponíveis, consulte a [documentação de referência do CertReq][].

2.  Salve o arquivo de texto como **myrequest.txt**.

3.  Na **tela Iniciar** ou no **Menu Iniciar**, execute o **cmd.exe**.

4.  No prompt de comando, use o seguinte comando para criar o arquivo de solicitação de certificado:

        certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

    Especifique o caminho para o arquivo **myrequest.txt** criado na etapa 1 e o caminho a ser usado para o arquivo **myrequest.csr**.

5.  Envie o **myrequest.csr** a uma Autoridade de Certificação para obter um certificado SSL. Pode ser necessário carregar o arquivo ou abri-lo no bloco de notas e colar o conteúdo diretamente no formulário da web.

    Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][] no Microsoft TechNet Wiki.

6.  Depois que a Autoridade de Certificação fornecer um arquivo de certificado (.CER), você deverá salvá-lo no computador usado para gerar a solicitação e usar o seguinte comando para aceitar a solicitação e concluir o processo de geração do certificado.

        certreq -accept -user mycert.cer

    Nesse caso, o certificado **mycert.cer** recebido da Autoridade de Certificação será usado para concluir a assinatura do certificado. Nenhum arquivo será criado. Em vez disso, o certificado será armazenado no repositório de certificados do Windows.

7.  Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado nas próximas etapas. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

    Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

8.  Para exportar o certificado do repositório de certificados, execute **certmgr.msc** na **tela Iniciar** ou no **Menu Iniciar**. Quando o **Gerenciador de certificados** aparecer, expanda a pasta **Pessoal** e selecione **Certificados**. No campo **Emitido Para**, pesquise a entrada com o nome de domínio personalizado referente ao certificado solicitado. No campo **Emitido Por** deve aparecer a Autoridade de Certificação usada para esse certificado.

    ![inserir a imagem do gerenciador de certificado aqui][]

9.  Clique com o botão direito no certificado e selecione **Todas as Tarefas** e depois **Exportar**. No **Assistente de exportação de certificados**, clique em **Avançar** e, em seguida, selecione **Sim, exportar a chave particular**. Clique em **Próximo**.

    ![Exporte a chave particular][]

10. Selecione **Troca de Informações Pessoais - PKCS #12**, **Incluir Todos os Certificados na Cadeia de Certificados** e **Exportar Todas as Propriedades Estendidas**. Clique em **Próximo**.

    ![incluir todos os certificados e propriedades estendidas][]

11. Selecione **Senha**, insira e confirme a senha. Clique em **Próximo**.

    ![especifique uma senha][]

12. Determine um caminho e o nome do arquivo que conterá o certificado exportado. O nome do arquivo deve ter uma extensão **.pfx**. Clique em **Avançar** para concluir o processo.

    ![forneça um caminho do arquivo][]

Agora você pode carregar o arquivo PFX exportado em seu site do Azure.

###Obter um certificado usando OpenSSL

1.  Gere uma chave particular e uma Solicitação de Assinatura de Certificado (CSR) usando o seguinte de uma sessão de linha de comando, bash ou terminal:

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  Quando solicitado, digite as informações adequadas. Por exemplo:

        Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []: 

    Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

3.  Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][] no Microsoft TechNet Wiki.

4.  Depois de obter um certificado de uma autoridade de certificação, salve-o em um arquivo chamado **myserver.crt**. Se a autoridade de certificação tiver fornecido o certificado em formato de texto, cole o texto do certificado no arquivo **myserver.crt**. O conteúdo do arquivo deve ser semelhante ao seguinte quando visualizado em um editor de texto:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

    Salve o arquivo.

5.  Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelos Sites do Azure:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	<div class="dev-callout"> 
	<b>Observa&ccedil;&atilde;o</b>
	<p>Se sua CA usar certificados intermedi&aacute;rios, voc&ecirc; deve instalar esses certificados antes de exportar o certificado na pr&oacute;xima etapa. Geralmente esses certificados s&atilde;o fornecidos como um download separado da autoridade de certifica&ccedil;&atilde;o e s&atilde;o fornecidos em v&aacute;rios formatos para tipos de servidor diferentes. Selecione a vers&atilde;o que &eacute; fornecida como um arquivo PEM (extens&atilde;o de arquivo .pem).</p>
	<p>O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermedi&aacute;rios contidos no arquivo <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

Depois de executar este comando, você deve ter um arquivo **myserver.pfx** apropriado para uso nos Sites do Azure.

[][3]

##Configure o modo padrão

A habilitação de HTTPS para um domínio personalizado só está disponível para o modo padrão nos sites do Azure. Use as etapas a seguir para alternar para o modo padrão.

> [WACOM.NOTE] Antes de mudar um site do modo de site gratuito para o modo padrão, remova os limites de gastos em vigor na assinatura do site, caso contrário, ele poderá ficar indisponível se o limite for alcançado antes do fim do período de cobrança. Para obter mais informações sobre os preços do modo compartilhado e padrão, consulte [Detalhes de preços][].

1.  No navegador, abra o [Portal de gerenciamento][].

2.  Na guia **Sites**, clique no nome do seu site.

    ![selecionando um site][]

3.  Clique na guia **ESCALA**.

    ![Na guia Escala][]

4.  Na seção **geral**, defina o modo do site clicando em **PADRÃO**.

    ![modo padrão selecionado][]

5.  Clique em **Salvar**. Quando solicitado, clique em **Sim**.

    > [WACOM.NOTE] Se você receber um erro “Falha na configuração de escala para o site '&lt;nome do site&gt;'", você poderá usar o botão Detalhes para obter mais informações. Você pode receber um erro "Não servidores de instância padrão disponíveis para atender a esta solicitação". Se você receber esse erro, contate o [suporte do Azure][].

[][4]

##Configurar SSL

Antes de executar as etapas nesta seção, você deve ter associado um nome de domínio personalizado ao seu site do Azure. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para um Site do Azure][Configurando um nome de domínio personalizado para um site do Azure].

1.  No seu navegador, abra o [Portal de Gerenciamento do Azure][Portal de gerenciamento].

2.  Na guia **Sites**, clique no nome do seu site e selecione a guia **CONFIGURAR**.

    ![a guia configurar][]

3.  Na seção **Certificados**, clique em **Carregar um Certificado**

    ![carregar um certificado][]

4.  Usando a caixa de diálogo **Carregar um certificado**, selecione o arquivo de certificado .pfx criado anteriormente usando o Gerenciador do IIS ou OpenSSL. Digite a senha que foi usada para proteger o arquivo .pfx, se houver. Por fim, clique em **Verificação** para carregar o certificado.

    ![caixa de diálogo carregar certificado][]

5.  Na seção **Associações SSL** da guia **CONFIGURAR**, use os menus suspensos para selecionar o nome de domínio a ser protegido com o SSL e o certificado a ser usado. Você também pode selecionar se deseja usar [Indicação de nome do servidor][] (SNI) ou SSL baseado em IP.

    ![associações SSL][]

    -   O SSL baseado em IP associa um certificado a um nome de domínio mapeando um endereço IP público dedicado do servidor ao nome de domínio. Isso exige que cada nome de domínio (contoso.com, fabricam.com etc.), associado ao seu serviço tenha um endereço IP dedicado. Esse é o método tradicional de associar certificados SSL a um servidor web.

    -   O SSL baseado em SNI é uma extensão para SSL e [protocolo TLS][] que permite que vários domínios compartilhem o mesmo endereço IP, com certificados de segurança separados para cada domínio. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) oferecem suporte ao SNI. No entanto, navegadores mais antigos podem não oferecer esse suporte. Para obter mais informações sobre SNI, consulte o artigo [Indicação de nome do servidor][] no Wikipédia.

6.  Clique em **Salvar** para salvar as alterações e habilitar SSL.

> [WACOM.NOTE] Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado por meio de um registro A, execute as seguintes etapas adicionais:
>
> 1.  Depois de configurar uma associação de SSL baseado em IP, um endereço IP dedicado é atribuído ao seu site. Você encontrará esse endereço IP na página **Painel** do seu site, na seção **visão rápida**. Ele será listado como **Endereço IP Virtual**:
>
>     ![Endereço IP virtual][]
>
>     Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI, ou não estiver configurado para usar SSL, nenhum endereço será listado para essa entrada.
>
> 2.  Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior.
>
Nesse momento, você poderá visitar o seu site usando HTTPS para verificar se o certificado foi configurado corretamente.

[][5]

##Certificados SubjectAltName (opcional)

OpenSSL pode ser usado para criar uma solicitação de certificado que usa a extensão SubjectAltName para oferecer suporte a vários nomes de domínio com um único certificado. No entanto, ele requer um arquivo de configuração. As etapas a seguir abordam a criação de um arquivo de configuração e, depois, o uso dele para solicitar um certificado.

1.  Crie um novo arquivo chamado **sancert.cnf** e use o seguinte como o conteúdo do arquivo:

        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default = 
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default = 
        localityName = Locality Name (eg, city)
        localityName_default = 
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  = 
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----

    Observe a linha que começa com 'subjectAltName'. Substitua os nomes de domínio listados pelos nomes de domínio que você deseja oferecer suporte além do nome comum. Por exemplo:

        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

    Não é preciso alterar o campo commonName\_default, pois será solicitado que você insira seu nome comum em uma das etapas seguintes.

2.  Salve o arquivo **sancert.cnf**.

3.  Gere uma chave particular e a Solicitação de Assinatura de Certificado usando o arquivo de configuração sancert.cnf. Em uma sessão de terminal ou bash, use o seguinte comando:

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  Quando solicitado, digite as informações adequadas. Por exemplo:

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

5.  Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][] no Microsoft TechNet Wiki.

6.  Depois de obter um certificado de uma autoridade de certificação, salve-o em um arquivo chamado **myserver.crt**. Se a autoridade de certificação tiver fornecido o certificado em formato de texto, cole o texto do certificado no arquivo **myserver.crt**. O conteúdo do arquivo deve ser semelhante ao seguinte quando visualizado em um editor de texto:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

    Salve o arquivo.

7.  Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelos Sites do Azure:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, digite uma senha para proteger o arquivo.pfx.

    <div class="dev-callout"> 
	<b>Observa&ccedil;&atilde;o</b>
	<p>Se sua CA usar certificados intermedi&aacute;rios, voc&ecirc; deve instalar esses certificados antes de exportar o certificado na pr&oacute;xima etapa. Geralmente esses certificados s&atilde;o fornecidos como um download separado da autoridade de certifica&ccedil;&atilde;o e s&atilde;o fornecidos em v&aacute;rios formatos para tipos de servidor diferentes. Selecione a vers&atilde;o que &eacute; fornecida como um arquivo PEM (extens&atilde;o de arquivo .pem).</p>
	<p>O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermedi&aacute;rios contidos no arquivo <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

    Depois de executar este comando, você deve ter um arquivo **myserver.pfx** apropriado para uso nos Sites do Azure.

##<a name="bkmk_iismgr"></a>Obtenha um certificado usando o Gerenciador do IIS (opcional)

Se você estiver familiarizado com o Gerenciador do IIS, é possível usá-lo para gerar um certificado que pode ser usado com os Sites do Azure.

1.  Gere uma Solicitação de Assinatura de Certificado (CSR) com o Gerenciador do IIS para enviar à Autoridade de Certificação. Para obter mais informações sobre como gerar um CSR, consulte [Solicitar um certificado de servidor para a Internet (IIS 7)][].

2.  Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][] no Microsoft TechNet Wiki.

3.  Conclua o CSR com o certificado fornecido pelo fornecedor de Autoridade de Certificação. Para obter mais informações sobre a conclusão do CSR, consulte [Instalar um certificado de servidor para a Internet (IIS 7)][].

4.  Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

    Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

5.  Exportar o certificado do Gerenciador do IIS Para obter mais informações sobre como exportar o certificado, consulte [Exportar um certificado do servidor (IIS 7)][]. O arquivo exportado será usado nas etapas posteriores para ser carregado no Azure para uso nos Sites do Azure.

    <div class="dev-callout"> 
	<b>Observa&ccedil;&atilde;o</b>
	<p>Durante o processo de exporta&ccedil;&atilde;o, verifique se voc&ecirc; selecionou a op&ccedil;&atilde;o <strong>Sim, exportar a chave privada</strong>. Isso incluir&aacute; a chave privada no certificado exportado.</p>
	</div>

    <div class="dev-callout"> 
	<b>Observa&ccedil;&atilde;o</b>
	<p>Durante o processo de exporta&ccedil;&atilde;o, verifique se voc&ecirc; selecionou as op&ccedil;&otilde;es <strong>incluir todos os certificados no caminho de certifica&ccedil;&atilde;o</strong> e <strong>Exportar todas as propriedades estendidas</strong>. Isso incluir&aacute; todos os certificados intermedi&aacute;rios no certificado exportado.</p>
	</div>

[][6]

##Certificados autoassinados (opcional)

Em alguns casos você poderá obter um certificado para fins de teste e, com isso, adiar a compra de um certificado confiável até a entrada em produção. Os certificados autoassinados podem preencher essa lacuna. Um certificado autoassinado é um certificado que você cria e assina como se fosse uma autoridade de certificação. Embora este certificado possa ser usado para proteger um site, a maioria dos navegadores retornará erros ao visitar o site, já que o certificado não foi assinado por uma AC confiável. Alguns navegadores podem até mesmo não permitir a exibição do site.

Embora haja várias maneiras de criar um certificado autoassinado, este artigo fornece apenas informações sobre o uso de **makecert** e **OpenSSL**.

###Criar um certificado autoassinado usando makecert

Você pode criar um certificado de teste em um sistema do Windows com Visual Studio instalado executando as seguintes etapas:

1.  Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Prompt de comando do desenvolvedor**. Por fim, clique com o botão direito no **Prompt de comando do desenvolvedor** e selecione **Executar como Administrador**.

    Se você receber uma caixa de diálogo Controle de Conta de Usuário, selecione **Sim** para continuar.

2.  No Prompt de comando do desenvolvedor, use o seguinte comando para criar um novo certificado autoassinado. Você deve substituir **serverdnsname** pelo DNS de seu site.

        makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

    Este comando criará um certificado que é válido entre as datas de 01/01/2013 e 01/01/2014 e armazenará o local no repositório de certificados CurrentUser.

3.  Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Windows PowerShell** e inicie este aplicativo.

4.  No prompt do Windows PowerShell, use os seguintes comandos para exportar o certificado criado anteriormente:

        $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
        get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

    Isso armazena a senha especificada como uma cadeia de caracteres segura em $mypwd e localiza o certificado usando o nome DNS especificado pelo parâmetro **dnsname**, e exporta para o arquivo especificado pelo parâmetro **filepath**. A cadeia de segura que contém a senha é usada para proteger o arquivo exportado.

###Criar um certificado autoassinado usando OpenSSL

1.  Crie um novo documento chamado **serverauth.cnf** usando o seguinte como o conteúdo deste arquivo:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your website's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

    Isso especifica as definições de configuração necessárias para produzir um certificado SSL que pode ser usado pelos Sites do Azure.

2.  Gere um novo certificado autoassinado usando uma das opções de uma sessão de linha de comando, bash ou terminal:

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    Isso cria um novo certificado usando as definições de configuração especificadas no arquivo **serverauth.cnf**.

3.  Para exportar o certificado para um arquivo .PFX que pode ser carregado em um Site do Azure, use o seguinte comando:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, digite uma senha para proteger o arquivo.pfx.

    O **myserver.pfx** produzido por esse comando pode ser usado para proteger seu Site do Windows Azure para fins de teste.

  [Detalhes de preços]: https://www.windowsazure.com/en-us/pricing/details/
  [Avaliação Gratuita do Microsoft Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  []: bkmk_azurewebsites
  [1]: bkmk_domainname
  [Configurando um nome de domínio personalizado para um site do Azure]: /en-us/develop/net/common-tasks/custom-dns-web-site/
  [2]: bkmk_getcert
  [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)]: http://go.microsoft.com/fwlink/?LinkID=269988
  [OpenSSL]: http://www.openssl.org/
  [Certificado SubjectAltName]: #bkmk_subjectaltname
  [Certificados autoassinados]: #bkmk_selfsigned
  [Obter um certificado usando o Gerenciador do IIS]: #bkmk_iismgr
  [documentação de referência do CertReq]: http://technet.microsoft.com/library/cc725793.aspx
  [inserir a imagem do gerenciador de certificado aqui]: ./media/configure-ssl-web-site/waws-certmgr.png
  [Exporte a chave particular]: ./media/configure-ssl-web-site/waws-certwiz1.png
  [incluir todos os certificados e propriedades estendidas]: ./media/configure-ssl-web-site/waws-certwiz2.png
  [especifique uma senha]: ./media/configure-ssl-web-site/waws-certwiz3.png
  [forneça um caminho do arquivo]: ./media/configure-ssl-web-site/waws-certwiz4.png
  [3]: bkmk_standardmode
  [Portal de gerenciamento]: https://manage.windowsazure.com/
  [selecionando um site]: ./media/configure-ssl-web-site/sslwebsite.png
  [Na guia Escala]: ./media/configure-ssl-web-site/sslscale.png
  [modo padrão selecionado]: ./media/configure-ssl-web-site/sslreserved.png
  [suporte do Azure]: http://www.windowsazure.com/en-us/support/options/
  [4]: bkmk_configuressl
  [a guia configurar]: ./media/configure-ssl-web-site/sslconfig.png
  [carregar um certificado]: ./media/configure-ssl-web-site/ssluploadcert.png
  [caixa de diálogo carregar certificado]: ./media/configure-ssl-web-site/ssluploaddlg.png
  [Indicação de nome do servidor]: http://en.wikipedia.org/wiki/Server_Name_Indication
  [associações SSL]: ./media/configure-ssl-web-site/sslbindings.png
  [protocolo TLS]: http://en.wikipedia.org/wiki/Transport_Layer_Security
  [Endereço IP virtual]: ./media/configure-ssl-web-site/staticip.png
  [5]: bkmk_subjectaltname
  [Solicitar um certificado de servidor para a Internet (IIS 7)]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
  [Instalar um certificado de servidor para a Internet (IIS 7)]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
  [Exportar um certificado do servidor (IIS 7)]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx
  [6]: bkmk_selfsigned
