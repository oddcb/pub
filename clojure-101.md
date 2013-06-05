# Clojure 101

## Hva er Clojure

Clojure er et Lisp som kjører på JVMen har god interop med andre språk på samme plattform. Siden Clojure er et hosted språk finnes det også et subset av Clojure, Clojurescript, som kan kjøre på en Javascript-plattform; f.eks. V8 eller rett i nettleseren. 

Clojure er et kompilert språk og tolkes ikke ved kjøring. 

## Hva og hvorfor

### Få byggesteiner

I Clojure vil han heller ha mange funksjoner som opererer på få datastrukturer kontra mange datastrukturer med få funksjoner. Det gjør at de samme datastrukturene går igjen i hele bruken av språket, og at man dermed må lære seg vesentlig mindre kontra en kodebase med kanskje hundrevis av datastrukturer med tilhørende metoder.

### Immutability

I praksis er alt i Clojure immutable. Funksjoner produserer verdier som konsumeres av andre funksjoner etc. Det må gjøres en ekstra innsats for å mutere tilstand. 

### Konsist

Måten Clojure (og for den saks skyld sikkert de fleste andre lisper) er bygd opp på gjør at man kan uttrykke veldig mye mer på mye mindre kode enn f.eks. Java. Nå vil nok mange av eksemplene man finner på clojure-kode sikkert kunne la seg uttrykke veldig konsist i språk som Ruby og Python, eller Scala for den sakens skyld og.. 

Eksempel hentet fra StringUtils i Apache Commons: 


```java
public static boolean isBlank(String str) {
    int strLen;
    if (str == null || (strLen = str.length()) == 0) {
        return true;
    }
    for (int i = 0; i < strLen; i++) {
        if ((Character.isWhitespace(str.charAt(i)) == false)) {
            return false;
        }
    }
    return true;
} 
```

Vis-a-vis Clojure: 

```clojure
(defn blank? [s]
  (every? #(Character/isWhiteSpace %) s))
```

Hvordan er dette mulig? Vel, man kan f.eks. tenke på en streng som en sekvens av karakterer. Dermed forsvinner endel spesial-caser (de som håndteres først i Java-koden).

## Homoikonisk

Et Clojure-program er homoikonisk. Dvs. at kode er representert som data og data som kode.

## Form og oppbygning

sexprs = symbolic expression

Et Clojure-program består av _former_. Forenklet noe som kan evalueres og gi et resultat. Dermed er: 
```clojure
(1 2 3)
``` 
ikke en form siden det ikke kan evalueres, mens: 
```clojure
(+ 1 2 3) 
```
er en form siden vi har en funksjon i første posisjon i lista. Sånnsett kan man si at alle funksjoner man definerer er en _form_ i et Lisp. 

### Spesialformer

For å kunne bygge opp et faktisk språk må det nødvendigvis finnes noen spesial-former. Det som gjør disse spesielle er at de er bygd inn i Clojure selv og er byggeklossene for resten av bibliotekene som finnes og det brukeren gjør. Eksempler på spesial-former: _if_, _def_  _declare_, _ns_, _+_, _-_ og mange flere. 

Grunnen til at man trenger disse er at om f.eks. ikke +, - eller if var definert ville det vært veldig vanskelig å gjøre noe fornuftig med språket. 

## Variable

Variable i Clojure er ikke variable som vi vanligvis tenker om variable. Variable er pekere på verdier som stort sett er immutable. 

Gitt f.eks. dette i Java: 

```java
public class Muter {
    public Integer a = 10;
    public Integer b = a;

    public void operasjon() {
        System.out.println(b);
    }

    public static void main(String args []) {
        Muter it = new Muter();
        it.b = 100;
        it.operasjon();
    }
}
```

... Fjernt eksempel ... 

I Clojure: variable defineres ved å gjøre en binding mellom et symbol og en verdi (som kan være alt fra ett tall til en funksjon osv.): 

```clojure
(def a 1337) ; (println a) => 1

;; redefinering av a

(def a (+ 2 2)) ; (println a) => 1

```

La oss si at a brukes som input til to funksjoner som kjører i parallel. _a_ er bare en verdi som sendes inn i funksjonene, dvs. at om den ene funksjonen skulle redefinere _a_ innenfor sitt eget scope ville ikke det føre til at _a_ i den andre funksjonen blir påvirket. 

### let

_let_ er en spesialform som brukes for å binde verdier til navn. _let_ tar en _vector_ og et antall uttrykk som argumenter:

```clojure
(let [a (+ 2 2)
      b "hei "] 
      (println (str b a)) ; => "hei 4)
    )    
```

Typisk bruk er for å binde opp lokale variable som har kommet inn som argument til en funksjon: 

```clojure
(defn funk [arg]
    (let [x (+ 2 arg)]
        (println x) ; (funk 40) => 42
    )
)
```

## Funksjoner

En funksjon kan defineres ved: 

```clojure
(def  (fn funk [] + 2 2)))
```

men den mer vanlige måten å gjøre det på er å bruke sukkeret _defn_: 
```clojure
(defn funk [] (+ 1 2))
```

Anonyme funksjoner kan defineres ved: 
```clojure
#(println %1)
```
Her angir %1 første argument (kan forenkles til %), %2 2. argument osv. Typisk bruk av anonym funksjon kan være sammen med filter: 

```clojure
(filter #(> % 2) [1 2 3 4 5])
```
## Seqs

En grunnstein måten man jobber med data på i Clojure er _Seqs_. Seqs er grensesnitt for å vise at at noe er en sekvens. "Fetteren" i Java-verdenen er en iterator, men denne kan muteres. Det kan ikke en seq.

