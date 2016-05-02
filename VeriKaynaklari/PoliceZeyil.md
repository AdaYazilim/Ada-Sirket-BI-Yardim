<h1>PoliceZeyil</h1>
Poliçe ve zeyil kayıtlarına erişmek için kullanılır. <a href="../VeriKaynaklari/Police.md">Police</a> veri kaynağına ilişkilendirilip poliçe son durum bilgilerini almak için kullanılabilir.

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
<td><a href="../VeriKaynaklari/Police.md">Police</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">Police-Zeyil</a></td>
<td>AnaPoliceKey</td>
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
		#"Removed Other Columns" = Table.SelectColumns(dbo_SPOLICE,{"YIL", "ACENTA", "BRANS", "POLICE_NO", "ZEYLKOD2", "ZEYL_KODU", "ZEYL_NO", "TECDIT_NO", "IPT_KAYIT", "BAS_TAR", "BIT_TAR", "TANZIM_TAR", "UW_YEAR", "TANZIM_YER", "KISI_SAYI", "PRIM", "TARIFE_BAS", "ACE_BOLGE_KODU", "ORTAK_NO", "ARAC_TARZ"}),
		#"Filtered Rows" = Table.SelectRows(#"Removed Other Columns", each ([IPT_KAYIT] = "I" or [IPT_KAYIT] = "K")),
		#"Added Custom" = Table.AddColumn(#"Filtered Rows", "PoliceKey", each [ACENTA]&"_"&[BRANS]&"_"&[POLICE_NO]&"_"&[TECDIT_NO]&"_"&[ZEYL_NO]),
		#"Added Custom1" = Table.AddColumn(#"Added Custom", "AnaPoliceKey", each [ACENTA]&"_"&[BRANS]&"_"&[POLICE_NO]&"_"&[TECDIT_NO]&"_   ")
in
	#"Added Custom1"
</pre>

<h2>Formüller</h2>
