<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Serviços do BizTalk: Nome e chave do emissor

Os Serviços BizTalk do Azure usam o nome e a chave do emissor do Barramento de Serviço e o nome e a chave do emissor do Controle de Acesso. Especificamente:

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Tarefa</strong></td>
<td><strong>Qual nome e chave do emissor</strong></td>
</tr>
<tr>
<td>Implantando seu aplicativo do Visual Studio</td>
<td>Nome e chave do emissor do Controle de Acesso</td>
</tr>
<tr>
<td>Configurando o Portal dos Servi&ccedil;os BizTalk do Azure</td>
<td>Nome e chave do emissor do Controle de Acesso</td>
</tr>
<tr>
<td>Criando retransmiss&otilde;es de LOB com os Servi&ccedil;os do Adaptador do BizTalk no Visual Studio</td>
<td>Nome e chave do emissor do Service Bus</td>
</tr>
</table>

Este tópico lista as etapas para recuperar o nome e a chave do emissor.

## Nome e chave do emissor do Controle de Acesso

O nome e a chave do emissor do Controle de Acesso são usados pelo seguinte:

-   Seu aplicativo do Serviço do BizTalk do Azure criado no Visual Studio: Para implantar com êxito seu aplicativo do Serviço do BizTalk no Visual Studio para o Azure, você insere o nome e a chave do emissor do Controle de Acesso.
-   O portal dos Serviços BizTalk do Azure: A primeira vez que você faz logon no portal dos Serviços do BizTalk, você insere seu nome do Serviço do BizTalk como o provedor de serviços, o nome e a chave do emissor do Controle de Acesso.

### Para recuperar o nome e a chave do emissor do Controle de acesso

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  No painel de navegação à esquerda, selecione **Serviços do BizTalk**.
3.  Selecione o Serviço do BizTalk.
4.  Selecione **Informação de Conexão** na barra de tarefas. O Namespace do Controle de Acesso, o Emissor Padrão (Nome do Emissor) e a Chave Padrão (Chave do Emissor) são listados e podem ser copiados e colados.
    Para resumir:
    Nome do emissor = Emissor Padrão
    Chave do emissor = Chave Padrão

Você também pode clicar em **Abrir Portal de Gerenciamento ACS** para recuperar os valores de Controle de Acesso:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  No painel de navegação à esquerda, selecione **Serviços do BizTalk**.
3.  Selecione o Serviço do BizTalk.
4.  Selecione o botão Informações de Conexão e selecione **Abrir Portal de Gerenciamento ACS**.
5.  No Portal, em **Configurações de Serviço**, clique em **Identidades de Serviço**. Isso exibe sua Identidade de Serviço, que é o valor do Nome do Emissor do Controle de Acesso. Clique no link da identidade do serviço para consultar a senha, que é o valor da chave do emissor. Seus valores podem ser copiados.
    Por exemplo, em **Identidades de Serviço**, você verá "proprietário". O "proprietário" é o nome do emissor de Controle de Acesso. Quando você clica no link "proprietário", você verá a **Senha**. Quando você clica no link "Senha", você verá o valor. O valor dessa Senha é sua Chave do Emissor do Controle de Acesso.
    Para resumir:
    Nome do emissor = Nome da identidade do serviço
     Chave do emissor = Valor da senha

No painel de navegação à esquerda, você também pode selecionar **Active Directory** para recuperar os valores do Controle de Acesso.

<div class="dev-callout"> 
<b>Importante</b> 
<p>Quando o namespace do Controle de Acesso &eacute; criado usando o <strong>Active Directory</strong>, uma Identidade de Servi&ccedil;o <strong>n&atilde;o</strong> &eacute; criada automaticamente. Quando voc&ecirc; provisiona um Servi&ccedil;o do BizTalk, um Namespace de Controle de Acesso, uma Identidade de Servi&ccedil;o chamada &quot;propriet&aacute;rio&quot; (Nome do Emissor), uma Senha (Chave do Emissor) e uma Chave Sim&eacute;trica s&atilde;o criados automaticamente.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">Como: Use o Servi&ccedil;o de Gerenciamento do ACS para configurar as Identidades de Servi&ccedil;o</a> fornece mais informa&ccedil;&otilde;es sobre identidades do servi&ccedil;o de ACS.</p>
</div>

## Nome e chave do emissor do Service Bus

O nome e a chave do emissor do Barramento de Serviço são usados pelos Serviços do Adaptador do BizTalk. No seu projeto de Serviços do BizTalk no Visual Studio, você usa os Serviços do Adaptador do BizTalk para se conectar a um sistema de linha de negócios (LOB) no local. Para se conectar, você cria a retransmissão de LOB e insere os detalhes do seu sistema de LOB. Ao fazer isso, você também insere o nome e a chave do emissor do Barramento de Serviço.

### Para recuperar o nome e a chave do emissor do Service Bus

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  No painel de navegação esquerdo, clique em **Service Bus**.
3.  Clique em seu namespace. Na barra de tarefas, clique em **Informações de Conexão**. Isso exibe o **Emissor padrão** (Nome do Emissor) e a **Chave Padrão** (Chave do Emissor). Seus valores podem ser copiados.
    Para resumir:
    Nome do emissor = Emissor padrão
    Chave do emissor = Chave padrão

## Avançar

Tópicos adicionais sobre Serviços BizTalk do Azure:

-   [Instalando o SDK dos Serviços BizTalk do Azure][Instalando o SDK dos Serviços BizTalk do Azure]
-   [Tutoriais: Serviços do BizTalk do Azure][Tutoriais: Serviços do BizTalk do Azure]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]
-   [Serviços BizTalk do Azure][Serviços BizTalk do Azure]

## Consulte também

-   [Como: Use o Serviço de Gerenciamento do ACS para configurar as Identidades de Serviço][Como: Use o Serviço de Gerenciamento do ACS para configurar as Identidades de Serviço]
-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]
-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: Gráfico de status do provisionamento][Serviços do BizTalk: Gráfico de status do provisionamento]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]
-   [Serviços do BizTalk: Backup e restauração][Serviços do BizTalk: Backup e restauração]
-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]

  [Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Como: Use o Serviço de Gerenciamento do ACS para configurar as Identidades de Serviço]: http://go.microsoft.com/fwlink/p/?LinkID=303942
  [Instalando o SDK dos Serviços BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [Tutoriais: Serviços do BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Serviços BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: Gráfico de status do provisionamento]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Serviços do BizTalk: Backup e restauração]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