De typiske datastrukturene man kan behandle som en seq er _list_ og _vector_. 

### List
```clojure
(def a '(1 2 3))
```

En liste er den samme datastrukturen som man uttrykket er clojure-program i. Legg merke til _'_ i kodesnutten over. ', som er sukker for funksjonen quote, angir at man ikke skal evaluere listen som vanlig i Clojure, men rett frem som en liste med data.

### Vector
```clojure
(def a [1 2 3])
```
En vektor er en indeksert samling av data. Dvs. nær det samme som en liste, men man kan gjøre operasjoner basert på posisjonen i den uten at det koster allverdens. Vektorer er også det som brukes for angi argument-listen i funksjonene man skaper: 

```clojure
(defn foo [bar]
  (* 10 bar))
```

### Andre collections

#### Set
```clojure 
(def a #{a b c})
```
#### Map
```clojure
(def a {:a 1 :b 2}) 
```

## Destructuring

En veldig kraftig egenskap ved Clojure er det som kalles _destructuring_. Kort fortalt en måte å bryte opp maps og sekvenser på i f.eks. _let_ og argumenter til _fn_, som bindes til lokale variable. I en funksjon er dette du typisk vil bruke _let_ til. 

Foreksempel med _let_:


```clojure
(def v [100 200 300 400 500])


(defn let-non-destruct [args]
    (let [x (first args)
          y (nth args 1)]
          (println (+ x y)) ; 300
    )
)

```

Med destructuring kan vi f.eks. uttrykke det samme med: 
```clojure
(defn fn-destruct [[x y]]
    (println (+ x y)) ;300
)

```

### Destructuring av sekvenser

```clojure
(def foo [2 40])

(let [[x y] foo]
    (println (+ x y)) ; 42
)
```

Man kan også hoppe over posisjoner i en sekvens med _: 

```clojure
(def lang-liste [100 2 40 99 250])

(defn process [[x _ _ _ y]]
    (println (str (= (+ x y) 350)))
)
```

Om man vil ta med resten av argumentene kan man bruke _& rest_: 

```clojure
(defn process-rest [[x y & rest]]
 (println rest) ; [40 99 250]
)
```
og for å ha alle argumentene tilgjengelig kan man bruke nøkkelordet _:as_: 

```clojure
(defn process-all [[x y :as alle]]
 (println alle) ; [100 2 40 99 250])
)
```

Kombinert: 

```clojure
(defn process-it [[x _ y & rest :as all]]
    (println (str x y))
    (println rest)
    (println all)
)
```

### Destructuring av maps

```clojure
(def some-map {:some 100, :important 200, :values 99})

(defn process-map [{a :some b :important}]
    (println (+ a b))
)

```

Om man vil bruke de samme argumentene som kom inn med map-et kan man bruke _:keys_:

```clojure
(defn process-map [{:keys [some important values]}]
    (println (+ some important values))
)
```

Man kan i tillegg også nøste seg inn videre i både sekvenser og maps: 

```clojure
(TODO)
```

## Lazy

En annen grunnstein i Clojure er at så mye som mulig er gjort _lazy_. Hva ligger så i dette? Vel, la oss si at man leser en sekvens av tall, eller hva som helst annet for den sakens skyld, så er det praktisk at man ikke leser inn mer enn man akkurat har behov for. Man er "lat" og gjør ikke mer arbeid enn man trenger, dvs. man leser ikke inn hele fila, men bolk for bolk ift. hvor mye data konsumenten trenger. 

## Eksempler

### isBlank

### join 

Fra Apache Commons StringUtils:

```java
    
public static String join(Iterator iterator, char separator) {
    // handle null, zero and one elements before building a buffer
    if (iterator == null) {
        return null;
    }
    if (!iterator.hasNext()) {
        return EMPTY;
    }
    Object first = iterator.next();
    if (!iterator.hasNext()) {
        return ObjectUtils.toString(first);
    }

    // two or more elements
    StrBuilder buf = new StrBuilder(256); // Java default is 16, probably too small
    if (first != null) {
        buf.append(first);
    }

    while (iterator.hasNext()) {
        buf.append(separator);
        Object obj = iterator.next();
        if (obj != null) {
            buf.append(obj);
        }
    }

    return buf.toString();
}
```

i Clojure: 
```clojure
(defn joiner [sek, sep]
  (apply str ; 2. str konkanterer når den får inn flere argumenterer. apply brukes for å ... TODO
    (interpose sep sek)) ; 1. sett sep mellom alle verdiene i seq. Gir tilbake en kommaseparert liste => ("hei" "," "på" "," "deg"
  )
```

## Oppgaver

1. installer leiningen etc. 
2. gitt sekvensen [1 2 3 3 3 99 20 1001 2000] finn alle partall og oddetall i denne (if odd even) - lag to separate funksjoner
3. bruk funksjonene over og returner resultatet som to separate sekvenser i et map med nøkkelordene :odd og :even. Bruk loop og recur. Eks {:even [2 20 2000] :odd [3 3 3 99]}
4. gitt sekvensen [1 2 3 3 99 20] beregn snittet og medianen (reduce). Lag to separate funksjoner
5. les inn filen a.txt og skriv innholdet til skjermen
6. les inn filen varsel.xml (http://www.yr.no/sted/Norge/Oslo/Oslo/Vippetangen/varsel.xml) og finn ut følgende: alle dager med temperaturvarsel over 20 grader og "Klarvær". Bruk xml-seq og destructuring med nøsting
