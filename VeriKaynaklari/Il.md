<h1>İlr</h1>
Il fihristi

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/Il.md">İl</a></td>
<td>A_IL_KODU</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>IL_KODU</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="SELECT DISTINCT(A_IL_KODU), IL_ADI FROM ASW_BELEDIYE ORDER BY A_IL_KODU"]) 
in
    veritabani
</pre>
