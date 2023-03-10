
# TTP: Obfuscate Javascript

L'intento di questo materiale è creare una guida per offuscare codice javascript, attraverso metodologie che personalmente non ho visto, facendo analisi di malware in javascript. Discostandomi da programmi che sfruttano una logica e processi già noti. 
Al interno troverete delle sezioni dedicate con esempio o funzioni gia testate. 
Tutto ciò ancora in fase di sviluppo. 



# Theory with console.log






Uso comune di un console.log

```javascript
  console.log("Hello!")
```

- la funzione  -> console
- il metodo    -> log 
- i parametri  ->("Hello!")


La scrittura del metodo cambia.

```javascript
  console["log"]("Hello!")
```

Quindi possiamo definire che le parentesi quadre permettono l'attivazione dei metodi di una funzione.


Ora rendiamo chiaro e offuschiamo realmente il codice per stampre un console.log("Hello")

```javascript
    d = ['\x48\x65\x6c\x6c\x6f','\x6c\x6f\x67']

    a = () =>{
      return d[0]
    }

    b = () => {
      return console
    }

    c = () => {
      return d[1]
    }

    b()[c()](a())
```

Andiamo ad analizzare ogni pezzo di codice 

```javascript
    d = ['\x48\x65\x6c\x6c\x6f','\x6c\x6f\x67']
```
Abbiamo un array con due elementi in hexcode, il primo equivale ad Hello, il secondo a log 

```javascript
  a = () =>{
    return d[0]
  }
```

La funzione a ci ritorna l'elemento in posizione 0 dell' array d 


```javascript
  b = () =>{
    return console
  }
```

La funzione b ci restituisce la funzione console 

```javascript
c = () => {
      return d[1]
    }
```
La funzione c ci ritorna l'elemento in poszione 1 dell'array d


```javascript
  b()[c()](a())
```
Concateniamo tutto ottenedo un riga di codice poco chiara e poco leggibile 


L'esempio fatto al momento è molto sempliche, ma immaginate di avere un codice composto cosi di mille righe.


# Boolean operators

Ora vi riporto un po di confronti booleani che rendono complessa la lettura. 

```javascript

  //NULL
  !(~null) -> false
  (~null) -> true
  !!(~null) -> true
  (null) -> false
  !(null) -> true

  //NaN

  (~NaN) -> true
  !(~NaN) -> false
  !!(~NaN) -> true
  !(NaN) -> true
  (~NaN) -> false

  //UNDEFINED
  (~undefined) -> true
  !(~undefined) -> false
  !!(~undefined) -> true
  !(undefined) -> true
  (undefined) -> false

  //Altri tipo 
  ![] -> false
  !![] -> true
  
  !''??null -> true
  !''??'' -> true
  !''??!'' -> true
  !''??!!'' -> true
```

Esempio pratico

```javascript
   d = ['\x48\x65\x6c\x6c\x6f','\x6c\x6f\x67','\x62\x61\x69\x74']

   a = () =>{
     return d[0]
   }

   b = () => {
     return console
   }

   c = () => {
     return d[1]
   }

   mm = (l) => {
     return b()[c()](l)
   }

   k = (s, l) => {
     if((typeof(l) == 'string') === !(~NaN)){
       while (s) {
             mm(l)
         }
     }else{
         while (s) {
             b()[c()](a())
         }
     }

   }

   k(!''??null,d[2])
```

Crea un loop dove stampa solo Hello.

Analizzando il codice al suo interno c'è un bait all'interno del if.
Una best practice sarebbe quella di non far scattare mai if ma far scattare solo else.



