1) If the river is present inside the AOI polygon

Spliting area of interest into two polygons by river using postgis:

Requirement:
area of interest polygon in geojson format,
osm data(building,river) in geojson format
use following query to split the area of interest polygon into two sub task polygons

select (St_Dump(ST_Difference(b.geom, a.geom))).geom from "tasking" as a, "aoi_pkr" as b where a.water = 'river';


2) Clustering the splited polygon into further tasks

.intersection of aoi inside the road
 
SELECT ST_Intersection(l.geom,a.geom), l.*
    FROM roadinter a, islington_polygons l
    WHERE ST_Intersects(l.geom, a.geom)
      AND (l.tags->>'building' IS NOT NULL

for k-means clustering
WITH points AS (
WITH buildings AS (
    SELECT * 
	FROM islington_polygons
	WHERE tags->>'building' IS not null
    )
SELECT *, st_centroid(geom) AS centroid_geom
FROM buildings
) 

SELECT st_clusterkmeans(geom, 100) --creates 100 clusters
over () 
AS cid, geom FROM points;




for selecting buildings inside the aoi
SELECT ST_Intersection(l.geom,a.geom), l.*
    FROM roadinter a, islington_polygons l
    WHERE ST_Intersects(l.geom, a.geom)
      AND (l.tags->>'building' IS NOT NULL


for extrating aoi from highway
WITH splitlines AS (
  SELECT lines.* 
  FROM islington_aoi poly, islington_lines lines 
  WHERE ST_Intersects(lines.geom, poly.geom)
    AND (tags->>'highway' IS NOT NULL
         OR tags->>'waterway' IS NOT NULL
         OR tags->>'railway' IS NOT NULL)
)

SELECT (ST_Dump(ST_Polygonize(ST_Node(multi_geom)))).geom
FROM   (
  SELECT ST_Collect(s.geom) AS multi_geom
  FROM splitlines s
) splitpolys;

