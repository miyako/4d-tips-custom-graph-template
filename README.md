# 4d-tips-custom-graph-template
GRAPHコマンドのカスタマイズ

8個を超える値を円グラフに出力する例

![2018-12-06 16 25 03](https://user-images.githubusercontent.com/1725068/49568361-26f65300-f974-11e8-84c9-4a66d8806f49.png)

```
$usePHT:=True  //PROCESS 4D TAGS
$usePHT:=False  //GRAPH

  //32-bit
  //$usePHT:=not(Version type?? 1)

C_PICTURE($graph)

$count:=18

ARRAY TEXT($labels;0)
ARRAY REAL($values;$count)

$char:="abcdefghijklmnopqrstuvwxyz"

For ($i;1;Size of array($values))
	$values{$i}:=Random%100
	APPEND TO ARRAY($labels;$char[[(Random%Length($char))+1]])
End for 

If ($usePHT)
	
	C_OBJECT($graphValues)
	$graphValues:=New object("xAxisLabels";->$labels)
	
	ARRAY POINTER($yValues;1)
	$yValues{1}:=->$values
	
	OB SET ARRAY($graphValues;"yValues";$yValues)
	
	$graphParameters:=New object("graphType";7)
	
	$templatePath:=Get 4D folder(Current resources folder)+"GraphTemplates"+Folder separator+"Graph_0_Template.svg"
	$template:=Document to text($templatePath;"utf-8")
	
	$templatePath:=Get 4D folder(Current resources folder)+"GraphTemplates"+Folder separator+"Graph_"+String($graphParameters.graphType)+"_Template.svg"
	$template:=$template+Document to text($templatePath;"utf-8")
	
	PROCESS 4D TAGS($template;$template;$graphValues;$graphParameters)
		
	$dom:=DOM Parse XML variable($template)
	SVG EXPORT TO PICTURE($dom;$graph;Own XML data source)
	
Else 
	
	GRAPH($graph;7;$labels;$values)
	
End if 

$path:=Temporary folder+Generate UUID+Folder separator
CREATE FOLDER($path;*)

$path:=$path+"graph.svg"

WRITE PICTURE FILE($path;$graph)

OPEN URL($path;"safari")

```

### Modifications

#96 don't trim the size of array

```
//If ($nbValues>8)
//	DELETE FROM ARRAY:C228($yValuesArrPtr{1}->;9;100000)
//	$nbValues:=8
//End if
```

#246: grow the ``$barColors`` array to support more than 8 values

```
ARRAY TEXT:C222($barColors;8)
$barColors{1}:="rgb(0,178,195)"
$barColors{2}:="rgb(255,195,56)"
$barColors{3}:="rgb(87,62,130)"
$barColors{4}:="rgb(79,168,57)"
$barColors{5}:="rgb(217,87,0)"
$barColors{6}:="rgb(29,157,242)"
$barColors{7}:="rgb(181,207,50)"
$barColors{8}:="rgb(212,58,38)"

For ($i;9;$nbValues)
	APPEND TO ARRAY:C911($barColors;$barColors{(($i-1)%8)+1})
End for
```

#261, #323: don't assume array size=``8``

```
ARRAY TEXT:C222($newBarColors;Size of array:C274($barColors))
ARRAY TEXT:C222($newBarColors;Size of array:C274($barColors))
```

```
ARRAY TEXT:C222($legendLabels;Size of array($barColors))
ARRAY TEXT:C222($legendLabels;Size of array($barColors))
```
