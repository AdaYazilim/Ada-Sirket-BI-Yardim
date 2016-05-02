<h1>PoliceZeyilTeminat</h1>
Poliçe ve zeyillerin teminat ayrıntılarına erişmek için kullanılır. 

<a href="../Tablolar/SPOLICE.md">STEMSATR</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">Police</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyilTeminat.md">PoliceZeyil</a></td>
<td>PoliceKey</td>
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
        dbo_STEMSATR = config{[Schema="dbo",Item="STEMSATR"]}[Data],
        #"Removed Other Columns" = Table.SelectColumns(dbo_STEMSATR,{"TYIL", "TACENTA", "TBRANS", "TPOLICE_NO", "TZEYL_NO", "TTECDIT_NO", "TEM_KODU", "TEK_BRANS", "SIG_BEDELI", "FIYAT", "FINDARTPRM", "FTEMKOM"}),
        #"Added Custom" = Table.AddColumn(#"Removed Other Columns", "PoliceKey", each [TACENTA]&"_"&[TBRANS]&"_"&[TPOLICE_NO]&"_"&[TTECDIT_NO]&"_"&[TZEYL_NO])
in
    #"Added Custom"
</pre>

<h2>Formüller</h2>

<h4>KayitIptalKodu</h4>
Teminatın ilişkili olduğu poliçe / zeyilin kayıt iptal kodunu belirtir. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a>
<pre>KayitIptalKodu = RELATED(PoliceZeyil[IPT_KAYIT])</pre>

<h4>NetPrim</h4>
Tahakkuk kayıtlarda artı, iade / iptal kayıtlarda eksi tutarlı olarak teminatın net primini belirtir.
<pre>NetPrim = IF([KayitIptalKodu]="K";PoliceZeyil[FINDARTPRM];PoliceZeyil[FINDARTPRM]*-1)</pre>

<h4>NetPrim</h4>
Tahakkuk kayıtlarda artı, iade / iptal kayıtlarda eksi tutarlı olarak net primi ifade eder.
<pre>NetPrim = IF(PoliceZeyil[IPT_KAYIT]="K";PoliceZeyil[PRIM];PoliceZeyil[PRIM]*-1)</pre>
