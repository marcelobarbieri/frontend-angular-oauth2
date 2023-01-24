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

# Configurar a aplicação

1. Na pasta *src/app*, edite o *app.module.ts* e adicione **MsalModule** e **MsalInterceptor** a **imports**, bem como a constante **isIE**. Seu código deve ficar assim:

|Nome do valor||Sobre o|
|---|---|---|
|clientId||Na página **Visão Geral** do seu registro de aplicativo, esta é seu valor **ID do Aplicativo (cliente)** .|
|authority¹|Instância da nuvem|Essa é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, insira https://login.microsoftonline.com. Para nuvens nacionais (por exemplo, China), confira [Nuvens nacionais](https://learn.microsoft.com/pt-br/azure/active-directory/develop/authentication-national-cloud).|
|authority¹|Tenant Info|Defina como uma das seguintes opções: Se o aplicativo der suporte a contas neste diretório organizacional, substitua esse valor pela ID do diretório (locatário) ou pelo nome do locatário (por exemplo, **contoso.microsoft.com**). Se o aplicativo for compatível com as contas em qualquer diretório organizacional, substitua esse valor por **organizações**. Se o seu aplicativo for compatível com as contas em qualquer diretório organizacional e contas pessoais da Microsoft, substitua esse valor por **comum**. Para restringir o suporte a contas pessoais da Microsoft, substitua esse valor por **consumidores**.|
|redirectUri||Substitua por http://localhost:4200 .|

> Para saber mais sobre opções configuráveis disponíveis, confira [Inicializar aplicativos cliente](https://learn.microsoft.com/pt-br/azure/active-directory/develop/msal-js-initializing-client-applications).

2. Adicione rotas aos componentes inicial e de perfil em *src/app/app-routing.module.ts*. 

# Substituir a interface do usuário base

1. Substitua o código de espaço reservado em *src/app/app.component.html*

2. Adicione módulos materiais a *src/app/app.module.ts*. O **AppModule**

3. (OPCIONAL) Adicione o CSS a src/style.css

4. (OPCIONAL) Adicione o CSS a src/app/app.component.css

# Conectar um usuário

Adicione o código das seguintes seções para invocar o logon usando uma janela pop-up ou um redirecionamento de quadro completo:

## Entrar usando pop-ups

1. Altere o código em *src/app/app.component.ts* para conectar um usuário usando uma janela pop-up.

## Conectar-se usando redirecionamentos

1. Atualize *src/app/app.module.ts* para inicializar o **MsalRedirectComponent**. Este é um componente de redirecionamento dedicado que tratará os redirecionamentos. 

2. Adicione o seletor **\<app-redirect\>** a *src/index.html*. Esse seletor é usado pelo **MsalRedirectComponent**. 

3. Substitua o código em *src/app/app.component.ts* para conectar um usuário usando um redirecionamento de quadro completo.

4. Substitua o código existente em *src/app/home/home.component.ts* para assinar o evento **LOGIN_SUCCESS**. Isso permitirá que você acesse o resultado do logon bem-sucedido com o redirecionamento. 

# Renderização Condicional

Para renderizar uma interface do usuário somente para usuários autenticados, os componentes precisam assinar o **MsalBroadcastService** para ver se os usuários se conectaram e se a interação foi concluída.

1. Adicione o **MsalBroadcastService** a *src/app/app.component.ts* e assine o **inProgress$** observável para verificar se a interação foi concluída e se uma conta está conectada antes de renderizar a interface do usuário. 

2. Atualize o código em *src/app/home/home.component.ts* para também verificar se a interação deve ser concluída antes da atualização da interface do usuário. 

3. Substitua o código em *src/app/home/home.component.html*

# Como proteger as rotas

A MSAL Angular fornece o **MsalGuard**, uma classe que você pode usar para proteger rotas e exigir autenticação antes de acessar a rota protegida. As etapas abaixo adicionam o **MsalGuard** à rota **Profile**. A proteção da rota **Profile** significa que, mesmo que um usuário não se conecte usando o botão **Login**, se ele tentar acessar a rota **Profile** ou clicar no botão **Profile**, o **MsalGuard** solicitará que o usuário se autentique por meio do item pop-up ou do redirecionamento antes de mostrar a página **Profile**.

**MsalGuard** é uma classe de conveniência que você pode usar para aprimorar a experiência do usuário, mas não deve ser usada para segurança. Os invasores podem contornar a proteção do lado do cliente, e você deve garantir que o servidor não retorne dados que o usuário não deve acessar.

1. Adicione a classe **MsalGuard** como um provedor no seu aplicativo em *src/app/app.module.ts* e adicione as configurações para o **MsalGuard**. Os escopos necessários para adquirir tokens posteriormente podem ser fornecidos no **authRequest**, e o tipo de interação para o Guard pode ser definido como ou **Redirect** ou **Popup**.

2. Defina o MsalGuard nas rotas que deseja proteger em *src/app/app-routing.module.ts*

3. Ajuste as chamadas de logon em *src/app/app.component.ts* para levar em conta o **authRequest** definido nas configurações de proteção. 

# Adquirir um token

## Interceptor Angular

A MSAL Angular fornece uma classe **Interceptor** que adquire automaticamente tokens para solicitações de saída que usam o cliente **http** do Angular para recursos protegidos conhecidos.

1. Adicione a classe **Interceptor** como um provedor ao seu aplicativo em *src/app/app.module.ts*, com as respectivas configurações. 

Os recursos protegidos são fornecidos como um **protectedResourceMap**. As URLs fornecidas na coleção **protectedResourceMap** diferenciam maiúsculas de minúsculas. Para cada recurso, adicione os escopos solicitados para retorno no token de acesso.

Por exemplo:
- **["user.read"]** para Microsoft Graph
- **["\<Application ID URL\>/scope"]** para APIs Web personalizadas (ou seja, **api://\<Application ID\>/access_as_user**)

Modifique os valores no **protectedResourceMap**, conforme descrito aqui:

|Nome do valor|Sobre o|
|---|---|
|**Enter_the_Graph_Endpoint_Here**|A instância da API do Microsoft Graph com a qual o aplicativo deve se comunicar. Para o ponto de extremidade **global** da API do Microsoft Graph, substitua as duas instâncias dessa cadeia de caracteres por https://graph.microsoft.com. Para pontos de extremidade em implantações de nuvens **nacionais**, confira [Implantações de nuvens nacionais](https://learn.microsoft.com/pt-br/graph/deployments) na documentação do Microsoft Graph.|

2. Substitua o código em *src/app/profile/profile.component.ts* para recuperar o perfil de um usuário com uma solicitação HTTP.

3. Substitua a interface do usuário em *src/app/profile/profile.component.html* para exibir as informações de perfil.

# Sair

Atualize o código em *src/app/app.component.html* para exibir condicionalmente um botão Logout.

## Sair do serviço usando redirecionamentos

Atualize o código em *src/app/app.component.ts* para desconectar um usuário usando redirecionamentos.