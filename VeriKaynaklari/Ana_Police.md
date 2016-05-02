<h1>Police</h1>
Poliçe kayıtlarına erişmek için kullanılır. Formüller kullanılarak poliçe üzerindeki tüm verilere (poliçe üzerindeki hali veya son durumu) erişilebilir.

<a href="../Tablolar/SPOLICE.md">SPOLICE</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td>Police</td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/Police-Zeyil.md">Police-Zeyil</a></td>
<td>AnaPoliceKey</td>
</tr>
<tr>
<td>Police</td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/Police_TrafikKaskoAyrinti.md">Police-Ayrinti-Arac</a></td>
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
        dbo_SPOLICE = config{[Schema="dbo",Item="SPOLICE"]}[Data],
        #"Filtered Rows" = Table.SelectRows(dbo_SPOLICE, each ([IPT_KAYIT] = "I" or [IPT_KAYIT] = "K") and ([ZEYL_NO] = "   ")),
        #"Added Custom" = Table.AddColumn(#"Filtered Rows", "PoliceKey", each [ACENTA]&"_"&[BRANS]&"_"&[POLICE_NO]&"_"&[TECDIT_NO]&"_"&[ZEYL_NO]),
        #"Removed Other Columns" = Table.SelectColumns(#"Added Custom",{"YIL", "ACENTA", "BRANS", "POLICE_NO", "ZEYLKOD2", "ZEYL_KODU", "ZEYL_NO", "TECDIT_NO", "IPT_KAYIT", "BAS_TAR", "BIT_TAR", "TANZIM_TAR", "UW_YEAR", "TANZIM_YER", "KISI_SAYI", "PRIM", "TARIFE_BAS", "ACE_BOLGE_KODU", "IPTAL_DURUMU", "IPTAL_TARIHI", "ORTAK_NO", "ARAC_TARZ"})
in
    #"Removed Other Columns"
</pre>

<h2>Formüller</h2>
<h4>SonDurum_Tarz</h4>
Araç tarzının son durumunu belirtir. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/Police-Zeyil.md">Police-Zeyil</a>
<br>
<b>SonDurum_Tarz</b> = SUMMARIZE(TOPN(1; relatedtable(Police-Zeyil); Police-Zeyil[ZEYL_NO];DESC);[ARAC_TARZ])
