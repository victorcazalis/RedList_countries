# RedList_countries
Global shapefile of countries / subnational entities matching Red List (SIS) list of Countries of Occurrence


This shapefile maps the Red List list of countries and subnational entities used in the Countries of Occurrence classification (levels 0 and 1).

*** 
NEWS IN VERSION 2023-1 
I fixed 3 subnational entity names that had typos in version 2022.1: Magellanes (Chile) was written "Magallanes" in 2022.1, Spratly Is. (Disputed territory) were written "Spartly Is." in 2022.1, and Jharkand (India) was written "Jharkhand" in 2022.1.
I also added two columns: lookup gives the lookup of the entity (country or subnational entity) according to the lookup list from SIS Connect; lk_SIS0 gives the lookup of the parent country (ie level 0 for SIS) of each entity.
Columns have been renamed with shorter names: SIS_nm0 for SIS_name0, SIS_nm1 for SIS_name1, lookup and lk_SIS0 

***

General methodology:
The base map used to create this shapefile is the GADM map version 4.1 (as recommended on the Red List Spatial Tools and Data website: https://www.iucnredlist.org/resources/spatialtoolsanddata). It was downloaded from https://gadm.org/index.html on the 25th of August 2022.

Step 1: Countries merge and names
The Red List do not use exactly the same list and names of countries than the GADM. In step 1, I compared the list of countries in GADM and in the Red List SIS to create the crosswalk of countries (Crosswalk_countries_level0.csv) applying the following rules for each line (i.e., each entity from the GADM Adm0 shapefile):
- If the country mentioned in the column COUNTRY of the Adm0 shapefile exactly corresponds to a country in SIS, I copied the name (column COUNTRY) in the SIS_name column.
- If the country mentioned in the column COUNTRY of the Adm0 shapefile points to a country from SIS but with a different name (e.g., Bolivia is called “Bolivia” in GADM but “Bolivia, Plurinational State of” in SIS), I copied the SIS name in the SIS_name column of the crosswalk.
- If several countries in GADM correspond to single country in SIS (e.g., Cyprus has 3 entities in GADM but only one in SIS), I copied the SIS name in the SIS_name column to dissolve later the different entities.
- For all countries, I wrote in the column ‘is_subnat’ a value of 1 for all countries that in SIS are divided at the subnational level for step 2.

Step2: Countries subnational level
I created a shapefile corresponding to a subset of Adm1 from the GADM (i.e. , subnational entities) including all countries that are divided at the national level in the Red List classification (i.e., with a value of ‘1’ in the Crosswalk_countries_level1.csv crosswalk). I created a crosswalk between the column NAME_1 of this subset of Adm1 and the subnational entities existing in SIS using the following rules:
- I wrote SIS name in the column SIS_RegionName the name of the region as named in SIS
- If an entity from Adm1 corresponded to several entities in SIS (e.g., Easter Island + Desventurados Is. + Juan Fernandez Is. all belonged to Valparaiso region of Chile in Adm1 while they are four different entities in SIS), I reported a value of 1 in the column ToSplit to enable manually working with these polygons in Step 3.

Step 3: Splitting subnational entities
For all subnational entities with ToSplit==1, I split polygons in individual entities (using the QGIS function “Multipart to single parts”) and assign to each polygon the corresponding SIS_RegionName.

Step 4: Merging the different shapefiles
I merged the subset of Adm0 with countries that were not split at subnational level in SIS + the subset of Adm1 from countries that are split at subnational level but excluding countries that needed manual splits + the shapefile resulting from the manual split in Step 3. I then dissolved the merged shapefile to keep a single polygon for each value of SIS_name and SIS_RegionName.

Step 5: Manual fixes
- Hong Kong and Macao are level1 in GADM but are level0 in SIS, I changed the attributes of the polygons accordingly
- Coco Islands are attributed to India in GADM (as part of Andaman and Nicobar) but to Myanmar in SIS. I changed the attribution
- Navassa I. is attributed to US Minor Outlying Islands in GADM but belongs to Puerto Rico in SIS. I changed the attribution.
- I manually assigned Clipperton I. as a subnational entity of France.
- A few islands were missing from GADM dataset so I took their polygons from different data source. This was the case of some Ogasawara islands (Japan), Malpelo Island (Colombia), Roxas Alijos (Mexico), and Marion Prince Edward Islands (South Africa).
- I used GADM lower level (2) to split a few small additional entities that are level 1 in SIS: Mahé and Yanam (Pondicherry), Trindade (Goias Brazil), Darjiling (West Benghal, India), Nagorno-Karabakh (this split has been done manually and quite coarsly because the Nagorno-Karabakh borders do not match GADM administrative boundaries).

Problems remaining in version 2023-1:
-	Russia has a second level of subnational entities in SIS, I did not split regions at that level because I was not able to match names in SIS with names in adm1.
-	Some regions are missing in SIS but I still included them in this shapefile to avoid having holes in the map. This includes: Arica y Parinacota (Chile), Nuble (Chile), Los Rios (Chile), Telangana (India). I reported this in the column “Absent_SIS”.

