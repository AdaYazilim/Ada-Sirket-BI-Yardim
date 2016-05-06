<h1>PoliceZeyilTanzimTarihi</h1>
Tüm poliçe/zeyil kayıtlarındaki farklı tanzim tarihlerinin fihristi. Tek kolon olarak sistemde yer alan tüm tanzim tarihlerini tutar.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyilTanzimTarihi.md">PoliceZeyilTanzimTarihi</a></td>
<td>PoliceZeyil Tanzim Tarihi</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>TANZIM_TAR</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="SELECT DISTINCT(TANZIM_TAR) FROM SPOLICE WHERE IPT_KAYIT IN ('K','I')"]),
    #"Renamed Columns" = Table.RenameColumns(veritabani,{{"TANZIM_TAR", "PoliceZeyil Tanzim Tarihi"}})
in
    #"Renamed Columns"
</pre>

