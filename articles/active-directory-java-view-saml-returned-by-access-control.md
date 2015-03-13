<properties 
	pageTitle="Exibir o SAML retornado pelo Serviço de Controle de Acesso (Java)" 
	description="Saiba como exibir o SAML retornado pelo serviço de controle de acesso em aplicativos Java hospedados no Azure." 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Como exibir o SAML retornado pelo Access Control Service do Azure

Este guia mostrará como exibir o SAML adjacente retornado a seu aplicativo pelo ACS (Serviço de Controle de Acesso) do Azure. O guia se baseia no tópico [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse][], fornecendo um código que exibe as informações de SAML. O aplicativo concluído será semelhante ao seguinte.

![Example SAML output][saml_output]

Para obter mais informações sobre o ACS, consulte a seção [Próximas etapas](#next_steps).

> [WACOM.NOTE]
> O Filtro de Controle dos Serviços de Acesso do Azure (da Microsoft Open Technologies) é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, ele não oferece suporte formal da Microsoft Open Technologies, Inc. nem da Microsoft.

## Sumário

-   [Pré-requisitos][]
-   [Adicionar a biblioteca JspWriter a seu caminho de compilação e assembly de implantação][]
-   [Modificar o arquivo JSP para exibir o XAML][]
-   [Executar o aplicativo][]
-   [Próximas etapas][]


## <a name="pre"></a>Pré-requisitos

Para executar as tarefas deste guia, conclua o exemplo em [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse][] e use-o como o ponto de partida deste tutorial.

## <a name="add_library"></a>Adicionar a biblioteca JspWriter a seu caminho de compilação e assembly de implantação

Adicione a biblioteca que contém a classe **javax.servlet.jsp.JspWriter** ao caminho de compilação e ao assembly de implantação. Se você estiver usando o Tomcat, a biblioteca será **jsp-api.jar**, localizada na pasta **lib** do Apache.

1. No Explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Caminho de Compilação**, clique em **Configurar Caminho de Compilação**, clique na guia **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**.
2. Na caixa de diálogo **Seleção de JAR**, navegue até o JAR necessário, selecione-o e clique em **Abrir**.
3. Com a caixa de diálogo **Propriedades de MyACSHelloWorld** ainda aberta, clique em **Assembly de Implantação**.
4. Na caixa de diálogo **Assembly de Implantação da Web**, clique em **Adicionar**.
5. Na caixa de diálogo **Nova Diretiva de Assembly**, clique em **Entradas do Caminho de Compilação Java** e em **Avançar**.
6. Selecione a biblioteca apropriada e clique em **Concluir**.
7. Clique em **OK** para fechar a caixa de diálogo **Propriedades de MyACSHelloWorld**.

## <a name="modify_jsp"></a>Modificar o arquivo JSP para exibir o XAML

Modifique o **index.jsp** para usar o código a seguir.

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	    <%@ page import="javax.xml.parsers.*"
	             import="javax.xml.transform.*"
	             import="org.w3c.dom.*"
	             import="java.io.*"
	             import="javax.xml.transform.stream.*"
	             import="javax.xml.transform.dom.*"
	             import="javax.xml.xpath.*"
	             import="javax.servlet.jsp.JspWriter" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Sample ACS Filter</title>
	</head>
	<body>
		<h3>SAML information from sample ACS program</h3>
		<%!
	    void displaySAMLInfo(Node node, String parent, JspWriter out)
	    {
	    
		    try
		    {
				String nodeName;
			    int nChild, i;
			    
			    nodeName = node.getNodeName();
			    out.println("<br>");
			    out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
			       
			       // Attributes.
			       NamedNodeMap attribsMap = node.getAttributes();
			       if (null != attribsMap)
			       {
	                     for (i=0; i < attribsMap.getLength(); i++)
	                     {
	                            Node attrib = attribsMap.item(i);
	                            out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
	                     }
			       }
			       
			       // Child nodes.
			       NodeList list = node.getChildNodes();
			       if (null != list)
	 		       {
			              nChild = list.getLength();
			              if (nChild > 0)
			              {                    
	
				                 // If it is a text node, just print the text.
				                 if (list.item(0).getNodeName() == "#text")
				                 {
	                                 out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
				                 }
				                 else
				                 {
				                	 // Print out the child node names.
				                	 out.print("Contains " + nChild + " child node(s): ");   
		   		                     for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    
					                    out.print("<b>" + temp.getNodeName() + "</b>");
					                    if (i < nChild - 1)
					                    {
					                    	// Separate the names.
					                    	out.print(", ");
					                    }
					                    else
					                    {
					                    	// Finish the sentence.
					                    	out.print(".");
					                    }
					                    	
				                     }
					                 out.println("<br>");
					                 
					                 // Process the child nodes.
					                 for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    displaySAMLInfo(temp, parent + nodeName + "\\", out);
				                     }
				               }
			              }
			          }
			      }
			    catch (Exception e)
			    {
			    	System.out.println("Exception encountered.");
			    	e.printStackTrace();	    	
			    }
		    }
	    %>
	
	    <%
	    try 
	    {
		    String data  = (String) request.getAttribute("ACSSAML");
		    
		    DocumentBuilder docBuilder;
			Document doc = null;
			DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
			docBuilderFactory.setIgnoringElementContentWhitespace(true);
			docBuilder = docBuilderFactory.newDocumentBuilder();
			byte[] xmlDATA = data.getBytes();
			
			ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
			doc = docBuilder.parse(in);
			doc.getDocumentElement().normalize();
			
			// Iterate the child nodes of the doc.
	        NodeList list = doc.getChildNodes();
	
	        for (int i=0; i < list.getLength(); i++)
	        {
	        	displaySAMLInfo(list.item(i), "", out);
	        }
		}
	    catch (Exception e) 
	    {
	    	out.println("Exception encountered.");
	    	e.printStackTrace();
		}
	    
	    %>
	</body>
	</html>

## <a name="run_application"></a>Executar o aplicativo

1. Execute o aplicativo no emulador do computador ou implante no Azure, usando as etapas documentadas em [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse][].
2. Inicie um navegador e abra o aplicativo web. Depois de fazer logon em seu aplicativo, você verá informações de SAML, incluindo a declaração de segurança fornecida pelo provedor de identidade.

## <a name="next_steps"></a>Próximas etapas

Para explorar melhor a funcionalidade do ACS e experimentar mais cenários sofisticados, consulte [Serviço de Controle de Acesso 2.0][].

[Pré-requisitos]: #pre
[Modificar o arquivo JSP para exibir o XAML]: #modify_jsp
[Adicionar a biblioteca JspWriter a seu caminho de compilação e assembly de implantação]: #add_library
[Executar o aplicativo]: #run_application
[Próximas etapas]: #next_steps
[Serviço de Controle de Acesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png

<!--HONumber=46--> 
