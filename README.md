TODO:
1. Federica - Account Validation
2. widok Waldka 
3. Hotel w Gliwicach 



VPN: Cisco kilent: KSkowronski rekeep2022 d: vpn.rekeep.com
BPC: KSKOWRON / rekeep2022


Question to next meating:
1. how the data is stored, cumulative after refresh or only update (clear store and one moret time load)?


# BPC


7.220 naptest BPC <BPCrek...2022>


<pre>

1. Eaadm add pivilage to eap_globals for BPC
2. add privilage to eat_firmy 

create view  kgv_konta as select * from kg_konta

create view  eav_firmy as select * from eat_firmy

create view  ckkv_klienci as select * from ckk_klienci

create view  kgv_ksiegowania as select * from kgt_ksiegowania

create view  kgv_dokumenty as select * from kgt_dokumenty

-- table:


-- BPCV_STATUTORY
CREATE TABLE BPC_STATUTORY
(
  TIME        VARCHAR2(7 BYTE),
  GL_ACCOUNT  VARCHAR2(100 BYTE),
  COMPANY     NUMBER,
  PARTNER     NUMBER,
  CT          NUMBER,
  DT          NUMBER
)
TABLESPACE USERS
PCTUSED    0
PCTFREE    10
INITRANS   1
MAXTRANS   255
STORAGE    (
            PCTINCREASE      0
            BUFFER_POOL      DEFAULT
           )
LOGGING 
NOCOMPRESS 
NOCACHE
NOPARALLEL
MONITORING;


-- new version from 2022-04:


-- 01
delete BPC_STATUTORY

commit

begin
eap_globals.USTAW_konsolidacje('T');
end;

insert into BPC_STATUTORY (time, gl_account, company, partner, ct, dt)
select time, gl_account, company, partner
, sum(CT) over (partition by company, partner, gl_account, year order by time) CT  
, sum(DT) over (partition by company, partner, gl_account, year order by time) DT   
from (
select time, gl_account, company, partner, sum(CT) CT, sum(DT) DT, substr(time,0,4) year  
from ( 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy where dok_kl_kod_pod = frm_kl_id) Partner
, ks_kwota CT
, null DT
, 'Ct'type
from kgt_ksiegowania, kg_konta, eat_firmy, kgt_dokumenty
where ks_knt_ma = knt_id and ks_frm_id = frm_id 
and (ks_f_symulacja = 'T' or ks_f_zaksiegowany = 'T') 
and ks_dok_id =dok_id
and dok_rdok_kod != 'BO'
and knt_typ = 'B'
and knt_pelny_numer like '234-826572%01'
and to_char(ks_dok_data_zaksiegowania,'YYYY-MM') in  ('2022-01','2022-02','2022-03','2022-04') 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
union all 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy where dok_kl_kod_pod = frm_kl_id) Partner
, null
, ks_kwota
, 'Dt'type
from kgt_ksiegowania, kg_konta, eat_firmy, kgt_dokumenty
where ks_knt_wn = knt_id and ks_frm_id = frm_id 
and (ks_f_symulacja = 'T' or ks_f_zaksiegowany = 'T') 
and ks_dok_id= dok_id
and dok_rdok_kod != 'BO'
and knt_typ = 'B'
and knt_pelny_numer like '234-826572%01'
and to_char(ks_dok_data_zaksiegowania,'YYYY-MM') in  ('2022-01','2022-02','2022-03','2022-04') 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
) group by time, gl_account, company, partner
)


-- check
select sum(ct), sum(dt), sum(ct)-sum(dt) from BPC_STATUTORY where company = 300319 and time = '2022-03' and (gl_account like '70%' or gl_account like '720%' or gl_account like '721%')




-- view Btcv_statutory


create or replace view bpcv_statutory as
select time, gl_account, company, partner, sum(CT) CT, sum(DT) DT  from ( 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy where dok_kl_kod_pod = frm_kl_id) Partner
, ks_kwota CT
, null DT
, 'Ct'type
from kgt_ksiegowania, kg_konta, eat_firmy, kgt_dokumenty
where ks_knt_ma = knt_id and ks_frm_id = frm_id 
and (ks_f_symulacja = 'T' or ks_f_zaksiegowany = 'T') 
and ks_dok_id =dok_id
and dok_rdok_kod != 'BO'
and knt_typ = 'B'
--and to_char(ks_dok_data_zaksiegowania,'YYYY-MM') in  ('2022-01','2022-02','2022-03') 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
union all 
select to_char(ks_dok_data_zaksiegowania,'YYYY-MM')  time 
, knt_pelny_numer GL_ACCOUNT
, frm_id COMPANY
, (select frm_id from eat_firmy where dok_kl_kod_pod = frm_kl_id) Partner
, null
, ks_kwota
, 'Dt'type
from kgt_ksiegowania, kg_konta, eat_firmy, kgt_dokumenty
where ks_knt_wn = knt_id and ks_frm_id = frm_id 
and (ks_f_symulacja = 'T' or ks_f_zaksiegowany = 'T') 
and ks_dok_id= dok_id
and dok_rdok_kod != 'BO'
and knt_typ = 'B'
--and to_char(ks_dok_data_zaksiegowania,'YYYY-MM') in  ('2022-01','2022-02','2022-03') 
and frm_id in (300000,300170,300201,300203,300202,300305,300313,300317,300319,300304,300322,300315,300303,300314)
) group by time, gl_account, company, partner


-- SPR:
-- zaimportowac obototówke do obrotowka_q1 
select company, gl_account, sum(wn1), sum(ma1), sum(wn), sum(ma) from (
select (select frm_nazwa from eat_firmy where frm_id = company) company, gl_account, null wn1, null ma1, sum(dt) wn, sum(ct) ma from bpc_statutory 
where time in ('2022-01','2022-02','2022-03') 
--and company = 300317  
and gl_account like '524%'
group by   company, gl_account
union all
select frmname, account, sum(obrotyWnNar),  sum(obrotyMaNar), null, null  from obrotowka_q1 
where account like '524%'
and account in (select distinct gl_account from bpc_statutory 
where time in ('2022-01','2022-02','2022-03') 
--and company = 300317  
and gl_account like '524%')
group by frmname, account)
group by company, gl_account 
order by 1, 2


select * from bpcv_statutory 
where time = '2022-03' and company = 300319 
and gl_account like '%301-1-01-011100%'


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
