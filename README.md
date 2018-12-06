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
	
	TEXT TO DOCUMENT(System folder(Desktop)+"test.xml";$template)
	
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
