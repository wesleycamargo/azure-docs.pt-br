<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Distribuições endossadas" pageTitle="Distribuições endossadas do Linux no Azure" metaKeywords="" description="Saiba mais sobre o Linux nas distribuições endossadas do Azure, incluindo diretrizes para Ubuntu, OpenLogic e SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux em distribuições endossadas do Azure" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





#Linux em distribuições endossadas do Azure

As imagens de distribuição na Galeria são fornecidas pelos parceiros a seguir e estamos trabalhando com a comunidade para trazer ainda mais distribuições endossadas. Enquanto isso, você sempre poderá trazer seu próprio Linux seguindo as diretrizes desta página.

## Canônico ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

O controle aberto da comunidade e a engenharia da Canonical impulsionam o sucesso do Ubuntu no cliente, no servidor e na computação em nuvem, incluindo serviços de nuvem pessoais para os consumidores. A visão da Canonical de uma plataforma livre unificada no Ubuntu, do telefone à nuvem, com uma família de interfaces coerentes para telefone, tablet, TV e área de trabalho, torna o Ubuntu a primeira opção para diversas instituições que vão desde provedores de nuvem pública até fabricantes de aparelhos eletrônicos e um favorito entre tecnólogos individuais.

Com desenvolvedores e centros de engenharia no mundo inteiro, a Canonical está posicionada exclusivamente para fazer parceria com fabricantes de hardware, provedores de conteúdo e desenvolvedores de software para oferecer soluções de Ubuntu no mercado, de PCs a servidores e dispositivos portáteis.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

O OpenLogic é líder no fornecimento de soluções empresariais de software livre para a nuvem e o data center. Ele ajuda centenas de empresas líderes de inúmeros setores a adquirir, dar suporte e controlar com segurança o software livre. O OpenLogic oferece suporte técnico de nível comercial e indenização para 600 pacotes de software livre com suporte da OpenLogic Expert Community (Comunidade de Especialistas do OpenLogic), incluindo suporte de nível empresarial para CentOS e também sendo o parceiro de lançamento para fornecer imagens do CentOS no Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle consiste em oferecer um amplo portfólio de soluções para nuvens públicas e privadas, dando aos clientes opção e flexibilidade no modo de implantação d um software Oracle em nuvens Oracle, bem como em outras nuvens.  A parceria da Oracle com a Microsoft permite aos clientes implantar um software Oracle em nuvens públicas e privadas da Microsoft com a certeza da certificação e do suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada da Oracle não são alterados.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server no Azure é uma plataforma comprovada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma versátil Linux do SUSE integra-se perfeitamente aos serviços de nuvem do Azure para oferecer um ambiente de nuvem facilmente gerenciável. E, com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho em execução com suporte no data center possam ser implantadas com segurança no Azure.

## Versões com suporte ##

A tabela a seguir mostra as versões de distribuição diferentes, os drivers LIS (Serviços de Integração do Linux) e as versões do Linux Agent para Azure que foram testadas para funcionar no Azure. Os drivers LIS estão disponíveis em [http://www.microsoft.com/pt-br/download/details.aspx?id=34603](http://www.microsoft.com/pt-br/download/details.aspx?id=34603). As versões do Linux Agent estão disponíveis em [https://github.com/windowsazure/walinuxagent](https://github.com/windowsazure/walinuxagent).

A tabela também inclui um link para o patch de compatibilidade do kernel necessário para que algumas versões de distribuição possam funcionar de forma otimizada no Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribuição</th>		
	    <th>Versão</th>
	    <th>Drivers</th>
		<th>Patch de compatibilidade do kernel</th>
		<th>Agente</th>
			</tr>
	<tr>
		<th>  Canonical UBUNTU </th>
		<td> Ubuntu 12.04.1, 12.10 e 13.04</td>
		<td>No kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Necessário apenas para 12.04 ou 12.04.01</a></td>
		<td>Pacote: no repo do pacote em walinuxagent <br />
			Fonte: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
			</tr>
	<tr>
		<th> CENTOS pela Open Logic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/p/?LinkID=254263">drivers LIS</a>; drivers CentOS 6.4+: no kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Necessário apenas para 6.3</a></td>
		<td>Pacote: no <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">repo do pacote da Open Logic </a> em walinuxagent<br />
			Fonte: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
 		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>No kernel</td>
		<td>N/D</td>
		<td>Pacote: no repo, nome: WALinuxAgent
<br />
			Fonte: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr><tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>No kernel</td>
		<td>N/D</td>
		<td>Pacote: na <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >nuvem:Ferramentas</A> repo, nome: WALinuxAgent<br />
			Software livre: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> OpenSUSE 13.1+</td>
		<td>No kernel</td>
		<td>N/D</td>
		<td>Pacote: na <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >nuvem:Ferramentas</A> repo, nome: WALinuxAgent<br />
			Software livre: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
</table>

