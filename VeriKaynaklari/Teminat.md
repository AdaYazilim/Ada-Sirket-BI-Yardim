<h1>Teminat</h1>
Teminat tanımlarını içerir. 

<a href="../Tablolar/STEMINAT.md">STEMINAT</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/Teminat.md">Teminat</a></td>
<td>TeminatKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyilTeminat.md">PoliceZeyilTeminat</a></td>
<td>TeminatKey</td>
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
        dbo_STEMINAT = config{[Schema="dbo",Item="STEMINAT"]}[Data],
        #"Added Custom" = Table.AddColumn(dbo_STEMINAT, "TeminatKey", each [TBRANS]&"_"&[TKODU]),
        #"Removed Other Columns" = Table.SelectColumns(#"Added Custom",{"TBRANS", "TKODU", "TREA_BRANS", "TREJ_BRANS", "EKRSIRANO", "TADI", "TEM_TIPI", "REASURANS", "UYGULAMA_TARIHI", "TeminatKey"}),
    #"Added Custom1" = Table.AddColumn(#"Removed Other Columns", "TeminatBransKodVeAdi", each "("&[TBRANS]&") "&[TKODU]&" - "&[TADI])
in
    #"Added Custom1"
</pre>

<h2>Formüller</h2>

