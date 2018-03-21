# Workshop Origo, Kartdagarna 2018

Länk till den här sidan: [https://origo-map.github.io/workshop/](https://origo-map.github.io/workshop/)

Det här behöver du:

- Origo: <https://github.com/origo-map/origo>
- Mongoose (webbserver): <https://cesanta.com/binary.html>
- Editor: <https://notepad-plus-plus.org/download/v7.5.6.html>
- En dator med administratörs-rättigheter.

## Förberedelser

- Skapa en ny lokal mapp, tex C:/workshop.
- Ladda ned Mongoose enligt länken ovan och spara exe-filen i mappen från föregående steg.
- Ladda ned senaste versionen av Origo från GitHub, enligt länken ovan.
- Packa upp upp den nedladdade zip-filen på valfri plats. Kopiera alla filer under mappen build och klistra in innehållet direkt under mappen C:/workshop. Din filstruktur under c:/workshop borde nu se ut så här:

  ```
  api-documentation/
  css/
  data/
  examples/
  img/
  js/
  index.html
  index.json
  mongoose-free-6.9.exe
  ```

- Starta webbservern Mongoose genom att dubbelklicka på exe-filen i workshop-mappen. Om du redan har en tjänst som använder port 8080 kommer det inte gå att starta webbservern. Be en workshop-ledare om hjälp i så fall.
- Om allt har gått rätt ska du nu kunna se en Origo-karta genom att följa den här länken: <http://10.0.1.16:8080>.
- Installera Notepad++ eller hoppa över steget om du redan har en editor som du vill använda.
- Som stöd till workshopen kan du med fördel använda api-dokumentationen för Origo: <https://origo-map.github.io/api-documentation/latest/#origo-api>

## Grundläggande konfigurationer

- Om du inte redan gjort det, öppna en webbläsare och gå in på sidan <http://10.0.1.16:8080>
- Om du inte ser en karta, tillkalla en workhop-ledare för hjälp.
- I kartans nedre vänstra hörn syns en kontroll för att visa koordinater. Använd den för att hitta koordinaterna till din arbetsplats eller hemkommun.
- Öppna Origos konfigurationsfil, dvs **index.json**, i Notepad++ eller motsvarande.
- Sätt koordinaterna från ovan som kartans start-koordinater, tex:

`"center": [1842027, 8313677],`

- Ändra också vilken zoomnivå kartan ska ha när den laddas, tex:

`"zoom": 10,`

- Ladda om kartan för att se förändringen.

## Lägga till ett lager

I Origo finns stöd för att använda en rad olika format och datakällor, inklusive WMTS, WMS, WFS, ArcGIS Server, Vector tiles, GeoJSON och TopoJSON. I den här övningen ska du lägga till ett nytt lager av typen **WFS** från GeoServer.

- Börja med att skapa en ny datakälla, **source**, genom att klistra in följande kod under source, direkt efter den befintliga datakällan local:

```
"origo": {
  "url": "https://www.malardalskartan.se/geoserver/origo/ows",
  "workspace": "origo"
}
```

- Under layers finns kartans samtliga lager definierade. I det här steget ska du lägga till en ny lagerdefnition genom att utgå från datakällan i föregående steg. Klistra in följande kod under layers, precis innan lagret **origo-cities**:

```
{
  "name": "ws-cities",
  "title": "Workshopdeltagare",
  "group": "test",
  "source": "origo",
  "style": "cirkel",
  "type": "WFS",
  "visible": true,
  "attributes": [
      {
        "name": "name"
      }
  ]
},
```

- Städa kartan genom att ta bort lagerdefinitionerna för **origo-cities** och **origo-mask**.
- Uppdatera kartan för att se resultatet.

## Byta stil på ett lager

I den här övningen ska du prova att byta stil på WFS-lagret från ovan. Eftersom WFS är ett vektor-baserat format görs detta i klienten, dvs i Origo. Kartans samtliga stilar finns definierade under **styles**.

- Kopiera följande stil och klistra in under styles:

```
"person": [
  [{
    "icon": {
      "size": [32, 32],
      "src":"https://origo-map.github.io/workshop/img/person.png"
    }
  }]
],
```

- För lagret **ws-cities**, byt style från **cirkel** till **person**. Nu ska det se ut så här:

```
{
  "name": "ws-cities",`
  "title": "Workshopdeltagare",`
  "group": "test",`
  "source": "origo",`
  "style": "person",`
  "type": "WFS",`
  "visible": true,`
  "attributes": [`
      {
        "name": "name"`
      }
  ]
},
```

- Uppdatera kartan.

## Lägga till kontroll för redigering

I Origo finns det en inbyggd kontroll för att göra enkla redigeringar via webbgränssnitt. För närvarande finns det stöd för att redigera via WFS och ArcGIS Server feature services. I den här övningen ska du prova att skapa en redigeringstjänst med hjälp av din WFS-tjänst. För att det ska fungera krävs att editor-kontrollen är tillagd i kartan och att WFS-lagret är redigerbart.

- Lägg till följande kodsnutt under controls:

```
{
  "name": "editor",
  "options": {
    "isActive": true,
    "autoSave": true
  }
},
```

- Gör lagret redigerbart genom att lägga till raden `"editable": true` och `"geometryType": "Point"`. Lägg också till en inställning för att möjliggöra redigering av attribut, `"type": "text"`. Lagerdefinitionen ska nu se ut så här:

```
{
  "name": "ws-cities",
  "title": "Workshopdeltagare",
  "group": "test",
  "source": "origo",
  "style": "person",
  "type": "WFS",
  "visible": true,
  "editable": true,
  "geometryType": "Point",  
  "attributes": [
      {
        "name": "name",
        "type": "text"
      }
  ]
},
```


* Efter att du har uppdaterat kartan ska du nu se en verktygsrad med redigeringsverktyg i botten av kartan.

## Använda redigeringsverktyget

Med redigeringsverktyget i Origo kan man ändra, lägga till och ta bort objekt i kartan. I den här övningen ska du lägga till en punkt med tillhörande attributinformation.

- Välj pennan för att skapa ny punkt. Placera punkten på din nuvarande arbetsplats eller hemmakommun.
- Efterjusteringar kan göras genom att markera punkten och dra i den med muspekaren.
- Öppna formuläret genom att klicka T-knappen. Skriv in namnet på platsen och klicka på Ok.
- När du är nöjd med resultatet, avaktivera redigeringsverkyget genom att ändra isActive till false:

```
{
  "name": "editor",
  "options": {
    "isActive": false,
    "autoSave": true
  }
},
```

- Uppdatera kartan

## Extra övningar
Om du får tid över och vill testa Origo lite mer kan du göra de här extra övningarna. Använd api-dokumentationen som hjälp.

* Testa att lägga till en splash-kontroll med valfri text som innehåll.
* WFS-lagret ws-cities innehåller också attributet url. Testa att använda det attributet för att visa en klickbar länk som visas när man identifierar lagret.
* Testa att lägga till ytterligare ett koordinatsystem för position-kontrollen. Tips: definitioner för olika koordinatsystem kan hittas på [epsg.io](http://epsg.io/).
