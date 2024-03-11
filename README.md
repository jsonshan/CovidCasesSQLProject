# COVID-19 Data Exploration

## Introduction
This SQL script explores COVID-19 data, utilizing various skills such as Joins, CTEs, Temp Tables, Windows Functions, Aggregate Functions, and Converting Data Types.

## Table of Contents
- [Select Initial Data](#select-initial-data)
- [Total Cases vs Total Deaths](#total-cases-vs-total-deaths)
- [Total Cases vs Population](#total-cases-vs-population)
- [Countries with Highest Infection Rate](#countries-with-highest-infection-rate)
- [Countries with Highest Death Count](#countries-with-highest-death-count)
- [Continent-wise Death Count](#continent-wise-death-count)
- [Global Numbers](#global-numbers)
- [Total Population vs Vaccinations](#total-population-vs-vaccinations)
- [Using CTE](#using-cte)
- [Using Temp Table](#using-temp-table)
- [Creating View for Visualization](#creating-view-for-visualization)


## Select Initial Data
```sql
-- Select Data that we are going to be starting with

Select Location, date, total_cases, new_cases, total_deaths, population
From PortfolioProject..CovidDeaths
Order by 1,2
```


## Total Cases vs Total Deaths
```sql
-- Shows likelihood of dying if you contract COVID-19 in your country

Select Location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
From PortfolioProject..CovidDeaths
Where location like '%states%'
and continent is not null
Order by 1,2
```


## Total Cases vs Population
```sql
-- Shows what percentage of the population is infected with COVID-19

Select Location, date, total_cases, population, (total_cases/population)*100 as TotalCasesPercentage
From PortfolioProject..CovidDeaths
Where location like '%state%'
and continent is not null
```

## Countries with Highest Infection Rate
```sql
-- Looking at Countries with Highest Infection Rate compared to Population

Select Location, Population, MAX(total_cases) as HighestInfectionCount, Max((total_cases / population)) * 100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%state%'
Group by Location, Population
Order by PercentPopulationInfected DESC
```


## Countries with Highest Death Count
```sql
-- Showing Countries with Highest Death Count per Population

Select Location, MAX(cast(total_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%state%'
Where continent is not null 
Group by Location
Order by TotalDeathCount DESC
```


## Continent-wise Death Count
```sql
-- LET'S BREAK THINGS DOWN BY CONTINENT

-- Showing continents with the highest death count per population
Select location, MAX(cast(total_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%state%'
Where continent is null 
Group by location
Order by TotalDeathCount DESC
```


## Global Numbers
```sql
Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths
, SUM(cast(new_deaths as int))/SUM(new_cases) * 100 as DeathPercentage
FROM PortfolioProject..CovidDeaths
Where continent is not null
--Group by date
Order by 1,2
```


## Total Population vs Vaccinations
```sql
-- Looking at Total Population vs Vaccinations

Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population) * 100
From PortfolioProject..CovidDeaths as dea
Join PortfolioProject..CovidVaccinations as vac
	On dea.location = vac.location
	and dea.date = vac.date
Where dea.continent is not null
Order by 2,3
```


## Using CTE
```sql
With PopvsVac (Continent, Location, Date, Population, New_vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population) * 100
From PortfolioProject..CovidDeaths as dea
Join PortfolioProject..CovidVaccinations as vac
	On dea.location = vac.location
	and dea.date = vac.date
Where dea.continent is not null
--Order by 2,3
)

SELECT *, (RollingPeopleVaccinated / Population) * 100 as RollingVaccinatedPopulation
From PopvsVac
```

## Using Temp Table
```sql
DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, dea.date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population) * 100
From PortfolioProject..CovidDeaths as dea
Join PortfolioProject..CovidVaccinations as vac
	On dea.location = vac.location
	and dea.date = vac.date
--Where dea.continent is not null
--Order by 2,3

Select *, (RollingPeopleVaccinated/Population) * 100
From #PercentPopulationVaccinated
```


## Creating View for Visualization
```sql
Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.location Order by dea.location, 
dea.date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population) * 100
From PortfolioProject..CovidDeaths as dea
Join PortfolioProject..CovidVaccinations as vac
	On dea.location = vac.location
	and dea.date = vac.date
Where dea.continent is not null
--Order by 2,3

Select *
From PercentPopulationVaccinated
```
