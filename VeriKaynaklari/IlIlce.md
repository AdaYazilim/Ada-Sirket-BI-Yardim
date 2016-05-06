<h1>IlIlce</h1>
Il/İlçe fihristi

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/IlIlce.md">IlIlce</a></td>
<td>AlternatifBelediyeKoduKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>SigortaliIlIlceKodu</td>
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
    dbo_ASW_BELEDIYE = config{[Schema="dbo",Item="ASW_BELEDIYE"]}[Data],
    #"Added Custom" = Table.AddColumn(dbo_ASW_BELEDIYE, "AlternatifBelediyeKoduKey", each [A_IL_KODU]&"_"&[A_ILCE_KODU]&"_"&[A_BELDE_KODU])
in
    #"Added Custom"
    
</pre>
