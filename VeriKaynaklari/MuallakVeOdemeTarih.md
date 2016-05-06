<h1>MuallakVeOdemeTarih</h1>
Muallak, hasar ödeme ve hasar tahsilat tarihini filtrelemek için kullanılır. 

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/MuallakVeOdemeTarih.md">MuallakVeOdemeTarih</a></td>
<td>Muallak Tarihi</td>
<td><a href="../VeriKaynaklari/HasarMuallakKumul.md">HasarMuallakKumul</a></td>
<td>YILAY</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/MuallakVeOdemeTarih.md">MuallakVeOdemeTarih</a></td>
<td>Muallak Tarihi</td>
<td><a href="../VeriKaynaklari/HasarOdeme.md">HasarOdeme</a></td>
<td>HasarOdemeYilAy</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/MuallakVeOdemeTarih.md">MuallakVeOdemeTarih</a></td>
<td>Muallak Tarihi</td>
<td><a href="../VeriKaynaklari/HasarTahsilat.md">HasarTahsilat</a></td>
<td>HasarTahsilatYilAy</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="SELECT DISTINCT(YILAY) FROM SHASKUM where YILAY NOT LIKE '%99'"]),
    #"Renamed Columns" = Table.RenameColumns(veritabani,{{"YILAY", "Muallak / Ödeme Tarihi"}})
in
    #"Renamed Columns"
</pre>

