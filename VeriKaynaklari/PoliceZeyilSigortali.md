<h1>PoliceZeyilSigortali</h1>
Poliçe ve zeyillerin sigortalı ayrıntılarına erişmek için kullanılır. 

<a href="../Tablolar/SMUSTERI.md">SMUSTERI</a> tablosunu kullanır.

<h2>İlişki Kurulabilecek Veri Kaynakları</h2>
<table>
<tr>
<th>Ana Veri Kaynağı</th>
<th>Ana Veri Kaynağı Kolon Adı</th>
<th>Çocuk Veri Kaynağı</th>
<th>Çocuk Veri Kaynağı Kolon Adi</th>
</tr>
<tr>
<td><a href="../VeriKaynaklari/PoliceZeyil.md">PoliceZeyil</a></td>
<td>PoliceKey</td>
<td><a href="../VeriKaynaklari/PoliceZeyilSigortali.md">PoliceZeyilSigortali</a></td>
<td>PoliceKey</td>
</tr>
</table>


<h2>Power Query</h2>
<pre>
let
    Source = Xml.Tables(File.Contents("C:\Power BI Raporlar\config.xml")),
    Table0 = Source{0}[Table],
    Table1 = Table.TransformColumnTypes(Table0,{{"server", type text}, {"database", type text}}),
    config = Table1{0},
    veritabani = Sql.Database(config[server], config[database], [Query="select * from SMUSTERI M where M.ADRES_CINS = ' ' AND exists (select '' from SPOLICE p where p.acenta = m.macenta and p.BRANS = M.MBRANS AND P.POLICE_NO = M.MPOLICE AND P.TECDIT_NO = M.MTECDIT_NO AND P.ZEYL_NO=M.MZEYL_NO AND P.IPT_KAYIT IN ('K','I'))"]),
    #"Removed Other Columns" = Table.SelectColumns(veritabani,{"MYIL", "MACENTA", "MBRANS", "MPOLICE", "MZEYL_NO", "MTECDIT_NO", "MKODU", "MADI", "MUNVAN", "ADRES1", "ADRES2", "ILCESI", "ILI", "FMMAHALLE", "FKAPINO", "FDAIRENO", "FMAPT", "FKAT", "FBELKOD", "FVERKIMNO", "FTCKIMNO", "FUYRUK", "FBABAADI", "FGERTUZ", "FSEVERGINO", "PASAPORTNO", "IL_KODU", "ILCE_KODU", "EGITIM", "CINSIYET", "DOGUM_TAR", "MSOYADI", "MTIPI", "DOGUM_YERI", "VERGI_DAIRESI", "EV_IS_ADRESI", "OLUM_TARIHI", "CADDE", "SOKAK", "UYRUKDIGER", "SEMT", "BELDE_KODU", "IS_TEL", "CEP_TEL", "MEDENI_HALI", "UAVT_IL_KODU", "UAVT_ILCE_KODU", "UAVT_KOY_KODU", "UAVT_MAHALLE_KODU", "UAVT_CSBM_KODU", "UAVT_BINA_KODU", "UAVT_DAIRE_KODU", "UAVT_ADRES_KODU"}) ,
    #"Added Custom" = Table.AddColumn(#"Removed Other Columns", "PoliceKey", each [MACENTA]&"_"&[MBRANS]&"_"&[MPOLICE]&"_"&[MTECDIT_NO]&"_"&[MZEYL_NO])
in
    #"Added Custom"
</pre>

<h2>Formüller</h2>

