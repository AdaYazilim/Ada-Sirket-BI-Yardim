<h1>HasarOdeme</h1>
Hasar ödeme bilgilerini içerir.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/Hasar.md">Hasar</a></td>
<td>HasarKey</td>
<td><a href="../VeriKaynaklari/HasarOdeme.md">HasarOdeme</a></td>
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
        dbo_SODEME = config{[Schema="dbo",Item="SODEME"]}[Data],
        #"Removed Other Columns" = Table.SelectColumns(dbo_SODEME,{"OACENTA", "OKOD", "OILKODU", "ODOSYA_NO", "OTARIH", "OKODU", "OMIKTARI", "ODURUM", "ODURUM2", "FOHASNO"}),
        #"Added Custom1" = Table.AddColumn(#"Removed Other Columns", "HasarKey", each [OKOD]&"_"&[OILKODU]&"_"&[ODOSYA_NO]&"_"&[FOHASNO]),
        #"Filtered Rows" = Table.SelectRows(#"Added Custom1", each ([ODURUM] = "O")),
        #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each not Text.StartsWith([OKODU], "9"))
in
    #"Filtered Rows1"
</pre>

<h2>Formüller</h2>

<h4>GidenOdeme</h4>
Hasar ödemesinin şirketten çıkış olup olmadığını belirtir. 
<pre>GidenOdeme = VALUE(HasarOdeme[OKODU])<90</pre>
