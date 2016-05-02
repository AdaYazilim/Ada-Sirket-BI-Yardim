<h1>Ana_Police</h1>
Ana_Police, poliçe kayıtlarına erişmek için kullanılır. Bu tablo ana tablo olarak kullanılarak ana poliçe üzerindeki tüm verilere (poliçedeki hali veya son durumu) erişilebilir.

<h2>İlgili Tablo</h2>
<a href="../Tablolar/SPOLICE.md">SPOLICE</a>

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
