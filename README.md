# Atlanta Analytics dbt Project

Welcome to the `atl_analytics` dbt project! This repository houses the data transformation layer for our BigQuery-based data warehouse, managed by the Data Team at KIPP Atlanta. Our goal is to provide clean, reliable, and accessible data for analytics and reporting, following modern analytics engineering practices as outlined in our [Data Team Data Warehouse Handbook](#handbook-reference) (last modified: Feb 7, 2025).

## Project Overview

- **Name**: `atl_analytics`
- **Version**: 1.0.0
- **Purpose**: Transform raw data from various source systems into structured models for analysis, reporting, and downstream consumption in tools like Tableau and Google Sheets.
- **Environment**: Google BigQuery, managed via dbt Cloud.
- **Repository**: Connected to the `atl_analytics` GitHub repository.

## Getting Started

### Prerequisites
1. **dbt Fundamentals**: Complete the [dbt Fundamentals course](https://courses.getdbt.com/courses/fundamentals) (~5 hours). Use your KIPP Atlanta email to enroll and request access to our BigQuery project (`kae-cloud-kippatl`) from Hannah if needed.
2. **dbt Cloud Access**: After completing the course, ask Hannah to add you to the Data Team dbt Cloud project. Submit a test PR to verify your GitHub connection.
3. **Development Setup**: Each team member gets a sandbox dataset (`dbt_[username]`) for development. Production models live in the `atl_analytics` dataset.

### Key Resources
- **Handbook**: Refer to the [Data Team Data Warehouse Handbook](#handbook-reference) for detailed guidelines on modeling, SQL style, and workflows.
- **BigQuery Project**: `kae-cloud-kippatl`
  - `prod_staging`: Stores raw and lightly enhanced source data (e.g., Illuminate).
  - `atl_analytics`: Production dataset for finalized models.
  - `dbt_[username]`: Development datasets.

## Project Structure

Our dbt project follows a layered model architecture, organized within the `models/` directory:

### Model Layers
1. **Staging** (`stg_` prefix):
   - Built directly from source data (e.g., `prod_staging` dataset).
   - 1:1 with source tables, using `{{ source() }}` macro.
   - Operations: renaming, type casting, basic computations.
   - Materialized as views.
   - Example: `stg_ps__students.sql`

2. **Intermediate** (`int_` prefix):
   - Complex logic (e.g., pivots, joins, aggregations).
   - Not exposed to end users.
   - Materialized as views.
   - Example: `int_core__student_race_grouped.sql`

3. **Dimensional** (`dim_` and `fct_` prefixes):
   - Organized into dimensions (nouns, e.g., students) and facts (verbs, e.g., enrollments).
   - Joins multiple staging/intermediate models.
   - Materialized as tables for performance.
   - Examples: `dim_core__students.sql`, `fct_core__enrollments.sql`

4. **Datasource** (`ds_` prefix):
   - Wide, analysis-ready models combining prior layers.
   - Materialized as tables.
   - Example: `ds_core__student_enrollment.sql`

5. **Reporting** (`rpt_` prefix):
   - Final layer for specific reports/tools (e.g., Tableau).
   - Materialized as tables.
   - Example: `rpt_core__attendance_report.sql`

## Configuration

The project is configured via `dbt_project.yml`:
- **Materializations**: Views for staging/intermediate, tables for dimensional/datasource/reporting.
- **Schemas**: Prod (`atl_analytics`), Dev (`dbt_[username]`).
- **Sources**: Defined in `_sources.yml` files within subdirectories (e.g., `_ps__sources.yml`).
- **Tests**: Severity escalates from `warn` (staging) to `error` (dimensional+).

## SQL Style Guide
- **Keywords**: Lowercase (enforced by SQLFluff).
- **Naming**: Snake_case (e.g., `student_id`).
- **Formatting**: Leading commas, 4-space indents, max 120 characters per line.
- **Best Practices**: Avoid `select *`, use CTEs over subqueries, prefix columns in joins.
- **Tooling**: Use dbt Cloud’s “Lint” and “Fix” buttons to apply SQLFluff rules.

## External Data
- **Google Sheets**: Connect as external tables in BigQuery (see handbook for setup).
- **Google Cloud Storage**: Use `dbt-external-tables` package for external tables from buckets (e.g., `gs://kae-kippatl-prod-lake/`).
  - Run `dbt run-operation stage_external_sources` to create/refresh.

## Development Workflow
1. **Clone Repository**: Work in your own branch off `main`.
2. **Build Models**: Use your `dbt_[username]` dataset for development.
3. **Test**: Add tests in `tests/` and validate locally with `dbt test`.
4. **Document**: Include a comment header in SQL files and business purpose in YAML.
   - Header template:
     ```sql
     /***
         [model_name].sql
         [YYYY]-[MM]-[DD]  [First Name Last Initial]  Created.
         [YYYY]-[MM]-[DD]  [First Name Last Initial]  [Update description]
     ***/


## PR Process
Submit a pull request in dbt Cloud, assign a reviewer, and merge to `main` for production deployment.

## Handbook Reference
This project adheres to the [Data Team Data Warehouse Handbook](#handbook-reference), covering:

- dbt fundamentals and model structure.
- SQL style and best practices.
- Integration with Google Sheets, BigQuery, and Tableau.

For questions or support, reach out to the Data Team via Hannah.