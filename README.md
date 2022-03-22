# BPC


7.220 naptest BPC


<pre>

begin
eap_globals.USTAW_firme(300326);
eap_globals.USTAW_konsolidacje('N');
end;


select knt_pelny_numer id, knt_nazwa as "Polish Description", null as "English Description" 
, case when substr(knt_pelny_numer,1,1) in (5,7) or  substr(knt_pelny_numer,1,3) in ('870') then 'EXP' else 'AST' end as "Account type"
, null as "Rate type"
, null as "Hierarchy"
, null as "Intercompany"
 from kg_konta where knt_typ = 'B' and knt_rp_rok = 2022
order by 1

</pre>
