# game-of-life-haskell
Implementation of Conway's Game of Life in Haskell.

<h1> Feladat leírás </h1>

<h2>Életjáték</h2>
<p>Az életjáték (,,the game of life'') egy speciális sejtautomata, amit John
Conway angol matematikus fejlesztett ki 1970-ben. Rengeteg különböző változata
létezik, a következőkben leírt variáns a klasszikusnak számító változat.</p>
<p>Az életjáték végtelen kétdimenziós térben, egy négyzetrácson játszódik.
A négyzetrácsot <em>cellák</em> alkotják. Minden cellában maximum egy élő <em>sejt</em> lehet.
Gyakori ábrázolási mód, hogy az üres cellákat fehér, míg az élő sejtet
tartalmazó cellákat fekete négyzetek jelölik, így egy kép rajzolható ki
az aktuális állapotról.</p>
<p>A játék nem igényel aktív beavatkozást, elegendő csupán a sejtek kezdeti
állapotát megadni. Ez után minden körben kiszámítunk egy
új generációt, tehát azoknak a celláknak a helyét, ahol a lépés után élő sejtek
lesznek. Három eset lehetséges: egy sejt életben maradhat a következő
generációra, meghalhat, vagy új sejt születhet egy üres cellában. Ezt a
következő szabályok határozzák meg.</p>
<ol>
<li>A sejt <em>túléli</em> a kört, ha két vagy három szomszédja van.</li>
<li>A sejt elpusztul, ha kettőnél kevesebb, vagy háromnál több szomszédja van.
Az előbbit <em>elszigetelődés</em>nek, az utóbbit pedig <em>túlnépesedés</em>nek nevezzük.</li>
<li>Új sejt <em>születik</em> minden olyan cellában, melynek környezetében pontosan
három sejt található.</li>
</ol>
<p>Egy cella környezete a hozzá legközelebb eső 8 mező (tehát a cellához képest
átlósan elhelyezkedő cellákat is figyelembe vesszük, és feltesszük, hogy a
négyzetrácsnak nincs széle).</p>
<p>Minden cellát a koordinátái írnak le. A koordináta rendszer <em>x</em> tengelye jobbra
növekszik, <em>az y tengelye pedig lefelé</em>. Mivel a tér végtelen, negatív
koordináták is megengedettek, de csak egész értékekkel dolgozhatunk.</p>
<p><em>Ügyeljünk arra, hogy a koordináták előbb az y, majd az x tengelyen vett
pozíciót tartalmazzák, a számítógépes grafikában megszokott módon!</em></p>
<pre><code class="language-hs">type Coordinate = (Integer, Integer)
</code></pre>
<p>A játék egy állapota az aktuális generáció (az élő sejtek) celláinak megadásából
áll:</p>
<pre><code class="language-hs">type Generation = [Coordinate]
</code></pre>
<p>Egy lépés során tehát az a dolgunk, hogy az aktuális generációból kiszámítsuk az
új generációt a fenti szabályok alapján. <em>Ez minden cellára szimultán történik,
tehát az új generáció kiszámítása során csak az előző generációt vesszük
figyelembe, a már éppen megszülető vagy elpusztuló sejteket nem!</em></p>
<h2>Példák</h2>
<p>Az alábbi példákat vagy másoljuk be a megoldásunkba, vagy töltsük le a feladathoz csatolt base filet!</p>
<h3>Elszigetelődött sejt</h3>
<p>Egyetlen sejt, amely a következő generációra elszigetelődés miatt kihal:</p>
<pre><code class="language-hs">single :: Generation
single = [ (42, 42) ]
</code></pre>
<p>Ne feledjük, mivel egy minden irányba végtelen sejtautomatát képzelünk el, ahol
csak ez az egyetlen élő sejt, így pozíciók bárhol lehetnek ugyanazt az állapotot
írják le.</p>
<p>Pl. a következő <code>Generation</code> megegyezik az előzővel.</p>
<pre><code class="language-hs">single2 :: Generation
single2 = [ (93, 16) ]
</code></pre>
<h3>Csendélet</h3>
<p>Csendélet vagy blokk, ahol mindig ugyanezek a sejtek maradnak életben, és nem születnek újak
sem:</p>
<pre><code class="language-hs">block :: Generation
block = [ (5, 6), (5, 7)
        , (6, 6), (6, 7) ]
