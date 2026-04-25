## AirAsia — Data Engineer

**Since August 2022.** Started as Data Engineer I, promoted to Data Engineer II in August 2024.

I joined AirAsia as a Data Engineer and have spent the time building and scheduling ETL pipelines that move data between operational systems, vendor sources, and our data warehouse — and increasingly, owning end-to-end projects that put that data in front of the people who make daily operational decisions.

### Selected projects

- **Cabin Crew Operational App.** Integrated data from Google Sheets and BigQuery into a centralised application serving **5000+ flight crew members** — putting key performance metrics directly in the hands of the people they describe. Built the multi-source ingestion pipelines (→ Postgres) and the Airflow DAGs that keep them current.<br><span class="tag">Python</span> <span class="tag">BigQuery</span> <span class="tag">Postgres</span> <span class="tag">Google Sheets</span> <span class="tag">Airflow</span>

- **Automated Training Tracker.** Built an end-to-end workflow that monitors compliance and training schedules for **60+ operations staff** and automatically emails escalations when training is overdue. Eliminated the manual chase entirely.<br><span class="tag">Python</span> <span class="tag">BigQuery</span> <span class="tag">Email automation</span> <span class="tag">Airflow</span>

- **Data Democratisation via LLM Tools.** Spearheaded internal LLM tooling that integrates **6+ core operational modules** (ancillary sales, aircraft availability, etc.) so non-technical staff can self-serve analytics. Built the supporting pipelines and AppScript automation in Google Sheets.<br><span class="tag">Python</span> <span class="tag">LLM</span> <span class="tag">Google Sheets</span> <span class="tag">AppScript</span> <span class="tag">Airflow</span>

- **Aircraft On Ground (AOG) Recovery Foundation.** Built the data foundation feeding clean datasets into the operations research team's heuristic recovery models — directly supporting how AirAsia mitigates critical operational disruptions.<br><span class="tag">SQL</span> <span class="tag">ETL</span> <span class="tag">Airflow</span>

- **NPS Dashboard.** Built the ETL pipeline behind the Net Promoter Score dashboard the customer happiness team uses to track passenger sentiment. Used the Google Translation API for multi-language reviews and Gemini for sentiment + keyword extraction (powering the dashboard's word cloud).<br><span class="tag">Python</span> <span class="tag">Google Translation API</span> <span class="tag">Gemini API</span> <span class="tag">Airflow</span>

- **Ground Operations Dashboards & Bot.** Built an automation that logs into Tableau, screenshots configured charts (e.g. flight load factor), and emails them to stakeholders on a daily cadence for operational decision-making.<br><span class="tag">Python</span> <span class="tag">Tableau</span> <span class="tag">Email automation</span> <span class="tag">Airflow</span>

- **Automated Crew Bag Tag.** Designed the SQL filtering logic — coordinating with the data science and data analytics teams — that the software engineering team consumed to automate the layover check-in process for crew.<br><span class="tag">SQL</span> <span class="tag">Cross-team</span>

- **Navitaire Logging Pipeline.** ETL pipeline that extracts data from BigQuery and lands it in GCS for audit purposes.<br><span class="tag">Python</span> <span class="tag">BigQuery</span> <span class="tag">GCS</span> <span class="tag">Airflow</span>

- **Disaster Recovery (AIMS).** Annual DR exercise for AIMS, a major operational system.<br><span class="tag">DR</span> <span class="tag">Operations</span>

### Core platform work

- **Google Sheets → BigQuery ingestion** with Python ETL on a schedule.
- **Database → Warehouse pipelines** — Postgres, MS SQL, and other operational databases into the data warehouse.
- **Stored Procedure → BigQuery** — execute SP on source, transfer result.
- **SFTP ingestion + reverse exports** — pick up files, transform, load to BigQuery; reverse direction (BigQuery → SFTP CSV) for downstream consumers.
- **Airflow scheduling and monitoring** for all of the above.
- **Documentation** — every pipeline shipped is documented on the company Confluence.
- **LLM experimentation** — exploring how ChatGPT and Gemini speed up ETL development (boilerplate, schema debugging, doc drafting).

Outside of work I also build personal projects to go deeper on the tooling — see [Projects](/projects/).
