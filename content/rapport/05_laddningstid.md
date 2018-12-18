#Kmom05

## Introduktion

I denna rapporten kommer jag analysera, diskutera, jämföra och föreslå förbättringar på tre olika hemsidor; Pinterest, Youtube, Instagram och hur de kan minska deras laddningstid. Jag valde dessa tre hemsidor för att de har alla en sak gemensamt; många bilder och tumnagelbilder (thumbnails). Alla tre hemsidor hanterar deras caching och optimering lite annorlunda och jag kommer diskutera och analysera lite djupare i det i denna rapporten.

## Urval

Pintrest

YouTube

Instagram

Jag har valt dessa hemsidor för de ägs av stora företag som hanterar stora mängder av trafik. Det betyder att de är tvungna att lösa problem som kommer med stora mängder trafik. Alla hemsidor använder också lite annorlunda metoder för att minska trafik mellan användaren och servern och vill titta närmare på dem och analysera deras metoder.

## Metod

För min analys har jag använt Google Chrome. Dev tools i Google Chrome för att mäta hastighet, titta på service workers och se cachet. Google Spreadsheets har jag använt för att dokumentera de olika hastigheterna på alla hemsidor.

## Resultat

Efter min analys och testning av dessa hemsidor som använder olika metoder för att minska deras laddningstid. Det effektivaste sättet är att använda delar från alla hemsidor. Det viktigaste är att komprimera dina bilder med något verktyg. Om det är WebP eller vanlig jpeg komprimering så hjälper det extremt mycket. Det gör att alla bilder tar mycket mindre bandbredd för servern och minskar nerladdningstiden för användaren.

Sekundärt är att cacha alla sina statiska filer med hjälp av service workers. De fungerar som en proxy mellan användaren och servern. När användaren laddar ner en JS eller CSS fil så kommer den cachas på användarens sida. Detta gör så när användaren frågar efter dessa filer igen. Så letar service worker efter den filen i cachet och laddar den filen istället för att ladda ner det från servern.

## Analys

### Bilder

Alla tre hemsidor använder sig av JPG/JPEG när de har komprimerat sina bilder som de använder. Själv är jag förvånad av att ingen av dem använder sig av webp. Lite kort om webp, är en kort text från google om vad webp är för något.

<p style="font-size: 14px; font-style: italic; margin-bottom: 0"> “WebP is a modern image format that provides superior lossless and lossy compression for images on the web. Using WebP, webmasters and web developers can create smaller, richer images that make the web faster. WebP lossless images are 26% smaller in size compared to PNGs. WebP lossy images are 25-34% smaller than comparable JPEG images at equivalent SSIM quality index.” </p>

<p style="font-size: 12px; font-style: italic; opacity: 0.65;"> - A New Image Format for the Web | WebP | Google Developers." Google. Google, 26 Sept. 2018. Web. 13 Dec. 2018.</p>

Webp är något relativt nytt som kommer antagligen byta ut alla JPEG och PNGs i framtiden då webp erbjuder lossless och lossy komprimering och transparens / alpha färgkanal. Tyvärr så fungerar inte webp på alla stora webbläsare just nu. Enligt caniuse.com så stöds det bara av Chrome, Opera och Edge. Safari och Firefox stödjer inte webp just nu.

Men efter lite funderande kom jag på en lösning på detta problemet. Och det att man kollar på användarens user-agent sträng. Sedan kan man matcha den strängen till olika webbläsare och skicka olika html filer, en använder webp och den andra använder jpg. Om användaren använder t.ex Chrome så kan man skicka html filen med webp. Och om användaren har Safari eller en user agent som inte matchar så skickar man html filen med jpgs istället.

Ett bättre alternativ är om man använder html templates så kan man skicka med vilket filformat som ska laddas för alla bilder. Och ett sista alternativ är att man ändrar bild src attributet om bilden inte visas. Men detta är inte en bra lösning för då kommer användaren alltid ladda bilderna dubbelt.

### Caching

Som tidigare, förvånad att många av dessa sidor inte använder caching. Men det är en hemsida som använder caching väldigt effektivt. De har nyligen haft en makeover för deras hemsida var extremt långsam. Pinterest cachar alla sina css och js filer med hjälp av service workers. Det är en förvånande mängd av data de sparar vid varje request.

