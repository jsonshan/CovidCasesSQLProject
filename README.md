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
