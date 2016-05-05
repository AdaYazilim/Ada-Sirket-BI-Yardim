<h1>HasarDosya</h1>
Hasar dosya verisine erişmek için kullanılır. Trafik hasarlarında, birden fazla mağdur olan hasarlarda tek dosyayı ifade eder. Kasko, yangın gibi mağdur olmayan branşlarda yine tek dosyayı ifade eder. Hasara ait ayrıntı bilgilere (ödeme, muallak toplamları gibi) erişmek için HasarMagdur veri kaynağını modele ekleyip bu veri kaynağı üzerindeki formülleri kullanabilirsiniz.

<a href="../Tablolar/SHASAR.md">SHASAR</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
<th>İlişki Özellikleri</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/HasarDosya.md">HasarDosya</a></td>
<td>PoliceKey</td>
<td>Cross filter direction: Single</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/HasarDosya.md">HasarDosya</a></td>
<td>HasarDosyaKey</td>
<td><a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a></td>
<td>HasarDosyaKey</td>
<td></td>
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
        #"Removed Other Columns" = Table.SelectColumns(dbo_SHASAR,{"HYIL", "HACENTA", "HBRANS", "HPOLICE_NO", "HTECDIT_NO", "HKOD", "HILKODU", "HDOSYA_NO", "HHTARIH", "HIHBAR_TAR", "FHASNO", "HZEYL_NO"}),
        #"Added Custom" = Table.AddColumn(#"Removed Other Columns", "PoliceKey", each [HACENTA]&"_"&[HBRANS]&"_"&[HPOLICE_NO]&"_"&[HTECDIT_NO]&"_"&[HZEYL_NO]),
        #"Added Custom1" = Table.AddColumn(#"Added Custom", "HasarKey", each [HKOD]&"_"&[HILKODU]&"_"&[HDOSYA_NO]&"_"&[FHASNO]),
        #"Filtered Rows" = Table.SelectRows(#"Added Custom1", each ([FHASNO] = "    " or [FHASNO] = "0001")),
        #"Added Custom4" = Table.AddColumn(#"Filtered Rows", "HasarDosyaKey", each [HKOD]&"_"&[HILKODU]&"_"&[HDOSYA_NO])        
in
    #"Added Custom4"
</pre>

<h2>Formüller</h2>

<h4>OdemeToplami (Measure)</h4>
Hasara ait yapılan tüm ödemelerin toplamıdır. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a>
<pre>OdemeToplami = SUMX(RELATEDTABLE(HasarMagdur);HasarMagdur[OdemeToplami])</pre>

<h4>TahsilatToplami (Measure)</h4>
Hasara ait yapılan tüm tahsilatların toplamıdır. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a>
<pre>TahsilatToplami = SUMX(RELATEDTABLE(HasarMagdur);HasarMagdur[TahsilatToplami])</pre>

<h4>HasarMagdurNetOdeme (Measure)</h4>
Hasara ait yapılan tüm ödemeler - yapılan tüm tahsilatlar rakamını belirtir. İhtiyaç duyulan ilişkili tablo <a href="../VeriKaynaklari/HasarMagdur.md">HasarMagdur</a>
<pre>HasarMagdurNetOdeme = [OdemeToplami] - [TahsilatToplami]</pre>


