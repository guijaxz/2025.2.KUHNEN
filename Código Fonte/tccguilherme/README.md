# Encontre Seu Pet: Guia de Configuração #
Este documento descreve os passos necessários para configurar e rodar o projeto "Encontre Seu Pet", incluindo a configuração do aplicativo Flutter (frontend) e do servidor Node.js (backend).

O projeto utiliza diversos serviços externos que exigem chaves de API e arquivos de configuração que não estão incluídos no repositório (conforme listado no .gitignore). Siga este guia para configurar seu ambiente.

# 1 Arquitetura de Serviços

Para rodar o projeto, você precisará de contas nos seguintes serviços:

Firebase: Utilizado para Autenticação, Banco de Dados (Firestore), Storage de arquivos e Mensagens (FCM).

Google Cloud Platform: Utilizado para o Google Maps (Maps SDK).

Facebook for Developers: Utilizado para o login social com Facebook.

AWS (Amazon Web Services): Utilizado para o serviço de reconhecimento de imagem (Rekognition) e S3.

SendGrid: Utilizado para o envio de e-mails transacionais.

# Pré-requisitos de Software
Antes de começar, garanta que você tenha os seguintes softwares instalados e configurados no seu computador:

SDK do Flutter: Essencial para rodar o aplicativo frontend. A instalação do Flutter já inclui o Dart.

