---
title: Espressioni di stile basate sui dati in Azure Maps Web SDK | Mappe Microsoft Azure
description: Informazioni sulle espressioni di stile basate sui dati. Per modificare gli stili nelle mappe, vedere come usare queste espressioni in Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680067"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Espressioni di stile basate sui dati (SDK Web)

Le espressioni consentono di applicare la logica di business alle opzioni di stile che osservano le proprietà definite in ogni forma di un'origine dati. Le espressioni possono filtrare i dati in un'origine dati o in un livello. Le espressioni possono essere costituite da logica condizionale, ad esempio If-Statements. E possono essere usati per modificare i dati usando gli operatori di stringa, gli operatori logici e gli operatori matematici.

Gli stili basati sui dati consentono di ridurre la quantità di codice necessario per implementare la logica di business per lo stile. Quando vengono utilizzati con i livelli, le espressioni vengono valutate in fase di rendering in un thread separato. Questa funzionalità garantisce un miglioramento delle prestazioni rispetto alla valutazione della logica di business nel thread dell'interfaccia utente.

Questo video offre una panoramica dello stile basato sui dati in Azure Maps Web SDK.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

Le espressioni sono rappresentate come matrici JSON. Il primo elemento di un'espressione nella matrice è una stringa che specifica il nome dell'operatore di espressione. Ad esempio, "+" o "case". Gli elementi successivi (se presenti) sono gli argomenti dell'espressione. Ogni argomento è un valore letterale (stringa, numero, valore booleano o `null` ) o un'altra matrice di espressioni. Lo pseudocodice seguente definisce la struttura di base di un'espressione. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK supporta molti tipi di espressioni. Le espressioni possono essere utilizzate autonomamente o in combinazione con altre espressioni.

