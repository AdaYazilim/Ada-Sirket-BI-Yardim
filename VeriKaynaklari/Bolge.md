<h1>Bolge</h1>
Bölgeler fihristi

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>ACE_BOLGE_KODU</td>
<td><a href="../VeriKaynaklari/Bolge.md">Bolge</a></td>
<td>BOLGEKODU</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    config = let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
Table0 = Source{0}[Table],
   Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
   config = Table1{0},
   veritabani = Sql.Database(config[server], config[database]),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"server", type text}, {"database", type text}})
in
    veritabani,
    dbo_SPARAM = config{[Schema="dbo",Item="SPARAM"]}[Data],
    #"Removed Other Columns" = Table.SelectColumns(dbo_SPARAM,{"SIRKETADI", "BOLGEKODU", "BOLGEADI"})
in
    #"Removed Other Columns"
    
</pre>
