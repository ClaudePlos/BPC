# BPC


7.220 naptest BPC <BPCrek...2022>


<pre>

-- BPCV_STATUTORY_2021


begin
eap_globals.USTAW_konsolidacje('T');
end;

delete BPC_STATUTORY_2021

commit

insert into BPC_STATUTORY_2021 (time, gl_account, company, partner, ct, dt)
select time, gl_account, company, partner, sum(CT) CT, sum(DT) DT  from ( 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy, kgt_dokumenty where dok_kl_kod_pod = frm_kl_id and dok_id = ks_dok_id) Partner
, ks_kwota CT
, null DT
, 'Ct'type
from kgt_ksiegowania, kg_konta, eat_firmy
where ks_knt_ma = knt_id and ks_frm_id = frm_id 
and ks_f_symulacja = 'T' 
and knt_typ = 'B'
and to_char(ks_dok_data_zaksiegowania,'YYYY') = 2021 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
union all 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy, kgt_dokumenty where dok_kl_kod_pod = frm_kl_id and dok_id = ks_dok_id) Partner
, null
, ks_kwota
, 'Dt'type
from kgt_ksiegowania, kg_konta, eat_firmy
where ks_knt_wn = knt_id and ks_frm_id = frm_id 
and ks_f_symulacja = 'T' 
and knt_typ = 'B'
and to_char(ks_dok_data_zaksiegowania,'YYYY') = 2021 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
) group by time, gl_account, company, partner


commit



-- Account:
begin
eap_globals.USTAW_firme(300326);
eap_globals.USTAW_konsolidacje('N');
end;

select knt_pelny_numer id, knt_nazwa as "Polish Description", null as "English Description" 
, case when substr(knt_pelny_numer,1,1) in (5,7) or  substr(knt_pelny_numer,1,3) in ('870') then 'EXP' else 'AST' end as "Account type"
, null as "Rate type"
, substr(knt_pelny_numer, 0, length(knt_pelny_numer)-length(knt_numer_segmentu)-1)   as "Hierarchy"
, null as "Intercompany"
 from kg_konta k1 where knt_typ = 'B' and knt_rp_rok = 2022
order by 1


</pre>