| Tipo di espressioni | Descrizione |
|---------------------|-------------|
| [Espressione di aggregazione](#aggregate-expression) | Espressione che definisce un calcolo elaborato su un set di dati e che può essere utilizzato con l' `clusterProperties` opzione di un oggetto `DataSource` . |
| [Espressioni booleane](#boolean-expressions) | Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani. |
| [Espressioni colore](#color-expressions) | Le espressioni colore semplificano la creazione e la modifica dei valori dei colori. |
| [Espressioni condizionali](#conditional-expressions) | Le espressioni condizionali forniscono operazioni logiche simili a If-Statements. |
| [Espressioni di dati](#data-expressions) | Consente di accedere ai dati delle proprietà in una funzionalità. |
| [Espressioni interpolate e Step](#interpolate-and-step-expressions) | Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. |
| [Espressioni specifiche del livello](#layer-specific-expressions) | Espressioni speciali applicabili solo a un singolo livello. |
| [Espressioni matematiche](#math-expressions) | Fornisce gli operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni. |
| [Espressioni operatore stringa](#string-operator-expressions) | Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. |
| [Espressioni di tipo](#type-expressions) | Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani. |
| [Espressioni di associazione variabili](#variable-binding-expressions) | Le espressioni di associazione variabili consentono di archiviare i risultati di un calcolo in una variabile e di farvi riferimento altrove in un'espressione più volte senza dover ricalcolare il valore archiviato. |
| [Espressione zoom](#zoom-expression) | Recupera il livello di zoom corrente della mappa in fase di rendering. |

Tutti gli esempi in questo documento utilizzano la seguente funzionalità per illustrare i diversi modi in cui è possibile utilizzare i diversi tipi di espressioni. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>Espressioni di dati

Le espressioni di dati consentono di accedere ai dati delle proprietà di una funzionalità. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['at', number, array]` | Valore | Recupera un elemento da una matrice. |
| `['geometry-type']` | string | Ottiene il tipo di geometria della funzionalità: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | Valore | Ottiene il valore della proprietà dalle proprietà della funzionalità corrente. Restituisce null se la proprietà richiesta è mancante. |
| `['get', string, object]` | Valore | Ottiene il valore della proprietà dalle proprietà dell'oggetto specificato. Restituisce null se la proprietà richiesta è mancante. |
| `['has', string]` | boolean | Determina se le proprietà di una funzionalità dispongono della proprietà specificata. |
| `['has', string, object]` | boolean | Determina se le proprietà dell'oggetto dispongono della proprietà specificata. |
| `['id']` | Valore | Ottiene l'ID della funzionalità se ne è presente uno. |
| `['in', boolean | string | number, array]` | boolean | Determina se un elemento esiste in una matrice |
| `['in', substring, string]` | boolean | Determina se una sottostringa esiste in una stringa |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | d'acquisto | Restituisce la prima posizione in cui è possibile trovare un elemento in una matrice o una sottostringa in una stringa o `-1` se l'input non viene trovato. Accetta un indice facoltativo da cui iniziare la ricerca. |
| `['length', string | array]` | d'acquisto | Ottiene la lunghezza di una stringa o di una matrice. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | matrice di stringhe \| | Restituisce un elemento da una matrice o da una sottostringa da una stringa da un indice iniziale specificato o tra un indice iniziale e un indice finale se impostato. Il valore restituito è incluso nell'indice iniziale ma non nell'indice finale. |

**esempi**

È possibile accedere alle proprietà di una funzionalità direttamente in un'espressione tramite un' `get` espressione. In questo esempio viene utilizzato il `zoneColor` valore della funzionalità per specificare la proprietà Color di un livello Bubble. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

L'esempio precedente funzionerà correttamente, se tutte le funzionalità punto hanno la `zoneColor` Proprietà. In caso contrario, il colore sarà probabilmente il "nero". Per modificare il colore di fallback, utilizzare un' `case` espressione in combinazione con l' `has` espressione per verificare se la proprietà esiste. Se la proprietà non esiste, restituisce un colore di fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Per impostazione predefinita, i livelli Bubble e Symbol eseguiranno il rendering delle coordinate di tutte le forme in un'origine dati. Questo comportamento può evidenziare i vertici di un poligono o di una linea. L' `filter` opzione del livello può essere usata per limitare il tipo di geometria delle funzionalità di cui esegue il rendering, usando un' `['geometry-type']` espressione in un'espressione booleana. Nell'esempio seguente viene limitato un livello Bubble in modo che `Point` venga eseguito il rendering solo delle funzionalità.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

L'esempio seguente consente di eseguire il `Point` `MultiPoint` rendering di entrambe le funzionalità e. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Analogamente, la struttura dei poligoni viene sottoposta a rendering nei livelli linea. Per disabilitare questo comportamento in un livello linea, aggiungere un filtro che consenta solo `LineString` le `MultiLineString` funzionalità e.  

Di seguito sono riportati alcuni esempi aggiuntivi di come usare le espressioni di dati:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Espressioni matematiche

Le espressioni matematiche forniscono operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['+', number, number, …]` | d'acquisto | Calcola la somma dei numeri specificati. |
| `['-', number]` | d'acquisto | Sottrae 0 per il numero specificato. |
| `['-', number, number]` | d'acquisto | Sottrae i primi numeri per il secondo numero. |
| `['*', number, number, …]` | d'acquisto | Moltiplica i numeri specificati insieme. |
| `['/', number, number]` | d'acquisto | Divide il primo numero per il secondo numero. |
| `['%', number, number]` | d'acquisto | Calcola il resto quando si divide il primo numero per il secondo numero. |
| `['^', number, number]` | d'acquisto | Calcola il valore del primo valore elevato alla potenza del secondo numero. |
| `['abs', number]` | d'acquisto | Calcola il valore assoluto del numero specificato. |
| `['acos', number]` | d'acquisto | Calcola l'arcoseno del numero specificato. |
| `['asin', number]` | d'acquisto | Calcola l'arcoseno del numero specificato. |
| `['atan', number]` | d'acquisto | Calcola l'arcotangente del numero specificato. |
| `['ceil', number]` | d'acquisto | Arrotonda il numero per eccesso al numero intero successivo. |
| `['cos', number]` | d'acquisto | Calcola il coseno del numero specificato. |
| `['e']` | d'acquisto | Restituisce la costante matematica `e` . |
| `['floor', number]` | d'acquisto | Arrotonda il numero per difetto all'intero intero precedente. |
| `['ln', number]` | d'acquisto | Calcola il logaritmo naturale del numero specificato. |
| `['ln2']` | d'acquisto | Restituisce la costante matematica `ln(2)` . |
| `['log10', number]` | d'acquisto | Calcola il logaritmo in base 10 del numero specificato. |
| `['log2', number]` | d'acquisto | Calcola il logaritmo in base due del numero specificato. |
| `['max', number, number, …]` | d'acquisto | Calcola il numero massimo nel set di numeri specificato. |
| `['min', number, number, …]` | d'acquisto | Calcola il numero minimo nel set di numeri specificato. |
| `['pi']` | d'acquisto | Restituisce la costante matematica `PI` . |
| `['round', number]` | d'acquisto | Arrotonda il numero all'intero più vicino. I valori a metà vengono arrotondati per eccesso da zero. Ad esempio, `['round', -1.5]` restituisce `-2` . |
| `['sin', number]` | d'acquisto | Calcola il seno del numero specificato. |
| `['sqrt', number]` | d'acquisto | Calcola la radice quadrata del numero specificato. |
| `['tan', number]` | d'acquisto | Calcola la tangente del numero specificato. |

## <a name="aggregate-expression"></a>Espressione di aggregazione

Un'espressione di aggregazione definisce un calcolo elaborato su un set di dati e può essere utilizzato con l' `clusterProperties` opzione di un oggetto `DataSource` . L'output di queste espressioni deve essere un numero o un valore booleano. 

Un'espressione di aggregazione accetta tre valori: un valore di operatore e un valore iniziale e un'espressione per recuperare una proprietà da ogni funzionalità di un dato a cui applicare l'operazione di aggregazione. Questa espressione ha il formato seguente:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- Operator: funzione di espressione a cui viene quindi applicato rispetto a tutti i valori calcolati da `mapExpression` per ogni punto del cluster. Operatori supportati: 
    - Per i numeri: `+` , `*` , `max` , `min`
    - Per i valori booleani: `all` , `any`
- initialValue: valore iniziale in cui viene aggregato il primo valore calcolato.
- mapExpression: espressione applicata a ogni punto del set di dati.

**esempi**

Se tutte le funzionalità di un set di dati dispongono di una `revenue` proprietà, che è un numero. Quindi, è possibile calcolare i ricavi totali di tutti i punti di un cluster creati dal set di dati. Questo calcolo viene eseguito utilizzando l'espressione di aggregazione seguente: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Espressione accumulata

L' `accumulated` espressione ottiene il valore di una proprietà cluster accumulata finora. Può essere utilizzato solo nell' `clusterProperties` opzione di un' `DataSource` origine cluster.

**Utilizzo**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Espressioni booleane

Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani.

Quando si confrontano i valori, il confronto è fortemente tipizzato. I valori di tipi diversi sono sempre considerati non uguali. I casi in cui i tipi sono noti come diversi in fase di analisi sono considerati non validi e genereranno un errore di analisi. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Negazione logica. Restituisce `true` se l'input è `false` e `false` se l'input è `true` . |
| `['!=', value, value]` | boolean | Restituisce `true` se i valori di input non sono uguali; `false` in caso contrario,. |
| `['<', value, value]` | boolean | Restituisce `true` se il primo input è strettamente inferiore al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['<=', value, value]` | boolean | Restituisce `true` se il primo input è minore o uguale al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['==', value, value]` | boolean | Restituisce `true` se i valori di input sono uguali; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['>', value, value]` | boolean | Restituisce `true` se il primo input è rigorosamente maggiore della seconda; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['>=' value, value]` | boolean | Restituisce `true` se il primo input è maggiore o uguale al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['all', boolean, boolean, …]` | boolean | Restituisce `true` se tutti gli input sono `true` ; `false` in caso contrario,. |
| `['any', boolean, boolean, …]` | boolean | Restituisce `true` se uno degli input è `true` ; `false` in caso contrario,. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | Restituisce `true` se la funzionalità valutata è completamente contenuta all'interno di un limite della geometria di input; in caso contrario, false. Il valore di input può essere un oggetto GeoJSON valido di tipo `Polygon` , `MultiPolygon` , `Feature` o `FeatureCollection` . Funzionalità supportate per la valutazione:<br/><br/>-Point: restituisce `false` se un punto si trova sul limite o non è compreso nel limite.<br/>-LineString: restituisce `false` se una parte di una riga non rientra nel limite, la riga interseca il limite oppure l'endpoint di una riga si trova sul limite. |

## <a name="conditional-expressions"></a>Espressioni condizionali

Le espressioni condizionali forniscono operazioni logiche simili a If-Statements.

Le espressioni seguenti eseguono operazioni di logica condizionale sui dati di input. Ad esempio, l' `case` espressione fornisce la logica "if/then/else" mentre l' `match` espressione è simile a "switch-Statement". 

### <a name="case-expression"></a>Espressione case

Un' `case` espressione è un tipo di espressione condizionale che fornisce la logica "if/then/else". Questo tipo di espressione segue un elenco di condizioni booleane. Restituisce il valore di output della prima condizione booleana per restituire true.

Lo pseudocodice seguente definisce la struttura dell' `case` espressione. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Esempio**

Nell'esempio seguente vengono illustrate le diverse condizioni booleane fino a quando non ne viene trovata una che restituisce `true` , quindi viene restituito il valore associato. Se nessuna condizione booleana restituisce `true` , verrà restituito un valore di fallback. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Espressione di corrispondenza

Un' `match` espressione è un tipo di espressione condizionale che fornisce un'istruzione switch come la logica. L'input può essere qualsiasi espressione, ad esempio, `['get', 'entityType']` che restituisce una stringa o un numero. Ogni etichetta deve essere un singolo valore letterale o una matrice di valori letterali, i cui valori devono essere costituiti da tutte le stringhe o da tutti i numeri. L'input corrisponde a se uno dei valori nella matrice corrisponde. Ogni etichetta deve essere univoca. Se il tipo di input non corrisponde al tipo delle etichette, il risultato sarà il valore di fallback.

Lo pseudocodice seguente definisce la struttura dell' `match` espressione. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**esempi**

Nell'esempio seguente viene analizzata la `entityType` proprietà di una funzionalità punto in un livello a bolle per cercare una corrispondenza. Se viene trovata una corrispondenza, viene restituito il valore specificato o viene restituito il valore di fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Nell'esempio seguente viene utilizzata una matrice per elencare un set di etichette che devono restituire tutti lo stesso valore. Questo approccio è molto più efficiente rispetto all'inserimento di ogni etichetta singolarmente. In questo caso, se la `entityType` proprietà è "Restaurant" o "grocery_store", verrà restituito il colore "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Espressione COALESCE

Un' `coalesce` espressione scorre un set di espressioni fino a quando non viene ottenuto il primo valore non null e restituisce tale valore. 

Lo pseudocodice seguente definisce la struttura dell' `coalesce` espressione. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Esempio**

Nell'esempio seguente viene utilizzata un' `coalesce` espressione per impostare l' `textField` opzione di un livello di simbolo. Se la `title` proprietà non è presente nella funzionalità o è impostata su `null` , l'espressione tenterà di cercare la `subTitle` proprietà, se manca o, eseguirà il `null` fallback a una stringa vuota. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

Nell'esempio seguente viene utilizzata un' `coalesce` espressione per recuperare la prima icona di immagine disponibile disponibile nello sprite della mappa da un elenco di nomi di immagine specificati.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Espressioni di tipo

Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Oggetto [] | Dichiara che l'input è una matrice. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | Asserisce che il valore di input è un valore booleano. Se vengono specificati più valori, ognuno viene valutato in ordine fino a quando non viene ottenuto un valore booleano. Se nessuno degli input è un valore booleano, l'espressione è un errore. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | Collator | Restituisce un oggetto COLLATE da utilizzare nelle operazioni di confronto dipendenti dalle impostazioni locali. Per impostazione predefinita, le opzioni distinzione maiuscole/minuscole e con distinzione diacritici sono false. Nell'argomento locale viene specificato il tag di lingua IETF delle impostazioni locali da utilizzare. Se non viene specificato alcun valore, vengono utilizzate le impostazioni locali predefinite. Se le impostazioni locali richieste non sono disponibili, l'utilità di confronto utilizzerà impostazioni locali di fallback definite dal sistema. Utilizzare risolto-impostazioni locali per verificare i risultati del comportamento di fallback delle impostazioni locali. |
| `['literal', array]`<br/><br/>`['literal', object]` | \|oggetto Array | Restituisce una matrice di valori letterali o un valore dell'oggetto. Utilizzare questa espressione per impedire la valutazione di una matrice o di un oggetto come espressione. Questa operazione è necessaria quando un'espressione deve restituire una matrice o un oggetto. |
| `['image', string]` | string | Verifica se un ID immagine specificato viene caricato nello sprite dell'immagine maps. Se è, viene restituito l'ID; in caso contrario, viene restituito null. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | d'acquisto | Asserisce che il valore di input è un numero. Se vengono specificati più valori, ognuno viene valutato in ordine fino a quando non viene ottenuto un numero. Se nessuno degli input è costituito da numeri, l'espressione è un errore. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Oggetto | Asserisce che il valore di input è un oggetto.  Se vengono specificati più valori, ognuno viene valutato in ordine fino a quando non viene ottenuto un oggetto. Se nessuno degli input è un oggetto, l'espressione è un errore. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | string | Asserisce che il valore di input è una stringa. Se vengono specificati più valori, ognuno viene valutato in ordine fino a quando non viene ottenuta una stringa. Se nessuno degli input è costituito da stringhe, l'espressione è un errore. |
| `['to-boolean', value]` | boolean | Converte il valore di input in un valore booleano. Il risultato è `false` quando l'input è una stringa vuota,,, `0` `false` `null` o `NaN` ; in caso contrario, `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte il valore di input in un colore. Se vengono specificati più valori, ognuno di essi viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se non è possibile convertire nessuno degli input, l'espressione è un errore. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | d'acquisto | Converte il valore di input in un numero, se possibile. Se l'input è `null` o `false` , il risultato è 0. Se l'input è `true` , il risultato è 1. Se l'input è una stringa, viene convertito in un numero utilizzando la funzione di stringa [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) della specifica del linguaggio ECMAScript. Se vengono specificati più valori, ognuno di essi viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se non è possibile convertire nessuno degli input, l'espressione è un errore. |
| `['to-string', value]` | string | Converte il valore di input in una stringa. Se l'input è `null` , il risultato è `""` . Se l'input è un valore booleano, il risultato è `"true"` o `"false"` . Se l'input è un numero, viene convertito in una stringa utilizzando la funzione numero [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) della specifica del linguaggio ECMAScript. Se l'input è un colore, viene convertito nella stringa CSS RGBA color `"rgba(r,g,b,a)"` . In caso contrario, l'input viene convertito in una stringa utilizzando la funzione [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) della specifica del linguaggio ECMAScript. |
| `['typeof', value]` | string | Restituisce una stringa che descrive il tipo del valore specificato. |

> [!TIP]
> Se `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` nella console del browser viene visualizzato un messaggio di errore simile a, significa che nel codice è presente un'espressione che contiene una matrice che non dispone di una stringa per il primo valore. Se si desidera che l'espressione restituisca una matrice, eseguire il wrapping della matrice con l' `literal` espressione. Nell'esempio seguente viene impostata l' `offset` opzione Icon di un livello Symbol, che deve essere una matrice contenente due numeri, usando un' `match` espressione per scegliere tra due valori di offset in base al valore della  `entityType` proprietà della funzionalità punto.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Espressioni colore

Le espressioni colore semplificano la creazione e la modifica dei valori dei colori.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crea un valore di colore dai componenti *rosso*, *verde* e *blu* che devono essere compresi tra `0` e e `255` un componente alfa di `1` . Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['rgba', number, number, number, number]` | color | Crea un valore di colore dai componenti *rosso*, *verde*, *blu* che devono essere compresi tra `0` e e `255` un componente alfa in un intervallo `0` di `1` e. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['to-rgba']` | \[numero, numero, numero, numero\] | Restituisce una matrice a quattro elementi contenente i componenti *rosso*, *verde*, *blu* e *alfa* del colore di input, in questo ordine. |

**Esempio**

Nell'esempio seguente viene creato un valore di colore RGB con un valore *rosso* di `255` e i valori *verde* e *blu* calcolati moltiplicando `2.5` per il valore della `temperature` Proprietà. Con la variazione della temperatura, il colore cambierà in tonalità diverse di *rosso*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Espressioni operatore stringa

Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena più stringhe insieme. Ogni valore deve essere una stringa. `to-string`Se necessario, utilizzare l'espressione di tipo per convertire altri tipi valore in stringa. |
| `['downcase', string]` | string | Converte la stringa specificata in minuscolo. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | Determina se la stringa di input utilizza un set di caratteri supportato dallo stack del tipo di carattere corrente. ad esempio `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | string | Restituisce il tag di lingua IETF delle impostazioni locali utilizzate dalle regole di confronto fornite. Può essere utilizzato per determinare le impostazioni locali predefinite del sistema o per determinare se le impostazioni locali richieste sono state caricate correttamente. |
| `['upcase', string]` | string | Converte la stringa specificata in maiuscolo. |

**Esempio**

Nell'esempio seguente la `temperature` proprietà della funzione Point viene convertita in una stringa e quindi viene concatenato "° f" alla fine.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

L'espressione precedente esegue il rendering di un pin sulla mappa con il testo "64 ° f" sovrapposto, come illustrato nell'immagine seguente.

<center>

![Esempio ](media/how-to-expressions/string-operator-expression.png) di espressione dell'operatore String </center>

## <a name="interpolate-and-step-expressions"></a>Espressioni interpolate e Step

Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. Queste espressioni accettano un'espressione che restituisce un valore numerico come input, ad esempio `['get',  'temperature']` . Il valore di input viene valutato in base alle coppie di valori di input e output, per determinare il valore che meglio si adatta alla curva o alla funzione Step interpolata. I valori di output sono denominati "Stops". I valori di input per ogni interruzione devono essere un numero ed essere in ordine crescente. I valori di output devono essere un numero, una matrice di numeri o un colore.

### <a name="interpolate-expression"></a>Espressione interpolate

Un' `interpolate` espressione può essere utilizzata per calcolare un set continuo e uniforme di valori mediante l'interpolazione tra valori di interruzione. Un' `interpolate` espressione che restituisce i valori dei colori produce una sfumatura di colore in cui vengono selezionati i valori dei risultati.

Sono disponibili tre tipi di metodi di interpolazione che possono essere utilizzati in un' `interpolate` espressione:
 
* `['linear']` : Esegue l'interpolazione lineare tra la coppia di arresti.
* `['exponential', base]` -Esegue l'interpolazione esponenziale tra le interruzioni. Il `base` valore controlla la frequenza con cui aumenta l'output. I valori più elevati rendono l'output maggiore verso l'estremità superiore dell'intervallo. Un `base` valore vicino a 1 produce un output che aumenta in modo lineare.
* `['cubic-bezier', x1, y1, x2, y2]` -Esegue l'interpolazione usando una [curva di Bezier cubica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definita dai punti di controllo specificati.

Di seguito è riportato un esempio di come appaiono questi diversi tipi di interpolazioni. 

| Lineari  | Esponenziale | Curva di Bézier cubica |
|---------|-------------|--------------|
| ![Grafico interpolazione lineare](media/how-to-expressions/linear-interpolation.png) | ![Grafico di interpolazione esponenziale](media/how-to-expressions/exponential-interpolation.png) | ![Grafico di interpolazione Bezier cubica](media/how-to-expressions/bezier-curve-interpolation.png) |

Lo pseudocodice seguente definisce la struttura dell' `interpolate` espressione. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Esempio**

Nell'esempio seguente viene usata un' `linear interpolate` espressione per impostare la `color` proprietà di un livello Bubble in base alla `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, viene restituito "Blue". Se è compreso tra 60 e 70, viene restituito Yellow. Se è compreso tra 70 e 80, viene restituito "Orange". Se è maggiore o uguale a 80, verrà restituito "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.
 
<center>

![Esempio ](media/how-to-expressions/interpolate-expression-example.png) di espressione di interpolazione </center>

### <a name="step-expression"></a>Espressione Step

Un' `step` espressione può essere utilizzata per calcolare valori discreti di risultati con rientri mediante la valutazione di una [funzione a tratti-Constant](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definita da stops. 

Lo pseudocodice seguente definisce la struttura dell' `step` espressione. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Le espressioni Step restituiscono il valore di output dell'arresto immediatamente prima del valore di input oppure il primo valore di input se l'input è minore del primo arresto. 

**Esempio**

Nell'esempio seguente viene usata un' `step` espressione per impostare la `color` proprietà di un livello Bubble in base alla `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, viene restituito "Blue". Se è compreso tra 60 e 70, viene restituito "Yellow". Se è compreso tra 70 e 80, viene restituito "Orange". Se è maggiore o uguale a 80, verrà restituito "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.
 
<center>

![Esempio di espressione Step](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Espressioni specifiche del livello

Espressioni speciali che si applicano solo a livelli specifici.

### <a name="heat-map-density-expression"></a>Espressione densità mappa termica

Un'espressione di densità della mappa termica Recupera il valore della densità della mappa termica per ogni pixel in un livello mappa termica e viene definito come `['heatmap-density']` . Questo valore è un numero compreso tra `0` e `1` . Viene usato in combinazione con un' `interpolation` espressione o `step` per definire la sfumatura di colore usata per colorare la mappa termica. Questa espressione può essere utilizzata solo nell' [opzione Color](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) del livello mappa termica.

> [!TIP]
> Il colore in corrispondenza dell'indice 0, in un'espressione di interpolazione o nel colore predefinito del colore di un passaggio, definisce il colore dell'area in cui non sono presenti dati. Il colore in corrispondenza dell'indice 0 può essere utilizzato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente.

**Esempio**

Questo esempio usa un'espressione di interpolazione Liner per creare una sfumatura di colore uniforme per il rendering della mappa termica. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Oltre a usare una sfumatura uniforme per colorare una mappa termica, i colori possono essere specificati all'interno di un set di intervalli usando un' `step` espressione. L'uso `step` di un'espressione per colorare la mappa termica suddivide visivamente la densità in intervalli simili a una mappa di contorno o di stile radar.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Per ulteriori informazioni, vedere la documentazione relativa all' [aggiunta di un livello mappa termica](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Espressione avanzamento riga

Un'espressione di avanzamento riga recupera lo stato di avanzamento lungo una linea sfumata in un livello linea e viene definito come `['line-progress']` . Questo valore è un numero compreso tra 0 e 1. Viene usato in combinazione con un' `interpolation` espressione or `step` . Questa espressione può essere utilizzata solo con l' [opzione strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) del livello linea. 

> [!NOTE]
> L' `strokeGradient` opzione del livello linea richiede che l' `lineMetrics` opzione dell'origine dati sia impostata su `true` .

**Esempio**

In questo esempio viene utilizzata l' `['line-progress']` espressione per applicare una sfumatura di colore al tratto di una linea.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Vedere l'esempio in tempo reale](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Espressione formato campo testo

L'espressione del formato del campo di testo può essere usata con l' `textField` opzione della `textOptions` Proprietà livelli simboli per fornire la formattazione mista del testo. Questa espressione consente di specificare un set di stringhe di input e di opzioni di formattazione. È possibile specificare le opzioni seguenti per ogni stringa di input in questa espressione.

 * `'font-scale'` : Specifica il fattore di scala per le dimensioni del carattere. Se specificato, questo valore eseguirà l'override della `size` proprietà di `textOptions` per la singola stringa.
 * `'text-font'` : Specifica una o più famiglie di caratteri da usare per la stringa. Se specificato, questo valore eseguirà l'override della `font` proprietà di `textOptions` per la singola stringa.

Lo pseudocodice seguente definisce la struttura dell'espressione del formato del campo di testo. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Esempio**

Nell'esempio seguente viene formattato il campo di testo aggiungendo un tipo di carattere in grassetto e scalando verticalmente le dimensioni del carattere della `title` proprietà della funzionalità. Questo esempio aggiunge anche la `subTitle` proprietà della funzionalità in una nuova riga, con una dimensione del carattere ridimensionata.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Questo livello eseguirà il rendering della funzionalità punto, come illustrato nell'immagine seguente:
 
<center>

![Immagine della funzionalità punto con il campo ](media/how-to-expressions/text-field-format-expression.png) di testo formattato </center>

### <a name="number-format-expression"></a>Espressione formato numero

L' `number-format` espressione può essere utilizzata solo con l' `textField` opzione di un livello di simbolo. Questa espressione converte il numero fornito in una stringa formattata. Questa espressione esegue il wrapping della funzione [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) di JavaScript e supporta il set di opzioni seguente.

 * `locale` -Specificare questa opzione per convertire i numeri in stringhe in modo da essere allineati alla lingua specificata. Passare un [tag di lingua BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) in questa opzione.
 * `currency` -Per convertire il numero in una stringa che rappresenta una valuta. I valori possibili sono i [codici di valuta ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), ad esempio "USD" per il dollaro statunitense, "EUR" per l'euro, o "CNY" per il RMB cinese.
 * `'min-fraction-digits'` -Specifica il numero minimo di posizioni decimali da includere nella versione della stringa del numero.
 * `'max-fraction-digits'` : Specifica il numero massimo di posizioni decimali da includere nella versione della stringa del numero.

Lo pseudocodice seguente definisce la struttura dell'espressione del formato del campo di testo. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Esempio**

Nell'esempio seguente viene utilizzata un' `number-format` espressione per modificare la modalità con cui `revenue` viene eseguito il rendering della proprietà della funzionalità punto nell' `textField` opzione di un livello di simbolo, in modo da visualizzare un valore in dollari USA.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Questo livello eseguirà il rendering della funzionalità punto, come illustrato nell'immagine seguente:

<center>

![Esempio ](media/how-to-expressions/number-format-expression.png) di espressione di formato numerico </center>

### <a name="image-expression"></a>Espressione immagine

Un'espressione di immagine può essere utilizzata con `image` le `textField` Opzioni e di un livello di simbolo e l' `fillPattern` opzione del livello poligono. Questa espressione verifica che l'immagine richiesta esista nello stile e restituirà il nome dell'immagine risolta o `null` , a seconda che l'immagine sia attualmente nello stile. Questo processo di convalida è sincrono e richiede che l'immagine sia stata aggiunta allo stile prima di richiederla nell'argomento image.

**Esempio**

Nell'esempio seguente viene utilizzata un' `image` espressione per aggiungere un'icona inline con testo in un livello di simboli. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Questo livello eseguirà il rendering del campo di testo nel livello dei simboli, come illustrato nell'immagine seguente:

<center>

![Esempio ](media/how-to-expressions/image-expression.png) di espressione immagine </center>

## <a name="zoom-expression"></a>Espressione zoom

`zoom`Viene utilizzata un'espressione per recuperare il livello di zoom corrente della mappa in fase di rendering e viene definito come `['zoom']` . Questa espressione restituisce un numero compreso tra l'intervallo minimo e massimo del livello di zoom della mappa. I controlli mappa interattiva di Azure Maps per il supporto per Web e Android 25, numerati da 0 a 24. L'utilizzo dell' `zoom` espressione consente la modifica dinamica degli stili quando viene modificato il livello di zoom della mappa. L' `zoom` espressione può essere utilizzata solo con `interpolate` `step` espressioni e.

**Esempio**

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Quando la mappa viene ingrandita, i dati vengono aggregati insieme e il livello mappa termica ha un aspetto diverso. Un' `zoom` espressione può essere utilizzata per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copra la stessa area fisica della mappa. Il livello mappa termica renderà l'aspetto più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente. Il ridimensionamento del raggio, in modo che raddoppi a ogni livello di zoom, creerà una mappa termica che risulti coerente in tutti i livelli di zoom. Questa operazione può essere eseguita usando l' `zoom` espressione con un' `base 2 exponential interpolation` espressione, con il raggio di pixel impostato per il livello di zoom minimo e un raggio scalato per il livello di zoom massimo calcolato come `2 * Math.pow(2, minZoom - maxZoom)` illustrato di seguito.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Vedere l'esempio in tempo reale](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Espressioni di associazione variabili

Le espressioni di associazione variabili archiviano i risultati di un calcolo in una variabile. Pertanto, è possibile fare riferimento ai risultati del calcolo in un'altra posizione in un'espressione più volte. Si tratta di un'ottimizzazione utile per le espressioni che coinvolgono molti calcoli.

| Expression | Tipo restituito | Descrizione |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Archivia uno o più valori come variabili per l'utilizzo da parte dell' `var` espressione nell'espressione figlio che restituisce il risultato. |
| `['var', name: string]` | any | Fa riferimento a una variabile creata utilizzando l' `let` espressione. |

**Esempio**

In questo esempio viene utilizzata un'espressione che calcola i ricavi relativi al rapporto di temperatura, quindi utilizza un' `case` espressione per valutare diverse operazioni booleane su questo valore. L' `let` espressione viene utilizzata per archiviare i ricavi relativi al rapporto di temperatura, in modo che sia necessario calcolarli una sola volta. L' `var` espressione fa riferimento a questa variabile con la frequenza necessaria senza ricalcolarla.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice che implementano espressioni, vedere gli articoli seguenti:

> [!div class="nextstepaction"] 
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)

Altre informazioni sulle opzioni del livello che supportano le espressioni:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)