# Smart Home Control Center 🏠

Detta projekt är en "Smart Home Hub" byggd i C# (.NET) som demonstrerar praktisk användning av objektorienterade designmönster. 
Systemet hanterar enheter (lampor, termostater, dörrlås) och använder mönster för att skapa en modulär, skalbar och lättskött arkitektur.

## 🚀 Hur man kör programmet

1. Klona repot, exempelvis genom "git clone https://github.com/b1-loop/Smart-Home-.git" i git bash.
2. Öppna en terminal i projektets rotmapp (där `.csproj`-filen ligger).
3. Kör programmet med kommandot:
   `dotnet run`

## 📁 Projektstruktur

````text
SmartHomeControl/
├── Commands/
│   ├── ICommand.cs
│   ├── SmartHomeInvoker.cs
│   ├── TurnOnCommand.cs
│   ├── TurnOffCommand.cs
│   └── SetTemperatureCommand.cs
├── Devices/
│   ├── IDevice.cs
│   ├── Light.cs
│   ├── Thermostat.cs
│   └── DoorLock.cs
├── Modes/
│   ├── ISmartHomeMode.cs
│   ├── NormalMode.cs
│   ├── EcoMode.cs
│   └── PartyMode.cs
├── Observer/
│   ├── IObserver.cs
│   ├── ISubject.cs
│   ├── Dashboard.cs
│   ├── AuditLog.cs
│   └── MobileAppAlert.cs
├── Logger.cs
├── SmartHomeFacade.cs
├── Program.cs
└── SmartHomeControl.csproj
````

Designmönster i koden
Här är de fem mönster som implementerats och vilket problem de löser:

Observer (Live-uppdateringar): Finns i mappen Observer/ och gränssnittet ISubject. Mönstret frikopplar enheterna från användargränssnittet och loggningen. När en enhet ändrar status notifieras tre lyssnare (Dashboard, AuditLog, MobileAppAlert) automatiskt, utan att enheten behöver veta om deras existens.

Command (Klick och actions): Finns i mappen Commands/. Kapslar in begäranden som objekt (t.ex. TurnOnCommand eller SetTemperatureCommand). Detta löser problemet med att spara historik och gör det möjligt att bygga funktioner som Undo och Replay (köra tidigare kommandon igen).

Strategy (Olika lägen): Finns i mappen Modes/. Används för att dynamiskt ändra hur hela hemmet agerar (t.ex. EcoMode eller PartyMode). Istället för massiva if-satser i huvudprogrammet kapslas reglerna in i separata, utbytbara algoritmklasser.

Facade (Enkelt API): Finns i SmartHomeFacade.cs. Ger ett förenklat gränssnitt till det komplexa systemet bakom. Huvudprogrammet (Program.cs) behöver inte hålla koll på listor, manuella kopplingar av Observers eller Command-invokers, utan kan styra systemet via enkla anrop som MorningRoutine().

Singleton (Gemensam instans): Finns i Logger.cs. Säkerställer att det bara existerar en enda instans av loggern i hela systemet. Detta gör att alla klasser kan skriva till samma loggflöde via en global åtkomstpunkt (Logger.Instance.Log()).

## Klassdiagram
(OBS: Bilden visar hur SmartHomeFacade agerar gränssnitt för de underliggande mönstren och relationerna mellan enheter, kommandon och lägen).
![Smart Home Diagram](images/Smart_Home_Diagram.png)

## Demo Output
- Såhär ser utskriften ut i konsolen när programmet körs:

````
[LOG] 15:07:50: Kökslampa lades till i hubben.
[LOG] 15:07:50: Vardagsrumstermostat lades till i hubben.
[LOG] 15:07:50: Ytterdörr lades till i hubben.

--- Startar Morgonrutin ---
[LOG] 15:07:50: Kökslampa tändes.
[Dashboard] Live-uppdatering: Kökslampa har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Kökslampa'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Kökslampa ändrade status.
[LOG] 15:07:50: Vardagsrumstermostat ställdes in på 21°C.
[Dashboard] Live-uppdatering: Vardagsrumstermostat har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Vardagsrumstermostat'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Vardagsrumstermostat ändrade status.

*** Aktiverar Eco Mode ***
[LOG] 15:07:50: Kökslampa släcktes.
[Dashboard] Live-uppdatering: Kökslampa har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Kökslampa'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Kökslampa ändrade status.
[LOG] 15:07:50: Vardagsrumstermostat ställdes in på 18°C.
[Dashboard] Live-uppdatering: Vardagsrumstermostat har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Vardagsrumstermostat'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Vardagsrumstermostat ändrade status.
[LOG] 15:07:50: Ytterdörr låstes.
[Dashboard] Live-uppdatering: Ytterdörr har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Ytterdörr'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Ytterdörr ändrade status.

--- Testar Undo via Facade ---
[LOG] 15:07:50: Ångrar senaste kommandot...
[LOG] 15:07:50: Kökslampa tändes.
[Dashboard] Live-uppdatering: Kökslampa har ändrats.
[Audit] Säkerhetslogg: Statusändring registrerad på 'Kökslampa'.
[LOG] 15:07:50: [Mobilapp] Pushnotis: Kökslampa ändrade status.

Systemtest klart!
````

## Reflektion - När ska man INTE använda design mönster?

När det inte behövs. En enda lampa behöver inte Command (fjärrkontroll) eller Observer (lyssnare), så att ha med det innebär bara kod som inte fyller någon nytta alls. 
Designmönster bör användas om/när ett system växer och man behöver anpassa det till olika situationer. 
Annars tillför de bara onödig komplexitet som gör koden svårare att läsa och underhålla.
