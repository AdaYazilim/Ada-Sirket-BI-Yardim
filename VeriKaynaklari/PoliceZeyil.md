<h1>PoliceZeyil</h1>
Tüm poliçe ve zeyil kayıtlarına erişmek için kullanılır. <a href="../VeriKaynaklari/Police.md">Police</a> veri kaynağına ilişkilendirilip poliçe son durum bilgilerini almak için kullanılabilir.

<a href="../Tablolar/SPOLICE.md">SPOLICE</a> tablosunu kullanır.

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
<td><a href="../VeriKaynaklari/Police.md">Police</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>AnaPoliceKey</td>
<td></td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyilTeminat.md">PoliceZeyilTeminat</a></td>
<td>PoliceKey</td>
<td></td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/HasarDosya.md">HasarDosya</a></td>
<td>PoliceKey</td>
<td>Cross filter direction: Single</td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/Bolge.md">Bolge</a></td>
<td>BOLGEKODU</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>ACE_BOLGE_KODU</td>
<td></td>
</tr>
<tr>
<td><a href="../VeriKaynaklari/IlIlce.md">IlIlce</a></td>
<td>AlternatifBelediyeKoduKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>SigortaliIlIlceKodu</td>
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
        dbo_SPOLICE = config{[Schema="dbo",Item="SPOLICE"]}[Data],
        #"Removed Other Columns" = Table.SelectColumns(dbo_SPOLICE,{"YIL", "ACENTA", "BRANS", "POLICE_NO", "ZEYLKOD2", "ZEYL_KODU", "ZEYL_NO", "TECDIT_NO", "IPT_KAYIT", "BAS_TAR", "BIT_TAR", "TANZIM_TAR", "UW_YEAR", "TANZIM_YER", "KISI_SAYI", "PRIM", "TARIFE_BAS", "ACE_BOLGE_KODU", "ORTAK_NO", "ARAC_TARZ", "T_SIG_IL_KODU", "T_SIG_ILCE_KODU", "T_SIG_BELDE_KODU", "T_PLAKA"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Other Columns", each ([IPT_KAYIT] = "I" or [IPT_KAYIT] = "K")),
        #"Added Custom" = Table.AddColumn(#"Filtered Rows", "PoliceKey", each [ACENTA]&"_"&[BRANS]&"_"&[POLICE_NO]&"_"&[TECDIT_NO]&"_"&[ZEYL_NO]),
        #"Added Custom1" = Table.AddColumn(#"Added Custom", "AnaPoliceKey", each [ACENTA]&"_"&[BRANS]&"_"&[POLICE_NO]&"_"&[TECDIT_NO]&"_   "),
        #"Added Custom2" = Table.AddColumn(#"Added Custom1", "SigortaliIlIlceKodu", each [T_SIG_IL_KODU]&"_"&[T_SIG_ILCE_KODU]&"_"&[T_SIG_BELDE_KODU])
in
    #"Added Custom2"
</pre>

<h2>Formüller</h2>

<h4>PoliceZeyilNetPrim (Column)</h4>
Tahakkuk kayıtlarda artı, iade / iptal kayıtlarda eksi tutarlı olarak net primi ifade eder.
<pre>PoliceZeyilNetPrim = IF([IPT_KAYIT]="K";[PRIM];[PRIM]*-1)</pre>

<h4>PlakaIlKodu (Column)</h4>
Plaka il kodunu rakam cinsinden belirtir.
<pre>PlakaIlKodu = IF(IFERROR(SEARCH(" ";[T_PLAKA]);-1) <= 0;"0";LEFT([T_PLAKA];SEARCH(" ";[T_PLAKA])))</pre>

<h4>PoliceZeyilTanzimYil (Column)</h4>
Poliçe/zeyil kaydının tanzim yılını belirtir.
<pre>PoliceZeyilTanzimYil = YEAR(PoliceZeyil[TANZIM_TAR])</pre>

<h4>PoliceZeyilTanzimAy (Column)</h4>
Poliçe/zeyil kaydının tanzim ayını belirtir.
<pre>PoliceZeyilTanzimAy = MONTH(PoliceZeyil[TANZIM_TAR])</pre>

<h4>PoliceZeyilTanzimGun (Column)</h4>
Poliçe/zeyil kaydının tanzim gününü belirtir.
<pre>PoliceZeyilTanzimGun = DAY(PoliceZeyil[TANZIM_TAR])</pre>

<h4>MebIptalZeyilimi (Column)</h4>
Poliçe/zeyil kaydının bir mebdeinden iptal zeyili olup olmadığını belirtir. Kayıt bir poliçe ise her zaman false döner. Bir poliçenin mebdeinden iptal edilip edilmediğini raporlarda kullanmak için <a href="../VeriKaynaklari/Police.md">Police</a> veri kaynağını kullanın.
<pre>MebIptalZeyilimi = IFERROR(SEARCH("mebd";RELATED(ZeyilTanim[AD])) > 0;FALSE())</pre>

<h4>GunlukPrim (Column)</h4>
Poliçe/zeyil kaydının bir günlük primini belirtir.
<pre>GunlukPrim = [PRIM]/[PoliceZeyilGunSayisi]</pre>

<h4>PoliceZeyilGunSayisi (Column)</h4>
Poliçe/zeyil kaydının başlangıcı ile bitişi arasındaki gün sayısını belirtir. Aynı gün başlayıp biten poliçelerde değer 1'dir.
<pre>PoliceZeyilGunSayisi = IF([BIT_TAR]<=[BAS_TAR];1;DATEDIFF([BAS_TAR];[BIT_TAR];DAY))</pre>

<h4>PoliceZeyilKPK (Column)</h4>
Poliçe/zeyil kaydının net KPK değerini belirtir. Tahakkuk kayıtlarda artı, iptal kayıtlarda eksi tutarlıdır.
<pre>PoliceZeyilKPK = IF([IPT_KAYIT]="K";[KalanGunSayisi]*[GunlukPrim];-1*[KalanGunSayisi]*[GunlukPrim])</pre>

<h4>KalanGunSayisi (Column)</h4>
Poliçe/zeyil kaydının bitişine kalan gün sayısını belirtir. İptal edilmiş ve sonlanmış poliçelerde değer 0'dır.
<pre>KalanGunSayisi = IF(OR(TODAY()>=[BIT_TAR];RELATED(Police[IptalEdilmis]));0;DATEDIFF(TODAY();[BIT_TAR];DAY))</pre>





