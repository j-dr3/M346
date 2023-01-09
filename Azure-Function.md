# Übersicht

Diese Azure Function ruft die 3rd-Party Weather API von OpenWeatherMap auf und gibt das Wetter für eine angegebene Stadt zurück.

## API-Endpunkt

Die Azure Function kann über den folgenden Endpunkt aufgerufen werden:

`https://weather-jd.azurewebsites.net/api/Weather-Func?city=Berlin`

Der city-Parameter ist erforderlich und gibt die Stadt an, für die das Wetter abgerufen werden soll.

## Beispielaufruf

Um das Wetter für London abzurufen, würde man die folgende URL aufrufen:

`https://weather-jd.azurewebsites.net/api/Weather-Func?city=London`

## Beispielantwort

Die Azure Function gibt das Wetter als JSON-Objekt zurück, das die folgenden Eigenschaften enthält:

    Name: Der Name der Stadt
    Temperature: Die aktuelle Temperatur in Kelvin
    Description: Eine Beschreibung des aktuellen Wetters

Hier ist ein Beispiel für eine Antwort:

```json
{
  "Name": "London",
  "Temperature": 38.9,
  "Description": "clear sky"
}
```

## Fehlerbehandlung

Wenn der city-Parameter nicht angegeben wurde oder wenn es ein Problem beim Aufrufen der Weather API gab, wird ein Fehlercode und eine Fehlermeldung zurückgegeben.

Hier sind einige mögliche Fehlercodes und ihre Bedeutungen:

    400 Bad Request: Der city-Parameter wurde nicht angegeben.
    401 Unauthorized: Der API Key ist ungültig oder wurde nicht korrekt konfiguriert.
    500 Internal Server Error: Es gab ein Problem beim Aufrufen der Weather API.

## Konfiguration

Die Azure Function benötigt einen gültigen API Key für die Weather API von OpenWeatherMap. Dieser API Key muss in der App-Settings-Datei der Azure Function unter dem Schlüssel "OpenWeatherMapApiKey" konfiguriert werden.

## Implementation in Azure

Um diese Azure Function in Azure zu implementieren, gibt es ein paar Schritte, die man ausführen muss:

1. Eine Azure Function-App im Azure-Konto erstellen. Dies kann man entweder über die Azure-Portal-Oberfläche oder über die Azure-Befehlszeilenschnittstelle (CLI) tun.

2. Eine neue Funktion innerhalb der Function-App erstellen. Man kann entweder einen Vorlagencode auswählen oder eine leere Funktion erstellen.

3. Den Code der neuen Funktion durch den Code, den ich oben gegeben habe ersetzen. Hierbei muss man sicherstellen, dass man den API Key für die Weather API in der App-Settings-Datei konfiguriert hat.

4. Die Funktion testen die, indem man den API-Endpunkt aufruft und die erwarteten Ergebnisse überprüft.

5. Die Funktion veröffentlichen, indem man den Code in die Function-App hochladen.

## Fazit

Leider ist es mir nicht gelungen die funktion zum laufen zu kriegen. So wie ich meine "Dev-Skills" kenne ist es vermutlich ein sehr simpler fehler. Die funktion sieht wie folgt aus:

```c#

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
    ILogger log, ExecutionContext context)
{
    // Lese den API Key aus der App-Settings-Datei
    var config = new ConfigurationBuilder()
        .SetBasePath(context.FunctionAppDirectory)
        .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
        .AddEnvironmentVariables()
        .Build();
    string apiKey = config["OpenWeatherMapApiKey"];

    // Hole die Stadt aus der Query-String-Parameter
    string city = req.Query["city"];

    // Baue die API-URL mit dem API Key und der Stadt zusammen
    string url = $"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={apiKey}";

    // Rufe die API auf und deserialisiere das Ergebnis
    var client = new WebClient();
    string json = await client.DownloadStringTaskAsync(url);
    var result = JsonConvert.DeserializeObject<WeatherResult>(json);

    // Gib das Ergebnis als JSON zurück
    return new OkObjectResult(result);
}

public class WeatherResult
{
    public string Name { get; set; }
    public double Temperature { get; set; }
    public string Description { get; set; }
}

```

Ich finde aber, dass die Funktion dafür, dass das mein erster C# Code ist, sehr stattlich aussieht.
