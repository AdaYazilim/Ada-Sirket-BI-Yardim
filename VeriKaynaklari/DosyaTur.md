<h1>DosyaTur</h1>
Hasar dosya tür fihristi.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/DosyaTur.md">DosyaTur</a></td>
<td>DosyaTurKey</td>
<td><a href="../VeriKaynaklari/Hasar.md">Hasar</a></td>
<td>DosyaTurKey</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="SELECT SORU_AD+'_'+DEGER AS DosyaTurKey, DEGER, METIN from ASW_URUN_SORU_SECENEK WHERE SORU_AD like 'DosyaTuru_%'"])
in
    veritabani
</pre>
