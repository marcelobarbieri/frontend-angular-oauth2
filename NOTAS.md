# Tutorial: Conectar usuários e chamar a API do Microsoft Graph em um SPA Angular usando o fluxo de código de autenticação

> Referência: https://learn.microsoft.com/pt-br/azure/active-directory/develop/tutorial-v2-angular-auth-code


- SPA (aplicativo de página única)
- API do Microsoft Graph
- Fluxo de código de autorização com PKCE
- MSAL (Biblioteca de Autenticação da Microsoft)

MSAL Angular v2: 
- suporte ao fluxo do código de autorização no navegador
- não dá suporte ao fluxo implícito

## Diagrama

![Diagrama](./assets/diagram-01-auth-code-flow.png)

O aplicativo de exemplo criado neste tutorial permite que um SPA Angular consulte a API do Microsoft Graph ou uma API Web que aceita tokens emitidos pela plataforma de identidade da Microsoft. Ele usa a MSAL (Biblioteca de Autenticação da Microsoft) para o Angular v2, um wrapper da biblioteca MSAL.js v2. A MSAL Angular permite que aplicativos Angular 9 e posterior autentiquem usuários corporativos usando o Azure AD (Azure Active Directory), bem como usuários com as contas Microsoft e usuários com identidades sociais, como o Facebook, o Google e o LinkedIn. A biblioteca também permite que os aplicativos tenham acesso aos serviços em nuvem da Microsoft e ao Microsoft Graph.

Depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL.ls

## Bibliotecas

|Biblioteca|Descrição|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|Biblioteca de Autenticação da Microsoft para Wrapper Angular JavaScript|
|[MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Biblioteca de Autenticação da Microsoft para o pacote de navegador do javaScript v2.0|

Código fonte de todas as bibliotecas MSAL.js no repositório no GitHub [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)

# Criação do projeto

```ps
# instalação do Angular CLI
npm install -g @angular/cli
# geração de uma nova aplicação Angular                         
ng new msal-angular-tutorial --routing=true --style=css --strict=false    
# navega para o diretório da aplicação
cd msal-angular-tutorial                            
# instala a biblioteca de componentes Angular Material (opcional, para UI)
npm install @angular/material @angular/cdk          
# instala as biblioteca MSAL Browser e MSAL Angular na sua aplicação
npm install @azure/msal-browser @azure/msal-angular 
# adiciona uma página 'home'
ng generate component home                          
# adiciona uma página 'profile'
ng generate component profile                       
```

# Registrar a aplicação

- [Instruções para registrar um aplicativo de página única](https://learn.microsoft.com/pt-br/azure/active-directory/develop/scenario-spa-app-registration) no portal do Azure
- Na página Visão geral do aplicativo do seu registro, anote o valor da ID do aplicativo (cliente) para uso posterior
- Registre o valor do URI de Redirecionamento como http://localhost:4200/ e digite-o como 'SPA'