</code></pre>
<p><img src="http://ndsblnt.web.elte.hu/GameOfLife_block.png" alt="Block"></p>
<h3>Oszcillátor</h3>
<p>Kétlépéses oszcillátor, ahol az egyik folyton a másikba alakul egy lépés
után. Valójában így egy alakzatnak számítanak, ennek a neve <code>blinker</code>.</p>
<pre><code class="language-hs">row :: Generation
row = [ (10, 1), (10, 2), (10, 3) ]
</code></pre>
<pre><code class="language-hs">column :: Generation
column = [ (9,  2)
         , (10, 2)
         , (11, 2) ]
</code></pre>
<p><img src="http://ndsblnt.web.elte.hu/GameOfLife_blinker.png" alt="Blinker"></p>
<p>Blinker animálva:
<img src="https://conwaylife.com/w/images/b/b9/Blinker.gif" alt="Blinker"></p>
<p>Háromlépéses oszcillátor, amely önmagába tér vissza három lépés után:</p>
<pre><code class="language-hs">caterer :: Generation
caterer = [ (2, 4), (3, 2), (3, 6), (3, 7), (3, 8), (3, 9)
          , (4, 2), (4, 6), (5, 2), (6, 5), (7, 3), (7, 4) ]
</code></pre>
<p><img src="http://ndsblnt.web.elte.hu/GameOfLife_caterer.png" alt="Caterer"></p>
<p>Caterer animálva:
<img src="https://conwaylife.com/w/images/1/17/Caterer.gif" alt="Caterer"></p>
<p>15-ös és 312-es periódusú oszcillátorok:</p>
<pre><code class="language-hs">o15 :: Generation
o15 = [(0,1),(1,1),(2,0),(2,2),(3,1),(4,1),(5,1),(6,1),(7,0),(7,2),(8,1),(9,1)]

o312 :: Generation
o312 = [(1,21),(1,22),(2,21),(2,22),(6,32),(6,33),(7,23),(7,24),(7,31),(7,34),(8,23),(8,32),(8,33),(9,7),(9,23),(10,6),(10,8),(10,24),(11,6),(11,8),(12,7),(19,33),(19,36),(20,34),(20,35),(20,36),(21,1),(21,2),(21,41),(21,42),(22,1),(22,2),(22,41),(22,42),(23,7),(23,8),(23,9),(24,7),(24,10),(31,36),(32,35),(32,37),(33,19),(33,35),(33,37),(34,20),(34,36),(35,10),(35,11),(35,20),(36,9),(36,12),(36,19),(36,20),(37,10),(37,11),(41,21),(41,22),(42,21),(42,22)]
</code></pre>
<p>Pentadecathlon, vagyis a 15-ös peródusú oszcillátor animálva:
<img src="https://conwaylife.com/w/images/3/31/Pentadecathlon.gif" alt="Pentadecathlon"></p>
<p>60p312, vagyis a 312-es peródusú oszcillátor animálva:
<img src="http://ndsblnt.web.elte.hu/o312.gif" alt="60p312"></p>
<h3>Űrhajó</h3>
<p>Az űrhajó olyan alakzat, amely önmagába tér vissza valahány lépés után,
de eközben "utazik", vagyis bármely irányba elmozdul.</p>
<p>A legkisebb űrhajó a <code>glider</code>, vagyis sikló.</p>
<pre><code class="language-hs">glider :: Generation
glider = [(6,5), (7,3), (7,5), (8,4), (8,5)]
</code></pre>
<p><em>Magyarázat:</em> A könnyebb olvashatóság miatt egy piros négyzetet és
fekete keretet helyeztem a sikló köré. A piros négyzet csak egy
tetszőlegesen kiválasztott fehér négyzet.</p>
<p><img src="http://ndsblnt.web.elte.hu/GameOfLife_glider.png" alt="Glider"></p>
<p>Glider animálva:
<img src="https://conwaylife.com/w/images/8/81/Glider.gif" alt="Glider"></p>
<p>Még néhány hajó a a tesztekhez:</p>
<pre><code class="language-hs">lwss :: Generation
lwss = [(0,1),(0,2),(0,3),(0,7),(0,8),(0,9),(1,0),(1,3),(1,7),(1,10),(2,3),(2,7),(3,3),(3,7),(3,11),(4,0),(4,2),(4,7),(5,8),(5,10)]

