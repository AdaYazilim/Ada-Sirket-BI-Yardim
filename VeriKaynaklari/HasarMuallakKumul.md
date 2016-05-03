<h1>HasarMuallakKumul</h1>
Dosya bazında ay-yıl bazında, muallak kümüllerine erişmek için kullanılır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>HasarKey</td>
<td><a href="../VeriKaynaklari/HasarMuallakKumul.md">HasarMuallakKumul</a></td>
<td>HasarKey</td>
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
        dbo_SHASKUM = config{[Schema="dbo",Item="SHASKUM"]}[Data],
        #"Added Custom1" = Table.AddColumn(dbo_SHASKUM, "HasarKey", each [HKOD]&"_"&[HILKODU]&"_"&[HDOSYA_NO]&"_"&[HSIRA_NO])
in
    #"Added Custom1"
</pre>

