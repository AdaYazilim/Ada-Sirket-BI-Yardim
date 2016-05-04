<h1>Urun</h1>
Ürün fihristi

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/Bolge.md">Urun</a></td>
<td>BKODU</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">Police</a></td>
<td>BRANS</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="select * from SBRANS WHERE BKODU = ALTKODU"]),
    #"Removed Other Columns" = Table.SelectColumns(veritabani,{"BKODU", "HASARKODU", "BADI", "IADI", "ACIKLAMA"}) 
in
    #"Removed Other Columns"
</pre>
