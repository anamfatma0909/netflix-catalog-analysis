# Netflix Catalog Analysis — SQL + MySQL Workbench
**Author:** `<Your Name>`  
**Description:** MySQL project analyzing the Netflix titles dataset. Schema, cleaned CSV, import scripts, views, custom queries, and results.

## How to run
1. Open `schema.sql` in MySQL Workbench and execute.
2. Edit `load_data.sql` to point to `netflix_titles_clean.csv` on your machine, then run it.
3. Run `views.sql` and then `queries.sql` (or run custom queries in `extra_queries.sql`).
4. See `/screenshots` for results images.

## Highlights (My custom additions)
- `extra_queries.sql` — co-actor network and top genres by decade
- `results/` — example CSV exports of key queries
- `screenshots/` — result images from MySQL Workbench

## Tools
- MySQL 8.0+, MySQL Workbench, Git, GitHub

## License
MIT — Anam Fatma

-----
sql
WITH actors AS (
  SELECT show_id, TRIM(j.person) AS actor
  FROM netflix_titles t
  JOIN JSON_TABLE(CONCAT('["', REPLACE(IFNULL(t.cast,''), ',', '","'), '"]'),
                  '$[*]' COLUMNS (person VARCHAR(200) PATH '$')) AS j
  WHERE TRIM(j.person) <> ''
),
pairs AS (
  SELECT a1.actor AS actor1, a2.actor AS actor2
  FROM actors a1
  JOIN actors a2 ON a1.show_id = a2.show_id AND a1.actor < a2.actor
)
SELECT actor1, actor2, COUNT(*) AS coappearances
FROM pairs
GROUP BY actor1, actor2
ORDER BY coappearances DESC
LIMIT 20;


---
SELECT COALESCE(rating,'UNRATED') AS rating, ROUND(AVG(duration_value),1) AS avg_minutes
FROM netflix_titles
WHERE type='Movie' AND duration_unit='min' AND duration_value IS NOT NULL
GROUP BY rating
ORDER BY avg_minutes DESC;