[Guia de instalação do Flutter](https://docs.flutter.dev/get-started)

Node.js: Necessário para rodar o servidor backend (API).

[Guia de instalação do Node.js](https://nodejs.org/en)

Um editor de código: Recomendo o [VS Code](https://code.visualstudio.com) com as extensões do Flutter e Dart.

Git: Para clonar o repositório.

# 2 Configuração do Ambiente

# Parte 1: Configuração do Backend (Node.js)
O backend é responsável pela lógica de negócios, autenticação de serviço e comunicação com as APIs da AWS e SendGrid.

Navegue até a pasta do backend:

Digite no terminal:

cd "Código Fonte/tccguilherme/backend"

Instale as dependências do Node.js digitando no terminal:

npm install

Crie o arquivo de credenciais do Firebase:

Vá até o seu console do Firebase.

No seu projeto, acesse "Configurações do Projeto" > "Contas de Serviço".

Clique em "Gerar nova chave privada" e baixe o arquivo JSON.

Renomeie este arquivo para firebase-credentials.json e coloque-o dentro da pasta backend/.

Crie o arquivo de variáveis de ambiente (.env):

Na pasta backend/, crie um arquivo chamado .env.

Este arquivo armazenará todas as chaves de API do lado do servidor.

Preencha o arquivo backend/.env com a seguinte estrutura:

# Firebase
# Encontre isso no seu console do Firebase > Storage
FIREBASE_STORAGE_BUCKET="seu-bucket-name.firebasestorage.app"

# AWS (Amazon Web Services)
# Crie um usuário IAM com acesso programático e permissões para Rekognition e S3
AWS_REGION="sua-regiao-aws"  ex: us-east-1
AWS_ACCESS_KEY_ID="sua-chave-de-acesso-id"
AWS_SECRET_ACCESS_KEY="sua-chave-de-acesso-secreta"

# SendGrid
# Crie uma API Key no painel do SendGrid
SENDGRID_API_KEY="sua-chave-api-do-sendgrid"
SENDER_EMAIL="seu-email-de-envio"


# Parte 2: Configuração do Frontend (Flutter)

Navegue até a pasta raiz do projeto Flutter e digite no terminal:

cd "Código Fonte/tccguilherme"

Instale as dependências do Flutter digitando no terminal:

flutter pub get

Configure o Firebase para o Flutter (FlutterFire):

Instale o Firebase CLI (siga o guia oficial: https://firebase.google.com/docs/cli).

Faça login com firebase login.

No diretório raiz do Flutter (tccguilherme), execute o comando no terminal:

flutterfire configure

Selecione o projeto Firebase que você criou. Siga os passos para configurar as plataformas (iOS e Android).

Este comando irá gerar automaticamente os seguintes arquivos que estão no .gitignore:

lib/firebase_options.dart

android/app/google-services.json

ios/Runner/GoogleService-Info.plist

ios/firebase_app_id_file.json

# Parte 3: Configuração de APIs de Plataforma (Android e iOS)
Você precisa adicionar as chaves de API do Google Maps e as configurações do Facebook Login diretamente nos arquivos de configuração nativos do Android e iOS.

# Parte 3.1: Configuração do Android
Google Maps API Key:

No Google Cloud Console (pode ser o mesmo projeto do Firebase), ative a API "Maps SDK for Android".

Crie uma API Key.

Abra o arquivo android/app/src/main/AndroidManifest.xml.

Adicione sua chave dentro da tag <application>:

XML

<application ...>
    <meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="SUA_CHAVE_API_DO_GOOGLE_MAPS_AQUI"/>
    ...
</application>

# Facebook Login:

No painel Facebook for Developers, crie um novo aplicativo.

Obtenha seu App ID e Client Token.

Abra o arquivo android/app/src/main/res/values/strings.xml. Crie-o se não existir e adicione:

XML

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Encontre Seu Pet</string>
    <string name="facebook_app_id">SEU_APP_ID_DO_FACEBOOK</string>
    <string name="fb_login_protocol_scheme">fbSEU_APP_ID_DO_FACEBOOK</string>
    <string name="facebook_client_token">SEU_CLIENT_TOKEN_DO_FACEBOOK</string>
</resources>

Abra o arquivo android/app/src/main/AndroidManifest.xml e adicione as seguintes meta-data e activity dentro da tag <application>:

XML

<application ...>
    ...
    <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
    <meta-data android:name="com.facebook.sdk.ClientToken" android:value="@string/facebook_client_token"/>

    <activity android:name="com.facebook.FacebookActivity"
        android:configChanges="keyboard|keyboardHidden|screenLayout|screenSize|orientation"
        android:label="@string/app_name" />
    <activity
        android:name="com.facebook.CustomTabActivity"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="@string/fb_login_protocol_scheme" />
        </intent-filter>
    </activity>
    ...
</application>

IMPORTANTE (Login Google/Facebook): Para o login social no Android funcionar, você precisa gerar uma SHA-1 hash da sua chave de debug (e release) e adicioná-la nas configurações do projeto Firebase (para Google) e no painel do Facebook (para Facebook).

# Parte 3.2: Configuração do iOS
Google Maps API Key:

No Google Cloud Console, ative a API "Maps SDK for iOS".

Use a mesma API Key criada para o Android (ela pode ser usada para ambas as plataformas).

Abra o arquivo ios/Runner/AppDelegate.swift.

Importe GoogleMaps e adicione sua chave no método didFinishLaunchingWithOptions:

Swift

import UIKit
import Flutter
import GoogleMaps // Importe o Google Maps

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {

    GMSServices.provideAPIKey("SUA_CHAVE_API_DO_GOOGLE_MAPS_AQUI") // Adicione sua chave

    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}

# Facebook Login:

Abra o arquivo ios/Runner/Info.plist.

Adicione o seguinte bloco de chaves dentro da tag principal <dict>:

XML

<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>fbSEU_APP_ID_DO_FACEBOOK</string>
        </array>
    </dict>
</array>
<key>FacebookAppID</key>
<string>SEU_APP_ID_DO_FACEBOOK</string>
<key>FacebookClientToken</key>
<string>SEU_CLIENT_TOKEN_DO_FACEBOOK</string>
<key>FacebookDisplayName</key>
<string>Encontre Seu Pet</string>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>fbapi</string>
    <string>fb-messenger-share-api</string>
</array>

Adicione também as permissões de localização (necessário para o geolocator):

XML

<key>NSLocationWhenInUseUsageDescription</key>
<string>Este app precisa da sua localização para encontrar pets perdidos perto de você.</string>
<key>NSLocationAlwaysUsageDescription</key>
<string>Este app precisa da sua localização para encontrar pets perdidos perto de você.</string>

# Parte 4: Rodando o Projeto
Após todas as configurações, você está pronto para rodar o projeto.

Inicie o Servidor Backend:

Em um terminal, navegue até a pasta backend/.

Execute execute o script de início padrão:

node server.js

Você deve ver a mensagem Servidor rodando na porta 3000.

Inicie o Aplicativo Flutter:

Em um segundo terminal, na pasta raiz tccguilherme/.

Verifique se um emulador está rodando ou um dispositivo físico está conectado (flutter devices).

Execute o aplicativo:

flutter run

Se todos os passos foram seguidos e as chaves de API inseridas corretamente, o aplicativo e o servidor deverão se conectar e funcionar como esperado.


