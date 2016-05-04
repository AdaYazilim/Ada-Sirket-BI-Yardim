<h1>HasarMagdur</h1>
Trafik hasarlarında her bir mağduru ifade eder. Diğer branş hasarlarında tek bir mağdur (sigortalı) var gibi kabul edilir dolayısıyla HasarMagdur verisi diğer branşlarda hasara ait bilgileri içerir.

<a href="../Tablolar/SHASAR.md">SHASAR</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/HasarDosya.md">HasarDosya</a></td>
<td>HasarDosyaKey</td>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>HasarDosyaKey</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/HasarTur.md">HasarTur</a></td>
<td>HasarTurKey</td>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>HasarMagdur</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/DosyaTur.md">DosyaTur</a></td>
<td>DosyaTurKey</td>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>DosyaTurKey</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>HasarKey</td>
<td><a href="../VeriKaynaklari/HasarOdeme.md">HasarOdeme</a></td>
<td>HasarKey</td>
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
        dbo_SHASAR = config{[Schema="dbo",Item="SHASAR"]}[Data],
        #"Removed Other Columns" = Table.SelectColumns(dbo_SHASAR,{"HYIL", "HACENTA", "HBRANS", "HPOLICE_NO", "HTECDIT_NO", "HKOD", "HILKODU", "HDOSYA_NO", "HHTARIH", "HTURU", "HIHBAR_TAR", "HIHBAR_SAA", "HDURUM", "HKAPANIS", "FHASNO", "FDOSYATUR", "BOLGEKODU", "FUW_YEAR", "HZEYL_NO"}),
        #"Added Custom" = Table.AddColumn(#"Removed Other Columns", "PoliceKey", each [HACENTA]&"_"&[HBRANS]&"_"&[HPOLICE_NO]&"_"&[HTECDIT_NO]&"_"&[HZEYL_NO]),
        #"Added Custom1" = Table.AddColumn(#"Added Custom", "HasarKey", each [HKOD]&"_"&[HILKODU]&"_"&[HDOSYA_NO]&"_"&[FHASNO]),
        #"Added Custom2" = Table.AddColumn(#"Added Custom1", "HasarTurKey", each "HasarTuru_"&[HKOD]&"_"&[HTURU]),
        #"Added Custom3" = Table.AddColumn(#"Added Custom2", "DosyaTurKey", each "DosyaTuru_"&[HKOD]&"_"+[FDOSYATUR]),
        #"Added Custom4" = Table.AddColumn(#"Added Custom3", "HasarDosyaKey", each [HKOD]&"_"&[HILKODU]&"_"&[HDOSYA_NO]) 
in
    #"Added Custom4"
</pre>

<h2>Formüller</h2>

<h4>HasarTur (Column)</h4>
Hasar türünü belirtir. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarTur.md">HasarTur</a>
<pre>HasarTur = RELATED(HasarTur[Hasar Nedeni])</pre>


<h4>DosyaTur</h4>
Dosya türünü belirtir. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/DosyaTur.md">DosyaTur</a>
<pre>DosyaTur = RELATED(DosyaTur[METIN])</pre>

<h4>OdemeToplami (Measure)</h4>
Hasara ait yapılan tüm ödemelerin toplamıdır. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarOdeme.md">HasarOdeme</a>
<pre>OdemeToplami = SUMX(RELATEDTABLE(HasarOdeme);HasarOdeme[OMIKTARI])</pre>

<h4>TahsilatToplami (Measure)</h4>
Hasara ait yapılan tüm tahsilatların toplamıdır. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarTahsilat.md">HasarTahsilat</a>
<pre>TahsilatToplami = SUMX(RELATEDTABLE(HasarTahsilat);HasarTahsilat[OMIKTARI])</pre>

<h4>HasarNetOdeme (Measure)</h4>
Hasara ait yapılan tüm ödemeler - yapılan tüm tahsilatlar rakamını belirtir. İhtiyaç duyulan ilişkili tablolar <a href="../VeriKaynaklari/HasarOdeme.md">HasarOdeme</a> ve <a href="../VeriKaynaklari/HasarTahsilat.md">HasarTahsilat</a>
<pre>HasarNetOdeme = [OdemeToplami] - [TahsilatToplami]</pre>


