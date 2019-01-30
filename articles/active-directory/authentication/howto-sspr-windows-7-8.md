---
title: Autoatendimento de redefinição de senha do Azure AD no Windows 7 e 8.1
description: Como habilitar o autoatendimento de redefinição de senha usando o "Esqueci a senha" na tela de logon do Windows 7 ou 8.1
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 1b488e938cf314eef18117ffd33f582319260162
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438772"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Como: habilitar a redefinição de senha do Windows 7, 8 e 8.1

Como administrador, você habilitou o SSPR (autoatendimento de redefinição de senha), mas os usuários continuam ligando para a assistência técnica para redefinir a senha, porque não conseguem encontrar a janela do navegador para acessar o [portal de SSPR](https://aka.ms/sspr). Nos computadores com o Windows 10, é possível habilitar o link "Redefinir senha" na tela de logon usando o tutorial [Redefinição de senha do Azure AD na tela de logon](tutorial-sspr-windows.md). Com essas diretrizes, será possível habilitar os usuários do Windows 7, 8 e 8.1 a redefinir a senha usando o SSPR na tela de logon do Windows.

Ao contrário dos computadores com Windows 10, os com Windows 7, 8 e 8.1 não têm um requisito associado ao domínio do Azure AD ou do Active Directory para redefinição de senha.

![Exemplo de tela de logon do Windows 7 com "Esqueceu a senha?" link mostrado](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Pré-requisitos

* A redefinição de senha de autoatendimento do Azure AD deve ser habilitada.
* Sistema operacional Windows 7 ou Windows 8.1 corrigido.
* TLS 1.2 habilitado usando a diretriz encontrada em [Configurações de registro do protocolo TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> O TLS 1.2 precisa ser habilitado, e não apenas configurado para negociar automaticamente.

## <a name="install"></a>Instalar

1. Baixe o instalador correto para a versão do Windows que você quer habilitar.

   1. O software está disponível no Centro de Download da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Entre no computador em que você quer instalar e execute o instalador.
1. Após a instalação, uma reinicialização é altamente recomendável.
1. Após a reinicialização, na tela de logon, escolha um usuário e clique em "Esqueceu a senha?" para iniciar o fluxo de trabalho de redefinição de senha.
1. Conclua o fluxo de trabalho seguindo as etapas na tela para redefinir sua senha.

![Exemplo de clique em "Esqueceu a senha?" no Windows 7 Fluxo de autoatendimento de redefinição de senha](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Instalação silenciosa

* Para uma instalação silenciosa, use o comando “msiexec /i SsprWindowsLogon.PROD.msi /qn”
* Para uma desinstalação silenciosa, use o comando “msiexec /x SsprWindowsLogon.PROD.msi /qn”

## <a name="caveats"></a>Limitações

É necessário se registrar no SSPR antes de usar o link "Esqueci a senha".

![Para redefinir a sua senha, são necessárias informações de segurança adicionais](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Usar o aplicativo do Microsoft Authenticator para notificações e códigos para redefinir senhas não funciona nesta versão inicial. Os usuários precisam ter métodos alternativos registrados que cumpram os requisitos da sua política.

## <a name="troubleshooting"></a>solução de problemas

Os eventos serão registrados no computador e no Azure AD.

Os eventos do Azure AD incluirão informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha na tela de logon do Windows 7 no log de auditoria do Azure AD](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário outro registro em log, uma chave do Registro no computador poderá ser alterada para habilitar o registro em log detalhado. Habilite o registro detalhado somente para fins de solução de problemas.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Para habilitar o log detalhado, crie REG_DWORD: "EnableLogging" e defina como 1.
* Para desabilitar o registro em log detalhado, altere o REG_DWORD: “EnableLogging” para 0.

Se os seus computadores com Windows 7, 8 e 8.1 estiverem atrás de um servidor proxy ou firewall, o tráfego HTTPS (443) para passwordreset.microsoftonline.com deverá ter permissão.

## <a name="next-steps"></a>Próximas etapas

[Habilitar os usuários do Windows 10 a redefinir sua senha na tela de logon](tutorial-sspr-windows.md)