Om man kikar i Application tabben i chrome och sedan Cache Storage, där kan man se alla filer som har blivit cachat. Och till sist, för att se mängd cache så man man gå till Clear storage under Application och där ser man att de har cachat 189 MB. Detta betyder då att de sparar 189 MB vid varje request om de hade inte cachat deras CSS och JS filer.

Detta fungerar via en service worker som fungerar som en proxy mellan användaren och servern. När man laddar ner Pinterest för första gången så tar den en liten stund att ladda ner. Men sedan aktiveras “The worker” och cachar alla filer som är listade i en manifest.json någonstans. Den sedan lagrar dessa filer på din hårddisk. Sedan när du frågar efter dessa filer så kommer den ladda dessa filer från din hårddisk istället för att hämta det från servern varje gång du besöker Pinterest.

Tyvärr så gör varken YouTube eller Instagram detta som kan spara en massa trafik och bandbredd på deras servrar. Men antagligen varför de har inte gjort det, är för att deras hemsidor är så stora så det är svårt att veta exakt vad som borde bli cachat och vad som borde inte bli det. Men det är något att förbättra på.

### Sammanfattning

Alla dessa tre hemsidor; YouTube, Pinterest och Instagram kan förbättras med att komprimera deras bilder med webp formatet istället. Detta kommer göra att hemsidan laddas mycket snabbare för användaren och det kommer att spara mycket trafik och bandbredd för servererna.

Själv gjorde jag ett litet test. Jag skapade ett Node.js projekt där jag hade 27st bilder som var totalt 357 MB i storlek. Jag gjorde ett laddnings test på denna sidan med alla jpgs. Det slutade med att sidan tog 2.52s att ladda sidan, bilderna var så stora att de gjorde att webbläsaren hängde sig i ett par sekunder till.

Sedan tog jag alla mina stora bilder och konverterade dem till webp format istället. Resultatet av det blev att sidan tog 0.593ms att ladda och 35.2 MB i totala storlek. Det är en otrolig 425% snabbare än att använda jpgs. Men då kanske man tänker sig att bildkvaliteten måste vara mycket sämre. Men jag tog båda sidor och flippade mellan dem väldigt snabbt. Och jag såg ingen skillnad alls. Ser exakt likadana ut.

I slutendan när jag testade alla hemsidor så är rent tekniskt sätt Instagram snabbast. Men anledningen till det är för att de har inte så många bilder på skärmen samtidigt. Däremot Youtube kom inte långt efter instagram, men de har flera gånger mer bilder på skärmen samtidigt. Så personligen tycker jag att YouTube är den snabbaste sidan ut av alla tre som jag har testat.

Sedan när det kommer till cache. Det är lite svårare än vad man tror. Det finns dock ett par bibliotek för service workers som Google har utvecklat. Men det är inget jag har själv testat. Jag har lyckats att skriva en egen service worker från grunden. Men det var inte lätt. Men om man använder ramverken och cachar alla sina statiska CSS och JS filer så kan man spara på mer trafik och bandbredd. Och det är något fler hemsidor borde använda. Du kan också göra så din hemsida fungerar helt offline när du inte har något internet med service workers. Men det är en annan diskussion.

##### Du kan se mina tester av de tre hemsidorna och mina egna test nedan

<iframe style="width: 100%; height: 430px"src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSh-6mitp_54_we71NvQkO28BRqCYeZQsWThJxmzpYFd8Caf0eddWEEkgZtddLF7Zhbnj6b-Yl237Q2/pubhtml?widget=true&amp;headers=false"></iframe>

###### YouTube

<img src="img/youtube.PNG" style="width: 100%; display: block;">

###### Pinterest

<img src="img/pinterest.PNG" style="width: 100%; display: block;">

###### Instagram

<img src="img/instagram.PNG" style="width: 100%; display: block;">

###### JPG

<img src="img/jpg_network.PNG" style="width: 100%; display: block;">

###### WEBP

<img src="img/webp_network.PNG" style="width: 100%; display: block;">
