---
title: "Segurança de comunicações - Microsoft Threat Modeling Tool - Azure | Microsoft Docs"
description: "atenuações de ameaças expostas na Ferramenta de Modelagem de Ameaças"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 8251f44200c11d3efcec04b7ac99857232b2f9ed
ms.openlocfilehash: 4bb69046373971b43bc8c92651daf4e153d28555
ms.lasthandoff: 02/15/2017


---

# <a name="security-frame-communication-security--mitigations"></a>Estrutura de segurança: Segurança de comunicações | Atenuações 
| Produto/serviço | Artigo |
| --------------- | ------- |
| Hub de Eventos do Azure | <ul><li>[Proteger comunicações para o Hub de Eventos usando SSL/TLS](#comm-ssltls)</li></ul> |
| Dynamics CRM | <ul><li>[Verificar se os privilégios da conta do serviço e verificar se os serviços ou páginas ASP.NET personalizados respeitam a segurança do CRM](#priv-aspnet)</li></ul> |
| Fábrica de dados do Azure | <ul><li>[Usar o Gateway de Gerenciamento de Dados durante a conexão local do SQL Server com o Azure Data Factory](#sqlserver-factory)</li></ul> |
| Servidor de identidade | <ul><li>[Garantir que todo o tráfego para o servidor de identidade passe pela conexão HTTPS](#identity-https)</li></ul> |
| Aplicativo Web | <ul><li>[Verificar os certificados x.&509; usados para autenticar conexões SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurar um certificado SSL para um domínio personalizado no Serviço de Aplicativo do Azure](#ssl-appservice)</li><li>[Forçar todo o tráfego para o Serviço de Aplicativo do Azure pela conexão HTTPS](#appservice-https)</li><li>[Habilitar HSTS (Segurança de Transporte Estrito HTTP)](#http-hsts)</li></ul> |
| Banco de dados | <ul><li>[Garantir a validação de certificado e a criptografia da conexão do SQL Server](#sqlserver-validation)</li><li>[Forçar a comunicação criptografada para o SQL Server](#encrypted-sqlserver)</li></ul> |
| Armazenamento do Azure | <ul><li>[Garantir que as comunicações para o Armazenamento do Azure sejam feitas via HTTPS](#comm-storage)</li><li>[Validar o hash MD5 depois de baixar o blob se o HTTPS não puder ser habilitado](#md5-https)</li><li>[Use um cliente em conformidade com SMB 3.0 para garantir a criptografia de dados em trânsito para os compartilhamentos de arquivos do Azure](#smb-shares)</li></ul> |
| Cliente móvel | <ul><li>[Implementar a anexação de certificado](#cert-pinning)</li></ul> |
| WCF | <ul><li>[Habilitar HTTPS - proteger o canal de transporte](#https-transport)</li><li>[WCF: definir o nível de proteção da segurança de mensagens como EncryptAndSign](#message-protection)</li><li>[WCF: use uma conta com privilégios mínimos para executar o serviço WCF](#least-account-wcf)</li></ul> |
| API Web | <ul><li>[Forçar todo o tráfego para APIs Web pela conexão HTTPS](#webapi-https)</li></ul> |
| Cache Redis do Azure | <ul><li>[Garantir que as comunicações para o Cache Redis do Azure sejam feitas via SSL](#redis-ssl)</li></ul> |
| Gateway de Campo IoT | <ul><li>[Proteger dispositivo para comunicações do Gateway de Campo](#device-field)</li></ul> |
| Gateway de Nuvem IoT | <ul><li>[Proteger o dispositivo para comunicações do Gateway de Nuvem usando SSL/TLS](#device-cloud)</li></ul> |

## <a name="a-idcomm-ssltlsasecure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Proteger comunicações para o Hub de Eventos usando SSL/TLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Hub de Eventos do Azure | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| Etapas | Proteja conexões AMQP ou HTTP com o Hub de Eventos usando SSL/TLS |

## <a name="a-idpriv-aspnetacheck-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Verificar se os privilégios da conta do serviço e verificar se os serviços ou páginas ASP.NET personalizados respeitam a segurança do CRM

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | N/D  |
| Etapas | Verifique se os privilégios da conta do serviço e verifique se os serviços ou páginas ASP.NET personalizados respeitam a segurança do CRM. |

## <a name="a-idsqlserver-factoryause-data-management-gateway-while-connecting-on-prem-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Usar o Gateway de Gerenciamento de Dados durante a conexão local do SQL Server com o Azure Data Factory

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Fábrica de dados do Azure | 
| Fase do SDL               | Implantação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | Tipos de serviços vinculados - no Azure e localmente |
| Referências              |[Movendo dados entre o armazenamento local e o Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [Gateway de Gerenciamento de Dados](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| Etapas | <p>A ferramenta Gateway de Gerenciamento de Dados (DMG) é necessária para conectar fontes de dados protegidas por um firewall ou pela rede corporativa.</p><ol><li>Blindar a máquina isola a ferramenta DMG e evita que programas com defeito danifiquem ou espionem o computador da fonte de dados. Por exemplo, instalando das atualizações mais recentes, habilitando o mínimo necessário de portas, provisionando contas controladas, habilitando a auditoria, habilitando a criptografia de disco, etc.</li><li>A chave do Gateway de Dados precisa ser trocada em intervalos frequentes ou sempre que a senha da conta do serviço do DMG for renovada.</li><li>O tráfego de dados pelo serviço de vinculação deve ser criptografado.</li></ol> |

## <a name="a-ididentity-httpsaensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Garantir que todo o tráfego para o servidor de identidade passe pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Servidor de identidade | 
| Fase do SDL               | Implantação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [IdentityServer3 - chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - implantação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| Etapas | Por padrão, o IdentityServer exige que todas as conexões de entrada sejam feitas via HTTPS. É absolutamente obrigatório que a comunicação com IdentityServer seja feita apenas transportes protegidos. Em alguns cenários de implantação, como o de descarregamento de SSL, esse requisito pode ser relaxado. Consulte a página de implantação do Servidor de identidade indicada nas referências para obter mais informações. |

## <a name="a-idx509-ssltlsaverify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verificar os certificados x.&509; usados para autenticar conexões SSL, TLS e DTLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Aplicativo Web | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | N/D  |
| Etapas | <p>Os aplicativos que usam SSL, TLS ou DTLS devem verificar os certificados X.509 das entidades a que se conectarem. Isso inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e vencimento)</li><li>Status de revogação</li><li>Uso (por exemplo, autenticação de servidor para servidores de autenticação de cliente para clientes)</li><li>Cadeia de confiança (os certificados devem estar vinculados a uma autoridade de certificação (CA) raiz de confiança da plataforma ou serem configurados explicitamente pelo administrador)</li><li>O comprimento da chave pública do certificado deve ser maior que&2;.048 bits</li><li>Algoritmo de hash deve ser SHA256 e superior |

## <a name="a-idssl-appserviceaconfigure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configurar um certificado SSL para um domínio personalizado no Serviço de Aplicativo do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Aplicativo Web | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | Tipo de ambiente - Azure |
| Referências              | [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/) |
| Etapas | Por padrão, o Azure já habilita o HTTPS para todos os aplicativos com um certificado curinga para o domínio *.azurewebsites.net. No entanto, assim como todos os domínios curinga, ele não é tão seguro quanto usar um domínio personalizado com seu próprio certificado. [Consulte este artigo](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). É recomendável habilitar o SSL para o domínio personalizado pelo qual o aplicativo implantado será acessado.|

## <a name="a-idappservice-httpsaforce-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Forçar todo o tráfego para o Serviço de Aplicativo do Azure pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Aplicativo Web | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | Tipo de ambiente - Azure |
| Referências              | [Impor o HTTPS no Serviço de Aplicativo do Azure]https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/#4-enforce-https-on-your-app |
| Etapas | <p>Embora os Serviços de Aplicativos do Azure já habilite o HTTPS com um certificado curinga para o domínio *.azurewebsites.net, ele não impõe o HTPPS. Os visitantes podem continuar acessando o aplicativo com o HTTP, possibilitando que a segurança do aplicativo seja comprometida, por isso o HTTPS deve ser explicitamente imposto. Os aplicativos do ASP.NET MVC devem usar o [filtro RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que obriga uma solicitação HTTP desprotegida a ser reenviada pelo HTTPS.</p><p>O módulo de Reescrita de URL, incluído no Serviço de Aplicativo do Azure, também pode ser usado para impor o HTTPS. Esse módulo permite que desenvolvedores definam as regras aplicadas às solicitações recebidas antes que elas cheguem ao aplicativo. As regras de Reescrita de URL são definidas em um arquivo web.config, que fica armazenado na raiz do aplicativo.</p>|

### <a name="example"></a>Exemplo
O exemplo abaixo contém uma regra básica de Reescrita de URL que força todo o tráfego de entrada a usar o protocolo HTTPS.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Essa regra funciona retornando um código de status de protocolo HTTP 301 (redirecionamento permanente) quando o usuário solicita uma página usando o protocolo HTTP. O 301 redireciona a solicitação para a mesma URL solicitada pelo visitante, mas substitui a parte do protocolo HTTP da solicitação com o protocolo HTTPS. Por exemplo, HTTP://contoso.com seria redirecionado para HTTPS://contoso.com. 

## <a name="a-idhttp-hstsaenable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Habilitar HSTS (Segurança de Transporte Estrito HTTP)

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Aplicativo Web | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [Folha de dados do OWASP sobre Segurança de Transporte Estrito HTTP](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| Etapas | <p>A Segurança de Transporte Estrito HTTP (HSTS) é um aperfeiçoamento de segurança opcional, que é especificado por um aplicativo Web por meio de um cabeçalho de resposta especial. Depois que um navegador em conformidade recebe o cabeçalho, esse navegador impede que todas as comunicações sejam enviadas pelo HTTP para o domínio especificado e envia todas as comunicações pelo HTTPS. Ele também impede cliques via HTTPS em prompts de navegadores.</p><p>Para implementar a HSTS, o seguinte cabeçalho de resposta precisa ser configurado globalmente para um site, por meio de um código ou de um arquivo de configuração. A HSTS Strict-Transport-Security: max-age=300; includeSubDomains impede as seguintes ameaças:</p><ul><li>O usuário marca ou digita http://example.com e fica sujeito a um ataque MITM (man-in-the-middle): a HSTS automaticamente redireciona as solicitações de HTTP para HTTPS para o domínio de destino.</li><li>O aplicativo da Web que se destina a ser puramente HTTPS acaba contendo links HTTP ou envia pelo HTTP: a HSTS automaticamente redireciona as solicitações de HTTP para HTTPS para o domínio de destino.</li><li>Um ataque MITM tenta interceptar o tráfego de um usuário utilizando um certificado inválido e espera que o usuário aceite o certificado incorreto: a HSTS não permite que um usuário substitua a mensagem de certificado inválido.</li></ul>|

## <a name="a-idsqlserver-validationaensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Garantir a validação de certificado e a criptografia da conexão do SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Banco de dados | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | SQL Azure  |
| Atributos              | Versão do SQL - V12 |
| Referências              | [Práticas recomendadas para escrever cadeias de caracteres de conexão seguras para o banco de dados SQL](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| Etapas | <p>Todas as comunicações entre o banco de dados SQL e um aplicativo cliente são criptografadas usando Secure Sockets Layer (SSL) o tempo todo. O banco de dados SQL não oferece suporte a conexões não criptografadas. Para validar certificados com o código do aplicativo ou as ferramentas, solicite explicitamente uma conexão criptografada e não confie nos certificados do servidor. Se o código ou as ferramentas do aplicativo não solicitam uma conexão criptografada, eles continuarão receberão conexões criptografadas.</p><p>No entanto, eles não podem validar certificados de servidor e, portanto, ficarão suscetíveis a ataques de "man in the middle". Para validar certificados com o código de aplicativo ADO.NET, defina `Encrypt=True` e `TrustServerCertificate=False` na cadeia de conexão de banco de dados. Para validar certificados por meio do SQL Server Management Studio, abra a caixa de diálogo Conectar ao Servidor. Na guia Propriedades da Conexão, clique em Criptografar conexão.</p>|

## <a name="a-idencrypted-sqlserveraforce-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Forçar a comunicação criptografada para o SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Banco de dados | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | OnPrem |
| Atributos              | Versão do SQL - MsSQL2016, Versão do SQL - MsSQL2012, Versão do SQL - MsSQL2014 |
| Referências              | [Habilitar conexões criptografadas para o mecanismo de banco de dados](https://msdn.microsoft.com/library/ms191192)  |
| Etapas | Habilitar a criptografia SSL aumenta a segurança dos dados transmitidos pelas redes entre instâncias do SQL Server e aplicativos. |

## <a name="a-idcomm-storageaensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Garantir que as comunicações para o Armazenamento do Azure sejam feitas via HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Armazenamento do Azure | 
| Fase do SDL               | Implantação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [Criptografia no nível do transporte do Armazenamento do Azure – usando HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| Etapas | Para garantir a segurança dos dados do Armazenamento do Azure que estão em trânsito, use sempre o protocolo HTTPS ao chamar APIs REST ou acessar objetos no armazenamento. Além disso, as Assinaturas de Acesso Compartilhado, que podem ser usadas para delegar acesso a objetos do Armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS seja utilizado com as Assinaturas de Acesso Compartilhado, garantindo que qualquer pessoa que envie links com tokens SAS usará o protocolo adequado.|

## <a name="a-idmd5-httpsavalidate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Validar o hash MD5 depois de baixar o blob se o HTTPS não puder ser habilitado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Armazenamento do Azure | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | Tipo de armazenamento - Blob |
| Referências              | [Visão geral da MD5 do Blob do Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| Etapas | <p>O serviço Blob do Microsoft Azure fornece mecanismos para garantir a integridade dos dados no aplicativo e nas camadas de transporte. Se, por algum motivo, você precisar usar o HTTP em vez do HTTPS e estiver trabalhando com blobs de bloco, você poderá usar a verificação MD5 para ajudar a verificar a integridade dos blobs que estão sendo transferidos.</p><p>Isso ajudará na proteção contra erros na camada de rede/transporte, mas não necessariamente contra ataques de intermediários. Se você puder usar HTTPS, que fornece segurança em nível de transporte, o uso da verificação MD5 será redundante e desnecessário.</p>|

## <a name="a-idsmb-sharesause-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Usar um cliente em conformidade com SMB 3.0 para garantir a criptografia de dados em trânsito para os compartilhamentos de arquivos do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Cliente móvel | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | Tipo de armazenamento - Arquivo |
| Referências              | [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Suporte do Armazenamento de Arquivos do Azure para SMB em clientes Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| Etapas | O Armazenamento de Arquivos do Azure é compatível com HTTPS quando se usa a API REST, mas é mais frequentemente usado como um compartilhamento de arquivos SMB conectado a uma VM. O SMB 2.1 não é compatível com a criptografia, de modo que as conexões só são permitidas dentro da mesma região no Azure. No entanto, o SMB 3.0 é compatível com a criptografia e pode ser usado com o Windows Server 2012 R2, o Windows 8, o Windows 8.1 e o Windows 10, permitindo o acesso entre regiões e até mesmo o acesso à área de trabalho. |

## <a name="a-idcert-pinningaimplement-certificate-pinning"></a><a id="cert-pinning"></a>Implementar a anexação de certificado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Armazenamento do Azure | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico, Windows Phone |
| Atributos              | N/D  |
| Referências              | [Anexação de chave pública e certificado](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| Etapas | <p>A anexação de certificado protege contra ataques MITM (Man-In-The-Middle). A anexação é o processo de associar um host com sua chave pública ou seu certificado X509 esperado. Depois que um certificado ou uma chave pública forem conhecidas ou vistos para um host, o certificado ou a chave pública serão associados ou “anexados” ao host. </p><p>Assim, quando um invasor tentar promover um ataque MITM SSL, durante o handshake do SSL, a chave do servidor do invasor será diferente da chave do certificado anexado, e a solicitação será descartada, impedindo que o certificado de MITM seja anexado com a implementação do delegado `ServerCertificateValidationCallback` do ServicePointManager.</p>|

### <a name="example"></a>Exemplo
```C#
private static String PUB_KEY = "30818902818100C4A06B7B52F8D17DC1CCB47362" +
    "C64AB799AAE19E245A7559E9CEEC7D8AA4DF07CB0B21FDFD763C63A313A668FE9D764E" +
    "D913C51A676788DB62AF624F422C2F112C1316922AA5D37823CD9F43D1FC54513D14B2" +
    "9E36991F08A042C42EAAEEE5FE8E2CB10167174A359CEBF6FACC2C9CA933AD403137EE" +
    "2C3F4CBED9460129C72B0203010001";

public static void Main(string[] args)
{
  ServicePointManager.ServerCertificateValidationCallback = PinPublicKey;
  WebRequest wr = WebRequest.Create("https://encrypted.google.com/");
  wr.GetResponse();
}

public static bool PinPublicKey(object sender, X509Certificate certificate, X509Chain chain,
                                SslPolicyErrors sslPolicyErrors)
{
  if (null == certificate)
    return false;

  String pk = certificate.GetPublicKeyString();
  if (pk.Equals(PUB_KEY))
    return true;

  // Bad dog
  return false;
}
```

## <a name="a-idhttps-transportaenable-https---secure-transport-channel"></a><a id="https-transport"></a>Habilitar HTTPS - proteger o canal de transporte

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | NET Framework 3 |
| Atributos              | N/D  |
| Referências              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| Etapas | A configuração do aplicativo deve garantir que o HTTPS seja usado para todos os acessos a informações confidenciais.<ul><li>**EXPLICAÇÃO:** se um aplicativo contiver informações confidenciais e não usar a criptografia no nível da mensagem, ele só poderá se comunicar por meio de um canal de transporte criptografado.</li><li>**RECOMENDAÇÕES:** verifique se o transporte HTTP está desabilitado e habilite o transporte HTTPS em vez disso. Por exemplo, substitua `<httpTransport/>` pela marcação `<httpsTransport/>`. Não confie em uma configuração de rede (firewall) para garantir que o aplicativo seja acessado apenas por um canal seguro. Do ponto de vista filosófico, o aplicativo não deve depender da rede para garantir sua segurança.</li></ul><p>Do ponto de vista prático, as pessoas responsáveis pela segurança da rede nem sempre monitoram os requisitos de segurança do aplicativo à medida que eles evoluem.</p>|

## <a name="a-idmessage-protectionawcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: definir o nível de proteção da segurança de mensagens como EncryptAndSign

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | .NET Framework 3 |
| Atributos              | N/D  |
| Referências              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| Etapas | <ul><li>**EXPLICAÇÃO:** quando o nível de proteção estiver definido como "none", a proteção de mensagem será desabilitada. A confidencialidade e a integridade dos dados só são conquistadas com o nível apropriado de configuração.</li><li>**RECOMENDAÇÕES:**<ul><li>`Mode=None` - desabilita a proteção de mensagem</li><li>`Mode=Sign` - assina, mas não criptografa a mensagem (deve ser usada quando a integridade dos dados for importante)</li><li>`Mode=EncryptAndSign` - assina e criptografa a mensagem</li></ul></li></ul><p>Considere desativar a criptografia e apenas assinar uma mensagem quando você só precisar validar a integridade das informações sem se preocupar com sua confidencialidade. Isso pode ser útil para operações ou contratos de serviço nos quais você precisa validar o remetente original, mas que não transmitem dados confidenciais. Quando você reduzir o nível de proteção, certifique-se de que a mensagem não contenha informações de identificação pessoal (PII).</p>|

### <a name="example"></a>Exemplo
A configuração do serviço e a operação de apenas assinar a mensagem são mostradas nos exemplos aabaixo. Exemplo de contrato de serviço de `ProtectionLevel.Sign`: o exemplo abaixo mostra o uso de ProtectionLevel.Sign no nível do contrato de serviço. 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Exemplo de contrato de operação de `ProtectionLevel.Sign` (para um controle granular): veja abaixo um exemplo do uso de `ProtectionLevel.Sign` no nível do contrato de operação.

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="a-idleast-account-wcfawcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: use uma conta com privilégios mínimos para executar o serviço WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | .NET Framework 3 |
| Atributos              | N/D  |
| Referências              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| Etapas | <ul><li>**EXPLICAÇÃO:** não execute serviços do WCF usando contas de administrador ou com privilégios altos. Se os serviços forem comprometidos, isso pode ter um impacto alto.</li><li>**RECOMENDAÇÕES:** use uma conta com privilégios mínimos para hospedar o serviço WCF, porque isso reduz a superfície de ataque do aplicativo e os potenciais danos causados se houver um ataque. Se a conta do serviço exigir direitos de acesso adicionais nos recursos de infraestrutura, como MSMQ, o log de eventos, contadores de desempenho e o sistema de arquivos, as permissões apropriadas para esses recursos devem ser concedidas, para que o serviço WCF seja executado êxito.</li></ul><p>Se o serviço precisar acessar recursos específicos em nome do chamador original, use a representação e a delegação, para que a identidade do chamador passe por uma verificação de autorização de downstream. Em um cenário de desenvolvimento, use a conta de serviço da rede local, que é uma conta interna especial com privilégios limitados. Em um cenário de produção, crie uma conta de serviço de domínio personalizada com privilégios mínimos.</p>|

## <a name="a-idwebapi-httpsaforce-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Forçar todo o tráfego para APIs Web pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | API Web | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | MVC5, MVC6 |
| Atributos              | N/D  |
| Referências              | [Impondo o SSL em um controlador de API da Web](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| Etapas | Se um aplicativo tiver uma associação HTTPS e HTTP, os clientes poderão usar o HTTP para acessar o site. Para impedir isso, use um filtro de ação para garantir que as solicitações para APIs protegidas serão sempre feitas via HTTPS.|

### <a name="example"></a>Exemplo 
O código abaixo mostra um filtro de autenticação de API da Web que verifica o SSL: 
```C#
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Adicione esse filtro às ações de API da Web que exigem SSL: 
```C#
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="a-idredis-sslaensure-that-communication-to-azure-redis-cache-is-over-ssl"></a><a id="redis-ssl"></a>Garantir que as comunicações para o Cache Redis do Azure sejam feitas via SSL

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Cache Redis do Azure | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [Suporte a SSL do Redis do Azure](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| Etapas | O servidor Redis não dá suporte ao SSL pronto para uso, enquanto o Cache Redis do Azure dá. Se você estiver conectando ao Cache Redis do Azure e o seu cliente dá suporte a SSL, como StackExchange.Redis, você deve usar SSL. Por padrão, a porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure. Certifique-se de que os padrões de segurança não sejam alterados, a menos que haja uma dependência no suporte a SSL para clientes Redis. |

Observe que o Redis foi projetado para ser acessado por clientes confiáveis em ambientes confiáveis. Isso significa que normalmente não é uma boa ideia expor a instância do Redis diretamente à Internet ou, de uma forma geral, a um ambiente onde clientes não confiáveis podem acessar diretamente o soquete UNIX ou a porta TCP do Redis. 

## <a name="a-iddevice-fieldasecure-device-to-field-gateway-communication"></a><a id="device-field"></a>Proteger dispositivo para comunicações do Gateway de Campo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Gateway de Campo IoT | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | N/D  |
| Etapas | Para dispositivos baseados em IP, o protocolo de comunicação poderia ser encapsulado em um canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não oferecem suporte a SSL/TLS, verifique se há versões protegidas do protocolo que fornecem segurança na camada de transporte ou de mensagens. |

## <a name="a-iddevice-cloudasecure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Proteger o dispositivo para comunicações do Gateway de Nuvem usando SSL/TLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| Componente               | Gateway de Nuvem IoT | 
| Fase do SDL               | Compilação |  
| Tecnologias aplicáveis | Genérico |
| Atributos              | N/D  |
| Referências              | [Escolha seu protocolo de comunicação](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| Etapas | Proteja os protocolos HTTP/AMQP ou MQTT usando SSL/TLS. |

