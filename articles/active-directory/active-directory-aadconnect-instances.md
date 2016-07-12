<properties
	pageTitle="Azure AD Connect: instâncias do serviço de sincronização | Microsoft Azure"
	description="Esta página documenta considerações especiais para instâncias do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Azure AD Connect: considerações especiais para instâncias
O Azure AD Connect é mais comumente usado com a instância mundial do Azure AD e o Office 365. Mas também existem outras instâncias que têm requisitos diferentes para URLs e outras considerações especiais.

## Microsoft Cloud Alemanha
O [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por uma administradora de dados alemã.

Esta nuvem está atualmente em visualização. Muitos dos cenários que você normalmente pode fazer por conta própria, como verificar os domínios, devem ser feitos pelo operador. Contate o seu representante local da Microsoft para obter mais informações sobre como participar da visualização.

URLs para abrir no servidor proxy |
--- |
*.microsoftonline.de |
*.windows.net |
\+ Listas de revogação de certificados |

Quando você entrar em seu diretório do Azure AD, deverá usar uma conta com o domínio onmicrosoft.de.

Recursos atualmente indisponíveis no Microsoft Cloud Alemanha:

- O Azure AD Connect Health não está disponível.
- As atualizações automáticas não estão disponíveis.
- O write-back de senha não está disponível.

## Nuvem do Microsoft Azure Governamental
A [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/) é uma nuvem para o governo dos EUA.

Esta nuvem teve suporte em versões mais antigas do DirSync. A partir da build 1.1.180 do Azure AD Connect, há suporte para a próxima geração da nuvem. Essa geração está usando pontos de extremidade com base somente nos EUA e tem uma lista de URLs diferente para abrir em seu servidor proxy.

URLs para abrir no servidor proxy |
--- |
*.microsoftonline.com |
*.gov.us.microsoftonline.com |
\+ Listas de revogação de certificados |

O Azure AD Connect não será capaz de detectar automaticamente que o diretório do Azure AD está localizado na nuvem Governamental. Em vez disso, você precisa executar as seguintes ações ao instalar o Azure AD Connect.

1. Inicie a instalação do Azure AD Connect.
2. Assim que você consultar a primeira página em que é solicitado a aceitar o EULA, não continue, mas deixe o assistente de instalação em execução.
3. Inicie o regedit e altere a chave do registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` para o valor `2`.
4. Vá para o assistente de instalação do Azure AD Connect, aceite o EULA e continue. Durante a instalação, certifique-se de usar o caminho de instalação **configuração personalizada** (e não a instalação Expressa). Em seguida, continue a instalação como de costume.

Recursos atualmente indisponíveis na nuvem do Microsoft Azure Governamental:

- O Azure AD Connect Health não está disponível.
- As atualizações automáticas não estão disponíveis.
- O write-back de senha não está disponível.

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->