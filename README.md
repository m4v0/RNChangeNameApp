# React Native 

# Alterar o nome do aplicativo, o nome do pacote e a versão do código para Android

# Resumo
  Após varias pesquisas na internet, senti a necessidade de escrever este conteúdo em 
  portugues, para disponibilizar a quem queira usar, também minimizar o trabalho de 
  entender todo o processo.

# Objetivo
  
  Tem o objetivo de explicar como alterar as seguintes instruções do aplicativo::
  
  > Nome do Aplicativo;
  > Nome do Pacote;
  > Versão do código;

# Modo correto de iniciar um projeto em react-native

  Todos nos sabemos que para gerar um projeto react-native, usamos a seguinte linha de comando:

  > react-native init <nome_projeto>

  Porem podemos já na criação utilizar o seguinte comando:

  > react-native init <nome_do_projeto> -package "br.com.<dominio>.<nome_do_aplicativo>"
  
  Neste procedimento o projeto já estará com o nome do package definitivo, sem a necessidade de realizar 
  alterações futuras.

# Como fazer
  Quando utilizamos a primeira opção da seção acima, em algum momento voce terá necessidade de alterar o nome 
  do pacote, então siga as instruções abaixo e verá que é super fácil.

  * Mudar o nome do aplicativo (App Name)
  
    > na pasta android/app/src/main/res/values/ do seu projeto abra o arquivo "strings.xml" procure pela 
      "<string name="app_name">My Android App</string>", onde tem "My Android App", você informa o nome
      do seu aplicativo ex: "Gerador de números", salve o arquivo e compile para ver o resultado.

  * Mudar o nome do package 
  
    O name package é exibido no Google Play juntamente com os detalhes do Aplicativo, então certifique-se 
    que o nome seja do seu agrado. Para fazer este procedimento é necessario editar cinco arquivos:

    * android/app/src/main/java/com/<nome_do_projeto>/MainActivity.java
    * android/app/src/main/java/com/<nome_do_projeto>/MainApplication.java
    * android/app/src/main/AndroidManifest.xml
    * android/app/build.gradle
    * android/app/BUCK

    Para exemplificar usaremos o nome do package "br.com.mavo.exemplo".

    Os dois primeiros arquivos Java têm o nome do pacote como algo abaixo:
    
    > package com.myapp;

    mude este nome para:

    > package br.com.mavo.exemplo;

    No arquivo AndroidManifest.xml localize e altere "package="br.com.mavo.exemplo".

    No arquivo build.gradle, localize e altere para applicationId "br.com.mavo.exemplo"

    No arquivo BUCK, localize e mude:
    
      > android_build_config(
        package="br.com.mavo.exemplo"
      )

      > android_resource(
        package="br.com.mavo.exemplo"
      )
    
    Após todas as mudanças e ter salvo todos os arquivos, vamos executar este comando (na pasta "android"):

    > ./gradlew clean

  * Mudar a versão do Aplicativo

    Cada um pode optar em fazer essa alteração de formas diversas, porém quando pesquisei na internet, achei 
    um artigo (https://github.com/AndrewJack/versioning-react-native-app) para quem quiser ver na integra, vou
    simplificar aqui o que ele fez, primeiro vamos alterar dois arquivos:

    * android/build.grable;
    * android/app/build.grable;

    Mão na massa galera, vamos editar o arquivo "android/build.grable" e acrescentar as seguintes linhas:
    
    * Na linha 2 adicione "import groovy.json.JsonSlurper"
    * No final do arquivo insira as linhas abaixo:

        subprojects {
            ext {
                def npmVersion = getNpmVersionArray()
                versionMajor = npmVersion[0]
                versionMinor = npmVersion[1]
                versionPatch = npmVersion[2]
            }
        }

        def getNpmVersion() {
            def inputFile = new File("../package.json")
            def packageJson = new JsonSlurper().parseText(inputFile.text)
            return packageJson["version"]
        }

        def getNpmVersionArray() { // major [0], minor [1], patch [2]
            def (major, minor, patch) = getNpmVersion().tokenize('.')
            return [Integer.parseInt(major), Integer.parseInt(minor), Integer.parseInt(patch)] as int[]
        }

    Vamos entender o que foi realizado, na seção subprojects é criado a def npmVersion e cria três variáveis,
    que são do tipo ext, a função getNpmVersion, é responsável por ler o arquivo "package.json" que esta no
    raiz do projeto, e pega a variável "version", a função getNpmVersionArray coloca em uma array que é criada
    pela separação dos "."

    Vamos editar o arquivo "android/app/build.grable" e ascrecentar as seguintes linhas na seção defaulConfig:

    > versionCode versionMajor * 10000 + versionMinor * 100 + versionPatch
    
    > versionName "${versionMajor}.${versionMinor}.${versionPatch}"

    Finalizando todos estes procedimentos de versionamento, basta você mudar a versão no arquivo package.json, 
    no parametro "version" na hora de compilar o seu projeto o grable vai fazer a verificação da versão.

    Simples né? rsrsrs

-28082018234000

