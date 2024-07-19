# Data Exploration Project in SQL - Global Layoffs

#### Project Overview

This project presents a comprehensive exploratory data analysis for a global dataset on corporate layoffs spanning from 2020 to 2023.

#### Data Source

https://github.com/AlexTheAnalyst/MySQL-YouTube-Series/blob/main/layoffs.csv

#### Tools

- MySQL Workbench

#### References

AlexTheAnalyst


### Code

```

SELECT *
FROM layoffs_staging2;

-- On one day 12 000 people have been laid off. Which is 100% of the company:

SELECT MAX(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;

-- Which company's had to lay off all their employees:

SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY total_laid_off DESC;

-- Which company has received the most funding:

SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;

-- Which company had the most total lay offs in total (ordered by company):

SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;

-- Date range for the lay offs:

SELECT MIN(date), MAX(date)
FROM layoffs_staging2;

-- What industry had the most lay offs in total:

SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;

-- Which country had the most lay offs in total:

SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;

-- Which year had the most lay offs:

SELECT YEAR (date), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR (date)
ORDER BY 1 DESC;

-- Which company stage had the most lay offs:

SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;

-- Percentage laid off is not that relevant as we do not have the numbers of how big the company's are.

SELECT company, AVG(percentage_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;

-- Progression of layoff's, shows the total of layoff's for every month:

SELECT SUBSTRING(date, 1, 7) AS month, SUM(total_laid_off) AS total_off
FROM layoffs_staging2
WHERE SUBSTRING(date, 1, 7) IS NOT NULL
GROUP BY month
ORDER BY month ASC;

-- Shows a month by month rolling progression of the people being laid off:

WITH Rolling_Total AS (
    SELECT SUBSTRING(date, 1, 7) AS month, SUM(total_laid_off) AS total_off
    FROM layoffs_staging2
    WHERE SUBSTRING(date, 1, 7) IS NOT NULL
    GROUP BY SUBSTRING(date, 1, 7)
)
SELECT month, total_off,
SUM(total_off) OVER (ORDER BY month) AS rolling_total
FROM Rolling_Total
ORDER BY month ASC;

-- Showing the total layoffs from each company per year 2020-2023:

SELECT company, YEAR(date), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(date)
ORDER BY 3 DESC;

-- 2 CTE's - Ranking companys (from 1 -5) by their total layoffs every year from 2020 - 2023:

WITH Company_Year (company, years, total_laid_off) AS
(
SELECT company, YEAR(date), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(date)
), Company_Year_Rank AS
(
SELECT *, 
DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) as Ranking
FROM Company_Year
WHERE years IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <=5
ORDER BY 4;
;

