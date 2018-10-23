# WP6
#Patent extraction
--*****************************************************************************************************************************

-- Name: WP6_patenttimelines
-- Author: Maikel Pellens
-- Work Package: 6
-- Goal: Generate public and private knowledge stock, by NACE sector

--  parameters:
		--	EPO applications 
		--	Inventor country of residence (fractional count)
		--	By public and private applicant (fractional count)
		--	By nace sector (fractional count)
		--	Priority date
	
--*****************************************************************************************************************************


-- prep tables. 
-- ****************************************************************************************************************************

WHENEVER SQLERROR EXIT FAILURE	

	-- table with public/private sector for assigning assingnees (source: Van Looy et al. 2006)
	create table mpl_w6_sector (
		PSN_SECTOR varchar2(50),
		PP_SECTOR varchar2(15));
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('GOV NON-PROFIT','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY HOSPITAL','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY HOSPITAL','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('UNIVERSITY','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY GOV NON-PROFIT','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY GOV NON-PROFIT','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY UNIVERSITY','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY UNIVERSITY','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY GOV NON-PROFIT UNIVERSITY','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY GOV NON-PROFIT UNIVERSITY','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('GOV NON-PROFIT UNIVERSITY','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('GOV NON-PROFIT HOSPITAL','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('COMPANY','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('UNIVERSITY HOSPITAL','PUBLIC');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('HOSPITAL','PRIVATE');
	Insert into MPL_W6_SECT_PP (PSN_SECTOR,PP_SECTOR) values ('INDIVIDUAL','PRIVATE');

	-- table with relevant fields for NACE 2/ISIC3 concordance 
	create table mpl_w6_sector (
		sector_txt varchar2(50) not null,
		nace2 varchar2(15) not null, 
		isic3 varchar2(15) not null);
		
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Chemicals and Pharmaceutical', '20', 'C42');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Chemicals and Pharmaceutical', '21', 'C42');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Office accounting and medical precision', '26', 'C73, C76');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Electrical equipment n.e.c.', '27', 'C74');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Machinery n.e.c.', '28', 'C72');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Motorvehicles and trucks', '29', 'C77');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Other transport equipment (includes spacecraft', '30', 'C78');
	insert into mpl_w6_sector (sector_txt, nace2, isic3) values ('Computer services', '62', 'LMN1');

	-- table with relevant countries
	create table mpl_w6_country (
		ctry3 varchar2(3) not null, 
		ctry2 varchar2(2) not null, 
		ctryfull varchar(25) not null
		);
		
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ARG', 'AR', 'ARGENTINA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('AUS', 'AU', 'AUSTRALIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('AUT', 'AT', 'AUSTRIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('BEL', 'BE', 'BELGIUM');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('CAN', 'CA', 'CANADA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('CHE', 'CH', 'SWITZERLAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('CHL', 'CL', 'CHILE');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('CHN', 'CN', 'CHINA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('CZE', 'CZ', 'CZECH REPUBLIC');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('DEU', 'DE', 'GERMANY');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('DNK', 'DK', 'DENMARK');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ESP', 'ES', 'SPAIN');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('EST', 'EE', 'ESTONIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('FIN', 'FI', 'FINLAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('FRA', 'FR', 'FRANCE');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('GBR', 'GB', 'UNITED KINGDOM');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('GRC', 'GR', 'GREECE');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('HUN', 'HU', 'HUNGARY');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('IRL', 'IE', 'IRELAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ISL', 'IS', 'ICELAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ISR', 'IL', 'ISRAEL');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ITA', 'IT', 'ITALY');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('JPN', 'JP', 'JAPAN');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('KOR', 'KR', 'KOREA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('LUX', 'LU', 'LUXEMBOURG');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('MEX', 'MX', 'MEXICO');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('NLD', 'NL', 'NETHERLANDS');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('NOR', 'NO', 'NORWAY');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('NZL', 'NZ', 'NEW ZEALAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('POL', 'PL', 'POLAND');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('PRT', 'PT', 'PORTUGAL');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ROU', 'RO', 'ROMANIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('RUS', 'RU', 'RUSSIAN FEDERATION');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('SGP', 'SG', 'SINGAPORE');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('SVK', 'SK', 'SLOVAKIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('SVN', 'SI', 'SLOVENIA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('SWE', 'SE', 'SWEDEN');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('TUR', 'TR', 'TURKEY');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('USA', 'US', 'UNITED STATES OF AMERICA');
	insert into mpl_w6_country (ctry3, ctry2, ctryfull) values ('ZAF', 'ZA', 'SOUTH AFRICA');

	
--- Calculations
-- ****************************************************************************************************************************

-- 1. EPO appln_id and priority year
create table MPL_W6_EPO_APPLN 
as 
select distinct appln_id, EARLIEST_FILING_YEAR as pyear from tls201_appln a where upper(trim(a.appln_auth)) = 'EP' and upper(trim(a.appln_kind)) = 'A';
create index ix_mpl_w6_epo_appln on mpl_w6_epo_appln (appln_id);

-- 2. Table with inventors and country or residence
create table MPL_W6_EPO_INV
as
SELECT appln_id, person_ctry_code as country, SUM(tot_in_ctry/tot_in_patent) AS fractional_count
FROM
 (SELECT  t.appln_id,  NVL(t1.person_ctry_code, '') AS person_ctry_code,  NVL(t1.tot_in_ctry, 1) AS tot_in_ctry,  NVL(t2.tot_in_patent, 1) AS tot_in_patent
 FROM  MPL_W6_EPO_APPLN t
 LEFT OUTER JOIN
 --> Accounts for missing inventor references in tls207_pers_appln table
 (SELECT  a.appln_id, b.person_ctry_code, COUNT(b.person_id) AS tot_in_ctry  FROM tls207_persappln a INNER JOIN tls206_person b ON a.person_id = b.person_id
 WHERE  a.invt_seq_nr > 0  GROUP BY a.appln_id, person_ctry_code
 --> Compiles country-level count of inventors per patent
 ) t1 ON t.appln_id = t1.appln_id
 LEFT OUTER JOIN
 (SELECT  appln_id, MAX(invt_seq_nr) AS tot_in_patent  FROM  tls207_persappln  GROUP BY appln_id HAVING MAX(invt_seq_nr) > 0
 --> Compiles total count of inventors per patent
 ) t2 ON t.appln_id = t2.appln_id
 ) 
GROUP BY appln_id, person_ctry_code
ORDER BY appln_id, SUM(tot_in_ctry/tot_in_patent) DESC;
create index ix_mpl_w6_epo_inv on mpl_w6_epo_inv (appln_id);

-- 3. assign public-private 
create table MPL_W6_EPO_asg
as
SELECT appln_id, pp_sector, SUM(tot_in_sector/tot_in_patent) AS fractional_count
FROM
 (SELECT  t.appln_id,  NVL(t1.pp_sector, '') AS pp_sector,  NVL(t1.tot_in_sector, 1) AS tot_in_sector,  NVL(t2.tot_in_patent, 1) AS tot_in_patent
 FROM  MPL_W6_EPO_APPLN t
 LEFT OUTER JOIN
 --> Accounts for missing inventor references in tls207_pers_appln table
 (	SELECT  a.appln_id, c.pp_sector, COUNT(b.person_id) AS tot_in_sector 
	FROM tls207_persappln a INNER JOIN tls206_person b ON a.person_id = b.person_id inner join mpl_w6_sect_pp c on upper(trim(b.psn_sector)) = upper(trim(c.psn_sector))
	WHERE  a.applt_seq_nr > 0  GROUP BY a.appln_id, c.pp_sector
 --> Compiles country-level count of inventors per patent
 ) t1 ON t.appln_id = t1.appln_id
 LEFT OUTER JOIN
 (SELECT  appln_id, MAX(applt_seq_nr) AS tot_in_patent  FROM  tls207_persappln  GROUP BY appln_id HAVING MAX(applt_seq_nr) > 0
 --> Compiles total count of assignees per patent
 ) t2 ON t.appln_id = t2.appln_id
 ) 
GROUP BY appln_id, pp_sector
ORDER BY appln_id, SUM(tot_in_sector/tot_in_patent) DESC;
create index ix_mpl_w6_epo_asg on mpl_w6_epo_asg (appln_id); 

-- 4. nace weights of appln
	
	-- table with weight of relevant appln_ids per class
	-- here we do not need the left outer join because we are subsetting anyway...
	create table mpl_w6_epo_nace 
	as 
	select a.appln_id, c.isic3, sum(b.weight_num) as fractional_count
	from mpl_w6_epo_appln a, tls229_applnnace2 b,mpl_w6_sector c
	where a.appln_id = b.appln_id and upper(trim(substr(b.nace2_code, 1, 2))) = upper(trim(c.nace2))
    group by a.appln_id, c.isic3
    order by a.appln_id, c.isic3;
	create index ix_mpl_w6_epo_nace on mpl_w6_epo_nace (appln_id);
	
	
-- 5. generate timelines
		-- per country
	create table mpl_w6_epo_out_country
	as
	select year, ctry3, round(sum(fractional_count), 4) as epo_flow
	from (
	select distinct a.appln_id, a.pyear as year, c.ctry3, inv.fractional_count
	from mpl_w6_epo_appln a inner join mpl_w6_epo_inv inv on a.appln_id = inv.appln_id inner join mpl_w6_epo_asg asg on a.appln_id = asg.appln_id
		left outer join mpl_w6_epo_nace nace on a.appln_id = nace.appln_id inner join mpl_w6_country c on inv.country = c.ctry2 
	where a.pyear >= 1978 and a.pyear <= 2013
	)
	group by year, ctry3
	order by year asc, ctry3 asc;

		-- time trend per country, public/private 
	create table mpl_w6_epo_out_country_pp
	as
	select year, ctry3, pp_sector, round(sum(fractional_count), 4) as epo_flow_pp
	from (
		select distinct a.appln_id, a.pyear as year, c.ctry3, pp_sector, inv.fractional_count*asg.fractional_count as fractional_count -- re-weighing
		-- this gets tricky because nationality is inventor based and sector is assignee based, so we need to split evenly.
	from mpl_w6_epo_appln a inner join mpl_w6_epo_inv inv on a.appln_id = inv.appln_id inner join mpl_w6_epo_asg asg on a.appln_id = asg.appln_id
		left outer join mpl_w6_epo_nace nace on a.appln_id = nace.appln_id inner join mpl_w6_country c on inv.country = c.ctry2 
	where a.pyear >= 1978 and a.pyear <= 2013
	)
	group by year, ctry3, pp_sector
	order by year asc, ctry3 asc, pp_sector asc;
	
		-- per sector 
	create table mpl_w6_epo_out_country_isic
	as
	select year, ctry3, isic3, round(sum(fractional_count), 4) as epo_flow_isic
	from (
		select distinct a.appln_id, a.pyear as year,  c.ctry3,nace.isic3, inv.fractional_count*nace.fractional_count as fractional_count -- re-weighing
		-- this gets tricky because nationality is inventor based and sector is assignee based, so we need to split evenly.
		from mpl_w6_epo_appln a inner join mpl_w6_epo_inv inv on a.appln_id = inv.appln_id inner join mpl_w6_epo_asg asg on a.appln_id = asg.appln_id
		inner join mpl_w6_epo_nace nace on a.appln_id = nace.appln_id inner join mpl_w6_country c on inv.country = c.ctry2  
		where a.pyear >= 1978 and a.pyear <= 2013 -- inner join on nace because the weight for these is null and they show up as null in the results table anyway. 
	)
	group by year, ctry3, isic3
	order by year asc, ctry3 asc, isic3 asc;
	
		-- and lastly, per public private and sector
	create table mpl_w6_epo_out_country_pp_isic
	as
	select year, ctry3, isic3, pp_sector, round(sum(fractional_count), 4) as epo_flow_pp_isic
	from (
		select distinct a.appln_id, a.pyear as year,  c.ctry3,nace.isic3, pp_sector, inv.fractional_count*asg.fractional_count*nace.fractional_count as fractional_count -- re-weighing
		-- this gets tricky because nationality is inventor based and sector is assignee based, so we need to split evenly.
		from mpl_w6_epo_appln a inner join mpl_w6_epo_inv inv on a.appln_id = inv.appln_id inner join mpl_w6_epo_asg asg on a.appln_id = asg.appln_id
		inner join mpl_w6_epo_nace nace on a.appln_id = nace.appln_id inner join mpl_w6_country c on inv.country = c.ctry2  
		where a.pyear >= 1978 and a.pyear <= 2013 -- inner join on nace because the weight for these is null and they show up as null in the results table anyway. 
	)
	group by year, ctry3, isic3, pp_sector
	order by year asc, ctry3 asc, isic3 asc, pp_sector asc;
	
