### <a name="troubleshoot-azure-diagnostics"></a>Solucionar problemas de diagnóstico do Azure

Se você receber a seguinte mensagem de erro, o provedor de recursos Microsoft.insights não está registrado:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Para registrar o provedor de recursos, execute as seguintes etapas no portal do Azure:

1.  No painel de navegação à esquerda, clique em *Assinaturas*
2.  Selecione a assinatura identificada na mensagem de erro
3.  Clique em *Provedores de Recursos*
4.  Localize o provedor *Microsoft.insights*
5.  Clique o *registrar* link

![Registrar o provedor de recursos microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Uma vez o *Microsoft.insights* provedor de recursos é registrado, configurando o diagnóstico de repetição.


<!--HONumber=Feb17_HO2-->