five6P6H1V0 :: Generation
five6P6H1V0 = [(1,6),(1,7),(1,8),(1,19),(1,20),(1,21),(2,1),(2,2),(2,3),(2,5),(2,13),(2,14),(2,22),(2,24),(2,25),(2,26),(3,5),(3,9),(3,12),(3,15),(3,18),(3,22),(4,5),(4,11),(4,16),(4,22),(5,11),(5,12),(5,15),(5,16),(6,8),(6,12),(6,15),(6,19),(7,8),(7,10),(7,17),(7,19),(8,9),(8,10),(8,11),(8,12),(8,13),(8,14),(8,15),(8,16),(8,17),(8,18),(9,11),(9,16),(10,9),(10,18),(11,8),(11,19),(12,9),(12,18)]

one19P4H1V0 :: Generation
one19P4H1V0 = [(1,34),(2,17),(2,33),(2,35),(3,7),(3,9),(3,16),(3,22),(3,23),(3,32),(4,7),(4,12),(4,17),(4,19),(4,20),(4,21),(4,22),(4,23),(4,24),(4,29),(4,30),(5,7),(5,9),(5,10),(5,11),(5,12),(5,13),(5,14),(5,15),(5,16),(5,27),(5,30),(5,32),(5,33),(5,34),(6,10),(6,16),(6,24),(6,25),(6,26),(6,27),(6,32),(6,33),(6,34),(7,5),(7,6),(7,24),(7,25),(7,26),(7,28),(8,2),(8,5),(8,6),(8,14),(8,15),(8,24),(8,25),(9,2),(9,5),(10,1),(11,2),(11,5),(12,2),(12,5),(12,6),(12,14),(12,15),(12,24),(12,25),(13,5),(13,6),(13,24),(13,25),(13,26),(13,28),(14,10),(14,16),(14,24),(14,25),(14,26),(14,27),(14,32),(14,33),(14,34),(15,7),(15,9),(15,10),(15,11),(15,12),(15,13),(15,14),(15,15),(15,16),(15,27),(15,30),(15,32),(15,33),(15,34),(16,7),(16,12),(16,17),(16,19),(16,20),(16,21),(16,22),(16,23),(16,24),(16,29),(16,30),(17,7),(17,9),(17,16),(17,22),(17,23),(17,32),(18,17),(18,33),(18,35),(19,34)]

