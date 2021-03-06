---
title: Konfigurera Node. js-appar
description: Lär dig hur du konfigurerar en fördefinierad Node. js-behållare för din app. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670276"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurera en Linux Node. js-app för Azure App Service

Node. js-appar måste distribueras med alla nödvändiga NPM-beroenden. Kudu (App Service Deployment Engine) körs automatiskt `npm install --production` åt dig när du distribuerar en [git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), eller ett [zip-paket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med skapande processer växlat. Om du distribuerar dina filer med [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)måste du dock ladda upp de nödvändiga paketen manuellt.

Den här guiden innehåller viktiga begrepp och instruktioner för Node. js-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du [snabb starten för Node. js](quickstart-nodejs.md) och [Node. js med MongoDB-kursen](tutorial-nodejs-mongodb-app.md) först.

## <a name="show-nodejs-version"></a>Visa Node. js-version

Om du vill visa den aktuella Node. js-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla Node. js-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Ange Node. js-version

Om du vill ställa in din app på en [Node. js-version som stöds](#show-nodejs-version)kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Den här inställningen anger vilken Node. js-version som ska användas, både vid körning och under automatisk paket återställning i kudu.

> [!NOTE]
> Du bör ange Node. js-versionen i projektets `package.json`. Distributions motorn körs i en separat behållare som innehåller alla Node. js-versioner som stöds.

## <a name="configure-nodejs-server"></a>Konfigurera Node. js-Server

Node. js-behållare levereras med [PM2](https://pm2.keymetrics.io/), en produktions process hanterare. Du kan konfigurera appen så att den startar med PM2, eller med NPM, eller med ett anpassat kommando.

- [Kör anpassat kommando](#run-custom-command)
- [Kör NPM-start](#run-npm-start)
- [Kör med PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Kör anpassat kommando

App Service kan starta din app med ett anpassat kommando, till exempel en körbar fil som *Run.sh*. Om du till exempel vill köra `npm run start:prod`kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Kör NPM-start

För att starta din app med hjälp av `npm start`kontrollerar du bara att ett `start`-skript finns i *Package. JSON* -filen. Exempel:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Om du vill använda en anpassad *Package. JSON* -modul i projektet kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Kör med PM2

Behållaren startar automatiskt appen med PM2 när en av de vanliga Node. js-filerna hittas i projektet:

- *bin/www*
- *Server. js*
- *app. js*
- *index. js*
- *hostingstart. js*
- En av följande [PM2-filer](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process. JSON* och *eko system. config. js*

Du kan också konfigurera en anpassad start fil med följande fil namns tillägg:

- En *. js* -fil
- En [PM2-fil](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) med fil namns tillägget *. JSON*, *. config. js*, *. yaml*eller *. yml*

Om du vill lägga till en anpassad start fil kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Felsöka via en fjärranslutning

> [!NOTE]
> Fjärrfelsökning är för närvarande en för hands version.

Du kan felsöka Node. js-appen via fjärr anslutning i [Visual Studio Code](https://code.visualstudio.com/) om du konfigurerar den att [köras med PM2](#run-with-pm2), förutom när du kör den med hjälp av *. config. js, *. yml eller *. yaml*.

I de flesta fall krävs ingen extra konfiguration för din app. Om din app körs med en *process. JSON* -fil (standard eller anpassad), måste den ha en `script`-egenskap i JSON-roten. Exempel:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Om du vill konfigurera Visual Studio Code för fjärrfelsökning, installerar du [App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Följ instruktionerna på sidan anknytning och logga in på Azure i Visual Studio Code.

I Azure Explorer letar du reda på den app som du vill felsöka, högerklickar på den och väljer **Starta fjärrfelsökning**. Klicka på **Ja** för att aktivera den för din app. App Service startar en tunnel-proxy för dig och kopplar fel söknings programmet. Du kan sedan göra förfrågningar till appen och se fel söknings verktyget pausas vid Bryt punkter.

När du är färdig med fel sökningen stoppar du fel sökningen genom att välja **Koppla från**. När du uppmanas bör du klicka på **Ja** om du vill inaktivera fjärrfelsökning. Om du vill inaktivera det senare högerklickar du på din app igen i Azure Explorer och väljer **inaktivera fjärrfelsökning**.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appens kod. Sedan kan du komma åt dem med hjälp av standard-Node. js-mönstret. Om du till exempel vill få åtkomst till en appinställning med namnet `NODE_ENV` använder du följande kod:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Kör grunt/Bower/Gulp

Kudu kör som standard `npm install --production` när den identifierar en Node. js-app distribueras. Om din app kräver något av de populära automatiserings verktygen, till exempel grunt, Bower eller Gulp, måste du ange ett [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) för att köra det.

Om du vill göra det möjligt för lagrings platsen att köra dessa verktyg måste du lägga till dem i beroenden i *Package. JSON.* Exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

I ett lokalt terminalfönster ändrar du katalogen till lagrings platsens rot och kör följande kommandon:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Din databas rot har nu två ytterligare filer: *. distribution* och *Deploy.sh*.

Öppna *Deploy.sh* och hitta `Deployment`-avsnittet som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Det här avsnittet slutar med att köra `npm install --production`. Lägg till kod avsnittet du måste köra det nödvändiga verktyget *i slutet* av `Deployment` avsnittet:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Se ett [exempel i Mean. js-exemplet](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)där distributions skriptet också kör ett anpassat `npm install`-kommando.

### <a name="bower"></a>Bower

Det här kodfragmentet kör `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Det här kodfragmentet kör `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodfragmentet kör `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [Express](https://expressjs.com/)kan du använda [betrodda proxyservrar](https://expressjs.com/guide/behind-proxies.html). Exempel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsöka

Prova följande när en fungerande Node. js-app fungerar annorlunda i App Service eller innehåller fel:

- [Åtkomst till logg strömmen](#access-diagnostic-logs).
- Testa appen lokalt i produktions läge. App Service kör Node. js-appar i produktions läge, så du måste se till att projektet fungerar som förväntat i produktions läge lokalt. Exempel:
    - Beroende på *Package. JSON*kan olika paket installeras i produktions läge (`dependencies` jämfört med `devDependencies`).
    - Vissa webb ramverk kan distribuera statiska filer på ett annat sätt i produktions läge.
    - Vissa webb ramverk kan använda anpassade Start skript när de körs i produktions läge.
- Kör din app i App Service i utvecklings läge. I [Mean. js](https://meanjs.org/)kan du till exempel ställa in appen i utvecklings läge i körningen genom [att ange inställningen `NODE_ENV` app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Node. js-app med MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