corderShip :: Generation
corderShip = [(1,33),(1,34),(1,36),(2,32),(2,33),(2,34),(2,36),(2,43),(2,45),(3,31),(3,36),(3,38),(3,43),(4,32),(4,33),(4,40),(4,42),(4,46),(5,33),(5,37),(5,40),(5,43),(5,44),(6,36),(6,38),(6,39),(6,43),(7,35),(7,37),(7,54),(7,55),(8,35),(8,37),(8,54),(8,55),(15,62),(15,63),(16,53),(16,54),(16,62),(16,63),(17,40),(17,50),(17,52),(17,53),(17,54),(17,55),(18,35),(18,39),(18,40),(18,41),(18,42),(18,43),(18,49),(18,50),(18,52),(18,56),(18,57),(19,34),(19,36),(19,44),(19,45),(19,50),(19,53),(19,54),(19,56),(19,57),(20,34),(20,42),(20,44),(20,45),(20,51),(20,52),(20,53),(20,54),(20,55),(20,56),(21,35),(21,44),(21,45),(21,52),(22,36),(22,40),(22,41),(22,42),(22,43),(23,41),(23,42),(23,43),(24,25),(24,27),(24,37),(24,38),(25,25),(25,27),(25,29),(25,36),(25,38),(26,24),(26,27),(26,28),(26,30),(26,35),(26,36),(27,25),(27,26),(27,30),(27,32),(27,34),(27,35),(27,37),(28,25),(28,26),(28,30),(28,31),(28,33),(28,34),(28,35),(28,36),(28,37),(29,29),(29,31),(29,32),(29,36),(29,37),(30,28),(30,30),(31,3),(31,4),(31,6),(32,2),(32,3),(32,4),(32,6),(32,13),(32,15),(33,1),(33,6),(33,8),(33,13),(34,2),(34,3),(34,10),(34,12),(34,16),(35,3),(35,7),(35,10),(35,13),(35,14),(35,26),(36,6),(36,8),(36,9),(36,13),(36,25),(36,26),(36,27),(37,5),(37,7),(37,25),(37,28),(38,5),(38,7),(38,24),(38,29),(39,25),(41,25),(41,28),(42,26),(42,28),(44,22),(45,21),(45,22),(45,23),(46,20),(46,21),(46,23),(46,24),(47,10),(47,19),(47,20),(47,22),(47,28),(48,5),(48,9),(48,10),(48,11),(48,12),(48,13),(48,18),(48,19),(48,26),(48,27),(49,4),(49,6),(49,14),(49,15),(49,18),(49,19),(49,27),(49,28),(50,4),(50,12),(50,14),(50,15),(51,5),(51,14),(51,15),(52,6),(52,10),(52,11),(52,12),(52,13),(53,11),(53,12),(53,13),(57,12),(57,13),(58,12),(58,13)]
</code></pre>
<p><em>Megjegyzés, a tesztesetekben szereplő generációk meglehet, hogy eltérő irányba haladnak az alábbi animációkhoz képest!</em></p>
<p>lwss, vagyis a Lightweight spaceship animálva:
<img src="https://conwaylife.com/w/images/2/21/Lwss.gif" alt="lwss"></p>
<p>five6P6H1V0 animálva:
<img src="https://conwaylife.com/w/images/3/3f/56p6h1v0.gif" alt="five6P6H1V0"></p>
<p>one19P4H1V0 animálva:
<img src="https://conwaylife.com/w/images/8/89/119p4h1v0.gif" alt="one19P4H1V0"></p>
<p>corderShip animálva:
<img src="http://ndsblnt.web.elte.hu/corder.gif" alt="corderShip"></p>
<h2>Alapfeladat</h2>
<h3>Szomszédos cellák</h3>
<p>Készítsünk egy függvényt, amely egy cella koordinátái alapján megadja a vele
szomszédos cellák koordinátáit! Mivel a tér végtelen, minden cellának pontosan
<code>8</code> szomszédja van. A szomszédokat olyan sorrendben adjuk meg, hogy sorokban
fentről lefelé, ezen belül pedig balról jobbra legyenek felsorolva.</p>
<pre><code class="language-hs">neighbors :: Coordinate -&gt; [Coordinate]
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">neighbors (1, 1) == [(0,0),(0,1),(0,2),(1,0),(1,2),(2,0),(2,1),(2,2)]
neighbors (0, 0) == [(-1,-1),(-1,0),(-1,1),(0,-1),(0,1),(1,-1),(1,0),(1,1)]
neighbors (100, -200) == [(99,-201),(99,-200),(99,-199),(100,-201),(100,-199),(101,-201),(101,-200),(101,-199)]
</code></pre>
<h3>Szomszédos élő sejtek</h3>
<p>Határozzuk meg egy generáció és egy cella alapján, hogy egy adott cellának hány
élő sejtet tartalmazó szomszédja van!</p>
<pre><code class="language-hs">livingNeighbors :: Generation -&gt; Coordinate -&gt; Int
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">livingNeighbors single (0, 0) == 0
livingNeighbors single (1, 1) == 0
livingNeighbors row (10, 0) == 1
livingNeighbors row (10, 1) == 1
livingNeighbors row (10, 2) == 2
livingNeighbors row (9, 2) == 3
</code></pre>
<h3>Szabályok kiszámítása</h3>
<p>A fent megadott szabályok alapján, az aktuális generációból kiindulva
határozzuk meg, hogy egy cella élő sejtet fog-e tartalmazni a következő
generációban/iterációban! Természetesen ehhez azt is figyelembe kell venni,
hogy az adott generációban van-e élő sejt!</p>
<pre><code class="language-hs">staysAlive :: Generation -&gt; Coordinate -&gt; Bool
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">not $ staysAlive single (1, 1)
not $ staysAlive row (10, 1)
staysAlive row (10, 2)
staysAlive row (9, 2)
not $ staysAlive column (9, 2)
staysAlive column (10, 2)
staysAlive column (10, 3)
</code></pre>
<h3>Szabályok alkalmazása élő sejtekre</h3>
<p>Alkalmazzuk a fenti szabályokat az aktuális generációra! Mivel egy generáció
csak az élő sejtek celláinak koordinátáit tárolja, ezért két dolog történhet
minden cellával: a benne lévő sejt vagy életben marad, vagy meghal.</p>
<pre><code class="language-hs">stepLivingCells :: Generation -&gt; Generation
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">sort (stepLivingCells row) == [(10,2)]
sort (stepLivingCells column) == [(10,2)]
sort (stepLivingCells glider) == [(7,5),(8,4),(8,5)]
sort (stepLivingCells caterer) == sort [(3,6),(3,7),(3,8),(4,2),(4,6),(7,4)]
</code></pre>
<h3>Élő sejtekkel szomszédos üres cellák</h3>
<p>Adjuk meg azon üres cellák listáját, amelynek az adott generációban van élő
sejtes szomszédja! Ügyeljünk rá, hogy minden cella csak egyszer szerepeljen!</p>
<pre><code class="language-hs">deadNeighbors :: Generation -&gt; [Coordinate]
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">sort (deadNeighbors single) == sort [(41,41),(41,42),(41,43),(42,41),(42,43),(43,41),(43,42),(43,43)]
sort (deadNeighbors row) == sort [(9,0),(9,1),(9,2),(10,0),(11,0),(11,1),(11,2),(9,3),(11,3),(9,4),(10,4),(11,4)]
sort (deadNeighbors column) == sort [(8,1),(8,2),(8,3),(9,1),(9,3),(10,1),(10,3),(11,1),(11,3),(12,1),(12,2),(12,3)]
sort (deadNeighbors glider) == sort [(5,4),(5,5),(5,6),(6,4),(6,6),(7,4),(7,6),(6,2),(6,3),(7,2),(8,2),(8,3),(8,6),(9,3),(9,4),(9,5),(9,6)]
sort (deadNeighbors caterer) == sort [(1,3),(1,4),(1,5),(2,3),(2,5),(3,3),(3,4),(3,5),(2,1),(2,2),(3,1),(4,1),(4,3),(2,6),(2,7),(4,5),(4,7),(2,8),(4,8),(2,9),(4,9),(2,10),(3,10),(4,10),(5,1),(5,3),(5,5),(5,6),(5,7),(6,1),(6,2),(6,3),(5,4),(6,4),(6,6),(7,5),(7,6),(7,2),(8,2),(8,3),(8,4),(8,5)]
</code></pre>
<h3>Szabály alkalmazása üres cellákra</h3>
<p>Alkalmazzuk a fenti szabályokat az aktuális generáció élő sejtjei körüli üres
cellákra! Természetesen ezeken a helyeken csak újabb sejtek születhetnek.</p>
<pre><code class="language-hs">stepDeadCells :: Generation -&gt; Generation
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">stepDeadCells single == []
sort (stepDeadCells row) == [(9,2),(11,2)]
sort (stepDeadCells column) == [(10,1),(10,3)]
sort (stepDeadCells glider) == [(6,4),(7,6)]
sort (stepDeadCells caterer) ==  sort [(3,3),(3,5),(4,1),(4,3),(2,7),(2,8),(4,8),(6,3),(6,4)]
</code></pre>
<h3>Új generáció kiszámítása</h3>
<p>Számítsuk ki a következő generációt az aktuális generációból! Ehhez a következő
lépéseket kell elvégezni:</p>
<ul>
<li>számítsuk ki, hogy az élő sejtek közül melyek maradnak életben,</li>
<li>határozzuk meg, hogy az élő sejtek körül mely cellákban születik új sejt,</li>
<li>vegyük az előző két pont eredményének unióját, majd rendezzük.</li>
</ul>
<pre><code class="language-hs">stepCells :: Generation -&gt; Generation
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">sort (stepCells single) == []
sort (stepCells row) == sort [(9,2),(10,2),(11,2)]
sort (stepCells column) == sort [(10,1),(10,2),(10,3)]
sort (stepCells glider) == sort [(6,4),(7,5),(7,6),(8,4),(8,5)]
sort (stepCells caterer) == sort [(2,7),(2,8),(3,3),(3,5),(3,6),(3,7),(3,8),(4,1),(4,2),(4,3),(4,6),(4,8),(6,3),(6,4),(7,4)]
sort (stepCells row) == column 
stepCells column == row 
sort ((stepCells . stepCells . stepCells) caterer) == caterer
sort (stepCells o15) == [(1,0),(1,1),(1,2),(2,0),(2,2),(3,0),(3,1),(3,2),(4,0),(4,1),(4,2),(5,0),(5,1),(5,2),(6,0),(6,1),(6,2),(7,0),(7,2),(8,0),(8,1),(8,2)]
sort (stepCells o312) == [(1,21),(1,22),(2,21),(2,22),(6,32),(6,33),(7,23),(7,24),(7,31),(7,34),(8,22),(8,23),(8,32),(8,33),(9,7),(9,23),(9,24),(10,6),(10,8),(11,6),(11,8),(12,7),(19,34),(19,36),(20,34),(20,35),(20,36),(21,1),(21,2),(21,35),(21,41),(21,42),(22,1),(22,2),(22,8),(22,41),(22,42),(23,7),(23,8),(23,9),(24,7),(24,9),(31,36),(32,35),(32,37),(33,35),(33,37),(34,19),(34,20),(34,36),(35,10),(35,11),(35,20),(35,21),(36,9),(36,12),(36,19),(36,20),(37,10),(37,11),(41,21),(41,22),(42,21),(42,22)]
sort (stepCells lwss) == [(-1,2),(-1,8),(0,1),(0,2),(0,3),(0,7),(0,8),(0,9),(1,1),(1,3),(1,4),(1,6),(1,7),(1,9),(2,2),(2,3),(2,4),(2,6),(2,7),(2,8),(3,2),(3,3),(3,6),(3,7),(3,8),(4,7),(4,8)]
sort (stepCells five6P6H1V0) == [(0,7),(0,20),(1,2),(1,6),(1,7),(1,20),(1,21),(1,25),(2,2),(2,4),(2,5),(2,7),(2,8),(2,13),(2,14),(2,19),(2,20),(2,22),(2,23),(2,25),(3,2),(3,5),(3,6),(3,12),(3,13),(3,14),(3,15),(3,21),(3,22),(3,25),(4,10),(4,11),(4,16),(4,17),(5,11),(5,12),(5,15),(5,16),(6,9),(6,12),(6,15),(6,18),(7,8),(7,10),(7,17),(7,19),(8,9),(8,12),(8,13),(8,14),(8,15),(8,18),(9,9),(9,11),(9,13),(9,14),(9,16),(9,18),(11,8),(11,9),(11,18),(11,19)]
sort (stepCells one19P4H1V0) == [(1,34),(2,33),(2,34),(3,8),(3,16),(3,17),(3,18),(3,20),(3,24),(4,6),(4,7),(4,9),(4,12),(4,14),(4,17),(4,20),(4,21),(4,24),(4,29),(4,30),(4,32),(5,8),(5,9),(5,10),(5,12),(5,13),(5,14),(5,15),(5,16),(5,17),(5,20),(5,21),(5,22),(5,27),(5,28),(5,29),(5,30),(5,32),(5,34),(6,6),(6,9),(6,10),(6,12),(6,13),(6,14),(6,16),(6,24),(6,28),(6,31),(6,32),(6,34),(7,5),(7,6),(7,15),(7,23),(7,33),(8,4),(8,24),(8,26),(9,1),(9,2),(9,5),(9,6),(10,1),(10,2),(11,1),(11,2),(11,5),(11,6),(12,4),(12,24),(12,26),(13,5),(13,6),(13,15),(13,23),(13,33),(14,6),(14,9),(14,10),(14,12),(14,13),(14,14),(14,16),(14,24),(14,28),(14,31),(14,32),(14,34),(15,8),(15,9),(15,10),(15,12),(15,13),(15,14),(15,15),(15,16),(15,17),(15,20),(15,21),(15,22),(15,27),(15,28),(15,29),(15,30),(15,32),(15,34),(16,6),(16,7),(16,9),(16,12),(16,14),(16,17),(16,20),(16,21),(16,24),(16,29),(16,30),(16,32),(17,8),(17,16),(17,17),(17,18),(17,20),(17,24),(18,33),(18,34),(19,34)]
sort (stepCells corderShip) == [(1,32),(1,34),(2,32),(2,34),(2,36),(2,44),(3,31),(3,34),(3,35),(3,37),(3,42),(3,43),(3,44),(4,32),(4,33),(4,37),(4,39),(4,41),(4,42),(4,44),(5,32),(5,33),(5,37),(5,38),(5,40),(5,41),(5,42),(5,43),(5,44),(6,36),(6,38),(6,39),(6,43),(6,44),(7,35),(7,37),(7,54),(7,55),(8,54),(8,55),(15,62),(15,63),(16,52),(16,55),(16,62),(16,63),(17,39),(17,40),(17,42),(17,49),(17,50),(17,52),(17,55),(17,56),(18,35),(18,39),(18,40),(18,41),(18,42),(18,43),(18,44),(18,49),(18,50),(18,52),(18,57),(19,34),(19,40),(19,45),(19,49),(19,50),(20,34),(20,46),(20,51),(20,56),(20,57),(21,35),(21,45),(21,51),(21,52),(21,54),(21,55),(22,40),(23,37),(23,40),(23,43),(24,28),(24,37),(24,38),(24,42),(25,24),(25,25),(25,27),(25,29),(25,35),(25,36),(25,38),(26,24),(26,27),(26,28),(26,30),(26,31),(26,34),(27,24),(27,30),(27,32),(27,37),(28,25),(28,26),(28,29),(28,38),(29,29),(29,32),(29,33),(29,34),(29,37),(30,29),(30,30),(30,31),(31,2),(31,4),(32,2),(32,4),(32,6),(32,14),(33,1),(33,4),(33,5),(33,7),(33,12),(33,13),(33,14),(34,2),(34,3),(34,7),(34,9),(34,11),(34,12),(34,14),(35,2),(35,3),(35,7),(35,8),(35,10),(35,11),(35,12),(35,13),(35,14),(35,25),(35,26),(35,27),(36,6),(36,8),(36,9),(36,13),(36,14),(36,25),(36,27),(37,5),(37,7),(37,24),(37,25),(37,27),(37,28),(38,24),(38,25),(41,27),(42,27),(44,21),(44,22),(44,23),(45,20),(45,24),(46,19),(46,24),(47,9),(47,10),(47,12),(47,18),(47,22),(47,23),(47,27),(48,5),(48,9),(48,10),(48,11),(48,12),(48,13),(48,14),(48,26),(49,4),(49,10),(49,15),(49,18),(49,19),(49,26),(49,27),(49,28),(50,4),(50,16),(51,5),(51,15),(52,10),(53,10),(53,13),(54,12),(57,12),(57,13),(58,12),(58,13)]
</code></pre>
<h3>Játék léptetése a megadott számú generációba</h3>
<p>Készítsük el azt a függvényt, amely egy kezdeti generációra elvégzi az adott számú
lépést! A kezdeti generációt vegyük a nulladiknak. Ha a megadott generáció száma
negatív, akkor térjünk vissza <code>Nothing</code>-al, küldönben <code>Just</code>-ba csomagolva adjuk
meg a megfelelő generációt!</p>
<p><em>A tesztesetek futtatásához szükséges a <code>Data.Maybe</code> module importálása!</em></p>
<pre><code class="language-hs">play :: Generation -&gt; Int -&gt; Maybe Generation
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">fmap sort (play single 0) == Just [(42,42)]
fmap sort (play single 1) == Just []
fmap sort (play single 2) == Just []
fmap sort (play single (-10)) == Nothing
fmap sort (play glider 4) == Just [(7,6),(8,4),(8,6),(9,5),(9,6)]
fmap sort (play glider 55) == Just [(20,17),(20,19),(21,18),(21,19),(22,18)]
column == (fromJust $ play row 1) 
row == sort (fromJust $ play row 2) 
row == sort (fromJust $ play column 1) 
column == sort (fromJust $ play column 2048) 
caterer == sort (fromJust $ play caterer 9)
</code></pre>
<h3>Stabil generációk detektálása</h3>
<p>Állapítsuk meg egy generációról, hogy stabil-e. Egy generáció akkor stabil,
ha a léptetése után változatlan marad!</p>
<pre><code class="language-hs">isStill :: Generation -&gt; Bool
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">not $ isStill single
isStill block
not $ isStill row
not $ isStill glider
not $ isStill caterer
</code></pre>
<h2>Extra feladatok</h2>
<p>A következő feladatok megoldása nem kötelező, viszont az itt szerzett
pontok hozzáadódnak a vizsga pontszámához (az átmenő pontszám
megszerzését követően). Ebben a részben összesen (1+2) azaz 3 pont szerezhető.</p>
<h3>Oszcillátorok detektálása (1 pont)</h3>
<p>Állapítsuk meg egy generációról, hogy oszcillátor-e, azaz visszatér-e önmagába
egy megadott lépésnyi távolságon belül. Ha igen, adjuk meg  azt a legkisebb
pozitív számot, ahány generáció múlva ez megtörténik <code>Just</code>-ba csomagolva.
Ha nem, adjunk vissza <code>Nothing</code>-ot.</p>
<pre><code class="language-hs">isOscillator :: Generation -&gt; Int -&gt; Maybe Int
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">isOscillator row 2 == Just 2
isOscillator column 10 == Just 2
isOscillator caterer 3 == Just 3
isOscillator caterer 100 == Just 3
isOscillator [] 1 == Just 1
isOscillator single 10 == Nothing
isOscillator row 1 == Nothing
isOscillator caterer 2 == Nothing
isOscillator glider 2 == Nothing
isOscillator o15 2 == Nothing
isOscillator o15 8 == Nothing
isOscillator o15 14 == Nothing
isOscillator o15 15 == Just 15
isOscillator o15 66 == Just 15
isOscillator o15 5000 == Just 15
isOscillator o312 311 == Nothing
isOscillator o312 312 == Just 312
</code></pre>
<h3>Űrhajók detektálása (2 pont)</h3>
<p>Állapítsuk meg egy generációról, hogy űrhajó-e. Egy generáció akkor űrhajó, ha
felveszi újra a kiindulási formáját egy megadott lépésen belül és nem önmagába
tér vissza. Ha űrhajó, adjuk meg irányvektorok formájában azt a legkisebb távolságot,
amely az azonos formák eltolódásából keletkezett. Amennyiben nem veszi fel a
kiindulási formát egyszer sem vagy önmagába tér vissza, adjunk vissza <code>Nothing</code>-ot.</p>
<pre><code class="language-hs">isSpaceShip :: Generation -&gt; Int -&gt; Maybe (Integer, Integer)
</code></pre>
<p><em>Az alábbi tesztesetek közül mindegyiknek <code>True</code>-t kell adnia:</em></p>
<pre><code class="language-hs">isSpaceShip glider 8 == Just (1,1)
isSpaceShip row 2 == Nothing
isSpaceShip column 10 == Nothing
isSpaceShip caterer 3 == Nothing
isSpaceShip caterer 100 == Nothing
isSpaceShip [] 1 == Nothing
isSpaceShip single 10 == Nothing
isSpaceShip row 1 == Nothing
isSpaceShip caterer 2 == Nothing
isSpaceShip glider 2 == Nothing
isSpaceShip o15 2 == Nothing
isSpaceShip o15 8 == Nothing
isSpaceShip o15 14 == Nothing
isSpaceShip o15 15 == Nothing
isSpaceShip o15 66 == Nothing
isSpaceShip o15 5000 == Nothing
isSpaceShip lwss 4 == Just (-2,0)
isSpaceShip lwss 6 == Just (-2,0)
isSpaceShip lwss 500 == Just (-2,0)
isSpaceShip lwss 0 == Nothing
isSpaceShip lwss 1 == Nothing
isSpaceShip lwss 2 == Nothing
isSpaceShip lwss 3 == Nothing
isSpaceShip five6P6H1V0  7 == Just (-1,0)
isSpaceShip one19P4H1V0 4 == Just (0,-1)
isSpaceShip one19P4H1V0 3 == Nothing
isSpaceShip corderShip 96 == Just (-8,-8)
</code></pre>
