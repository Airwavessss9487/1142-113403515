# Peer Review Report

> **Instructions:** Complete this form **individually and independently**.
> Do not discuss your ratings with teammates before submitting.
> Submit via EEClass as a **separate, confidential submission** — not in the shared team repo.
> Your teammates will not see this report.
>
> Reference the team's `WORK_ALLOCATION_TEMPLATE.md` when completing this form.

---

## Your Details

| Field | Your answer |
|-------|------------|
| Full Name | 施竑宇 |
| Student ID | 113403515 |
| Team ID | 18 |
| Date submitted | 2026-06-12 |

---

## Rating Scale

| Rating | Meaning |
|--------|---------|
| **5** | Exceeded expectations — delivered more than agreed; helped teammates; consistently high quality |
| **4** | Met expectations fully — delivered exactly what was agreed; on time; good quality |
| **3** | Mostly met expectations — minor shortfalls; one or two items completed late or with help |
| **2** | Partially met expectations — noticeable gaps; teammates had to cover some tasks |
| **1** | Did not meet expectations — significant tasks left incomplete; very limited contribution |

---

## Section A — Self-Assessment

### A1. What did you personally implement?

List the specific tasks, functions, files, or document sections that you were the primary author of.
Be specific (e.g., "I designed all 12 tables in schema.sql and implemented query_national_rail_availability and execute_booking").

> *Your answer:*
I was the primary author or primary owner of the following parts of the TransitFlow project:

* I was the primary owner of **Task 3 — PostgreSQL seeding (`skeleton/seed_postgres.py`)**. I worked on the data seeding process that loads the mock JSON data into PostgreSQL and validated that the seed process could run successfully.

* During PostgreSQL seeding and integration testing, I found that the `feedback` table was not properly created in the official schema, which caused the feedback seed data to fail or required temporary table creation inside the seeding script. I helped complete this missing schema component by moving the `feedback` table creation logic into `databases/relational/schema.sql` and removing the temporary table creation logic from `skeleton/seed_postgres.py`. I also used helper scripts such as `check_tables.py`, `init_schema.py`, and `reset_db.py` to verify that the required PostgreSQL tables were created correctly before running the seed process.

* I was the primary contributor for the **Vector Policy / RAG seed data workflow**. I worked on the policy JSON files used by pgvector, including:

  * `train-mock-data/booking_rules.json`
  * `train-mock-data/refund_policy.json`
  * `train-mock-data/ticket_types.json`
  * `train-mock-data/travel_policies.json`

* I was responsible for testing and validating the vector policy seeding process by running:

  * `python skeleton/seed_vectors.py`

* I worked on making sure the policy documents could be embedded into pgvector and retrieved through vector similarity search.

* I was responsible for documenting vector policy seeding progress in `AI_SESSION_CONTEXT.md`, including the commit:

  * `c589b19` — recorded vector policy seeding in `AI_SESSION_CONTEXT.md`

* I was the primary author of **Design Document Section 4 — Vector / RAG Design**. In this section, I explained:

  * how policy documents are converted into vector embeddings
  * how pgvector retrieves policy documents using similarity search
  * why vector search is suitable for policy questions
  * how cosine similarity is used in the RAG retrieval process
 
* I was a co-primary owner of **Task 6 — Optional Extension**, especially the UI-facing history feature. My main work was related to the **My History** panel and user history integration.

* I implemented or worked on `do_show_history(current_user_email)` so that a logged-in user could view their travel history in the Gradio UI.

* I tested `query_user_bookings(user_email)` to confirm that the system could retrieve both national rail booking history and metro travel history for the logged-in user.

* I helped make the **My History** feature visible through the UI, so the optional extension was not only stored in the database but also accessible to the user.

* I contributed to the Task 6 documentation and related explanation files, including:

  * `README.md`
  * `TASK6.md`

* I created or used local helper scripts for database initialization and testing, including:

  * `check_tables.py`
  * `init_schema.py`
  * `reset_db.py`
  * `skeleton/ensure_schema_snippet.py`

* I pushed work related to my part to the remote repository, including commits such as:

  * `e55f346` — seeded vector policy documents
  * `c589b19` — recorded vector policy seeding
  * `eae41dd` — saved local schema helper progress
  * `cc58fe4` — merged current local changes into `main`

Overall, my primary contribution was focused on PostgreSQL seeding, fixing schema/seeding gaps discovered during integration testing, vector policy / RAG data preparation, Design Document Section 4, and the Task 6 My History UI integration.

---

### A2. What challenges did you face?

Describe any technical or collaboration difficulties you personally encountered and how you resolved them.

> *Your answer:*
I personally encountered several technical and collaboration difficulties during the project, especially while working on PostgreSQL seeding, vector policy/RAG integration, UI testing, and integration testing.

1. **Missing table during PostgreSQL seeding**
   When I ran the PostgreSQL seeding workflow, I found that some seed data could not be inserted correctly because the `feedback` table was not properly created in the official schema. This caused the seed process to fail or required temporary table creation inside `skeleton/seed_postgres.py`. I resolved this by identifying the missing schema component, moving the `feedback` table creation logic into `databases/relational/schema.sql`, and verifying the database tables again before running the seed script.

2. **Vector policy JSON encoding issue**
   While working on the policy JSON files, I encountered JSON loading problems caused by file encoding, especially UTF-8 BOM. Some policy files could not be read correctly by `seed_vectors.py`, which caused vector seeding to fail. I resolved this by checking the file-reading logic and making sure the policy JSON files could be loaded correctly before being embedded into pgvector.

3. **Testing whether policy documents were actually searchable**
   After updating the policy JSON files and running `python skeleton/seed_vectors.py`, I still needed to confirm whether the new policy documents could actually be retrieved. I resolved this by testing vector search directly in Python: I generated query embeddings and called `query_policy_vector_search()` to check whether pgvector returned the expected policy documents.

4. **LLM tool routing was sometimes unreliable**
   During UI testing, the assistant did not always call the correct policy search tool. For example, delay compensation questions were sometimes answered using unrelated fare calculation logic instead of the refund policy document. I resolved this by testing the policy retrieval separately from the UI, checking whether pgvector itself returned the correct results, and then reviewing the agent/tool routing behavior.

5. **Git branch and commit management**
   I encountered Git workflow issues, such as running Git commands outside the repository folder and getting errors like `fatal: not a git repository`. I resolved this by making sure I was inside the correct project directory before running Git commands, checking my current branch with `git branch`, and using `git log` / `git show` to verify which commits belonged to my work.

6. **Database reset after pulling new team changes**
   During integration testing, I learned that pulling the latest `main` branch did not automatically update the local Docker database. Even after code files were updated, the old PostgreSQL volume could still contain the previous schema and data. I resolved this by resetting the Docker volumes when needed using `docker compose down -v && docker compose up -d`, then rerunning `seed_postgres.py`, `seed_neo4j.py`, and `seed_vectors.py`.

7. **Distinguishing database errors from LLM or UI errors**
   Sometimes the database query worked correctly when tested directly in Python, but the UI still returned an incorrect answer. This made debugging difficult because the error could come from PostgreSQL/pgvector, the query function, the agent tool selection, the LLM response, authentication state, or the UI. I resolved this by testing each layer separately: first the database seed, then the query function, then the vector search, and finally the full Gradio UI flow.

Overall, the main difficulty was not only implementing individual files, but also making sure PostgreSQL, pgvector, the LLM agent, Git workflow, Docker environment, and Gradio UI worked together correctly during integration testing.

---

### A3. Self-rating

| Criterion | Rating (1–5) | Justification (1–2 sentences) |
|-----------|-------------|-------------------------------|
| I delivered the tasks assigned to me in the work allocation | 5 | I completed my assigned work on PostgreSQL seeding, vector policy/RAG data preparation, and Task 6 UI history integration. I also helped fix schema/seeding issues discovered during integration testing. |
| The quality of my work was satisfactory | 5 | My work was tested through seed scripts, direct Python vector search, and UI testing. I also checked that the policy documents could be embedded into pgvector and retrieved correctly. |
| I communicated well and kept the team informed | 5 | I documented my work in files such as AI_SESSION_CONTEXT.md, TEAM.md, README.md, and TASK6.md. I also recorded issues and fixes so that teammates could understand the seeding and integration status. |
| I met deadlines agreed within the team | 5 | I completed my assigned seeding, vector policy, and UI testing tasks before final submission. Some integration issues required extra debugging time, but I resolved them and pushed the relevant work to the repository. |
| **Overall self-rating** | 5 | Overall, I contributed substantially to the seeding, vector/RAG, integration testing, and Task 6 UI history parts of the project. I also helped resolve technical issues that affected the full system workflow. |

---

### A4. Estimated contribution percentage

What percentage of the total team effort do you estimate you personally contributed?

> My estimated contribution: **__35__%**

---

## Section B — Peer Assessments

Complete one subsection per teammate. Add or remove subsections to match your team size.
If your team has 2 members, complete B1 only. If 3 members, complete B1 and B2.

---

### B1. Assessment of Teammate 1

| Field | Your answer |
|-------|------------|
| Teammate's full name | 張茗崴 |
| Teammate's student ID | 113403547 |

#### What did this teammate deliver?

List the tasks, functions, files, or document sections that this teammate was the primary author of,
based on what you observed during the project (compare against the work allocation).

> *Your answer:*
張茗崴 was the primary author or primary owner of the following parts of the TransitFlow project:

* 張茗崴 was the primary owner of **Task 1 — Relational schema design (`databases/relational/schema.sql`)**. He designed and implemented the PostgreSQL relational schema, including major tables such as users, national rail bookings, seat layouts, metro travel history, payments, and feedback.

* He added and refined important relational tables in `databases/relational/schema.sql`, including:

  * `national_rail_seat_layouts`
  * `metro_travel_history`
  * `payments`
  * `feedback`
  * updates to `national_rail_bookings`, including `seat_id` and `ticket_type`

* He was the primary owner of **Task 2a — Core availability & fare queries**. He implemented core PostgreSQL query logic in `databases/relational/queries.py`, including national rail availability, metro/national rail schedule lookup, and dynamic fare calculation.

* He implemented or completed required relational query functions in `databases/relational/queries.py`, including:

  * `query_national_rail_availability`
  * `query_available_seats`
  * fare calculation logic
  * user record and booking-related queries

* He was the primary owner of **Task 2b — Seat & user queries**. He implemented the logic for checking available seats, retrieving user-related records, and supporting booking/user history queries.

* He was the primary owner of **Task 2c — Write operations**. He implemented booking and cancellation write operations, including:

  * `execute_booking`
  * `execute_cancellation`

* In `execute_booking`, he implemented seat checking, `"any"` automatic seat selection, dynamic fare calculation using `stops_in_order`, and insertion of payment records into the `payments` table.

* In `execute_cancellation`, he implemented cancellation logic using `FOR UPDATE` row locking to prevent concurrency issues, updated booking status, and calculated refunds based on refund policy rules.

* He implemented transaction handling for write operations by using `try...except`, `conn.commit()`, and `conn.rollback()` to ensure database consistency when multiple SQL operations are executed together.

* He implemented the **Loyalty Points** database update logic, including SQL logic such as:

  * `UPDATE users SET loyalty_points = loyalty_points + %s WHERE user_id = %s`

* He was a co-primary owner of **Task 2d — Authentication queries**. He implemented authentication-related logic such as `register_user` and `login_user`.

* He upgraded the authentication system from `hashlib.sha256` with salt to **bcrypt**, improving password and security-answer hashing. This included changes in:

  * `databases/relational/queries.py`
  * `create_user.py`
  * `requirements.txt`

* He added `bcrypt>=4.1.2` to `requirements.txt` to support secure password hashing.

* He fixed an Agent login crash in `skeleton/agent.py` caused by schema normalization, where `full_name` was split into `first_name` and `surname`.

* He helped improve code quality by adding explanatory `[WHY]` comments in `databases/relational/queries.py`, explaining important design decisions such as:

  * why JSONB arrays were processed in Python
  * why `FOR UPDATE` row locking was used
  * why bcrypt was used for password security
  * why cosine distance was used for vector similarity

* He was a co-primary owner of **Task 6 — Optional Extension**, especially the **Loyalty Points** feature.

* For Task 6, he implemented the backend and Agent-side Loyalty Points integration, including registering a `get_loyalty_points` tool in `skeleton/agent.py`.

* He added deterministic fallback logic in `skeleton/agent.py` so that the assistant could answer user questions about loyalty point balance more reliably, even when the local Llama 3.2 1B model had weak tool-calling performance.

* He helped complete the end-to-end flow for the Loyalty Points feature:

  * UI → Agent → Database → Agent → UI

* He was the primary author of **Design Document Section 1 — ER Diagram**. He generated and integrated the professional ER diagram `資料庫ER圖.png`, showing primary keys, foreign keys, and 1:N relationships.

* He was the primary author of **Design Document Section 2 — Normalisation Justification**. He explained the relational schema normalization decisions, including 2NF/3NF design and password security considerations.

* He contributed heavily to the design document by documenting relational database design, ER relationships, schema normalization, bcrypt security, and static code rationale.

* He completed major documentation work in `DESIGN_DOCUMENT.md`, including relational database explanation, ER diagram integration, normalization justification, security design, and implementation evidence.

* He was a co-primary author of **Design Document Section 7 — Optional Extension**, especially the Loyalty Points part, including motivation, SQL examples, and testing evidence.

* He validated the relational database implementation by confirming that all required relational query functions had no remaining `NotImplementedError`.

* He validated transaction management by checking that successful operations were committed and failed operations were rolled back correctly.

* He confirmed that the Loyalty Points feature could correctly update user point balances in the database.

Overall, 張茗崴’s primary contribution focused on PostgreSQL relational schema design, relational query implementation, booking and cancellation write operations, authentication and bcrypt security, Loyalty Points backend/Agent integration, and the relational database sections of the design document.

#### Did their actual contribution match the agreed work allocation?

> *Your answer (Yes / Mostly / Partially / No — with explanation):*
yes
#### Peer rating for this teammate

| Criterion | Rating (1–5) | Justification (1–2 sentences) |
|-----------|-------------|-------------------------------|
| Delivered the tasks assigned in the work allocation | 5 | He completed the assigned relational schema, query functions, write operations, authentication logic, and Loyalty Points integration with clear ownership and strong implementation. |
| Quality of their work was satisfactory | 5 | His work was complete, well-structured, and included important fixes such as transaction handling, bcrypt authentication, refund logic, and schema improvements. |
| Communicated well and kept the team informed | 5 | He documented major implementation updates, bug fixes, and validation results clearly so the team could understand the project status and continue integration smoothly. |
| Met deadlines agreed within the team | 5 | He completed the core PostgreSQL and Task 6 backend work in time for integration, testing, and final documentation. |
| **Overall rating for this teammate** | 5 | Overall, he made a major contribution to the project by leading the relational database implementation, authentication system, Loyalty Points feature, and related documentation. |

#### Estimated contribution percentage for this teammate

> My estimate of their contribution: **___40_%**

---

### B2. Assessment of Teammate 2

| Field | Your answer |
|-------|------------|
| Teammate's full name | 吳絃紘 |
| Teammate's student ID | 113403302 |

#### What did this teammate deliver?

> *Your answer:*
吳絃紘 was the primary author or primary owner of the following parts of the TransitFlow project:

* 吳絃紘 was the primary owner of **Task 4 — Neo4j graph design and seeding**.

* He implemented Neo4j graph seeding in `skeleton/seed_neo4j.py`.

* He created graph nodes from the mock JSON station data, including:

  * `Station`
  * `MetroStation`
  * `NationalRailStation`

* He designed and created the graph topology for the transit network using Neo4j nodes and relationships.

* He created metro rail connections using `METRO_LINK` relationships.

* He created national rail connections using `RAIL_LINK` relationships.

* He created cross-network interchange connections using bidirectional `INTERCHANGE_TO` relationships.

* He added important relationship properties to the graph edges, including:

  * `line`
  * `travel_time_min`
  * `distance`
  * `standard_fare_usd`
  * `first_fare_usd`

* He was the primary owner of **Task 5 — Neo4j query functions**.

* He implemented all required Neo4j graph query functions in `databases/graph/queries.py`, including:

  * `query_shortest_route`
  * `query_cheapest_route`
  * `query_alternative_routes`
  * `query_interchange_path`
  * `query_delay_ripple`
  * `query_station_connections`

* He implemented shortest-route logic so the assistant could find the fastest route between stations using graph traversal.

* He implemented cheapest-route logic so the assistant could compare route cost using graph edge fare properties.

* He implemented alternative-route logic so the assistant could suggest routes when a station was closed or unavailable.

* He implemented interchange-path logic so the assistant could handle cross-network routing between metro and national rail stations.

* He implemented delay-ripple logic so the system could return stations affected by a delay propagation scenario.

* He implemented station-connection queries so the system could return direct or nearby connected stations.

* He validated the Neo4j implementation by running `python -m py_compile` using the project `.venv` Python environment.

* He successfully seeded the Neo4j database after Docker Desktop was started.

* He ran smoke tests for the main graph functions, including:

  * shortest route
  * cheapest route
  * interchange routing
  * alternative routes
  * delay ripple
  * station connections
  * not-found station cases

* He confirmed that the Neo4j portion had no remaining `TODO` or `NotImplementedError`.

* He was the primary author of **Design Document Section 3 — Graph Database Design Rationale**.

* In Design Document Section 3, he explained why a graph database is suitable for transit routing problems.

* He documented the graph model design, including station nodes, rail link relationships, and interchange relationships.

* He explained the advantage of using graph traversal algorithms for shortest routes, cheapest routes, alternative routes, and cross-network transfer planning.

Overall, 吳絃紘’s primary contribution focused on Neo4j graph design, graph seeding, Cypher query implementation, route-finding functions, cross-network interchange logic, delay ripple logic, and the graph database section of the design document.

#### Did their actual contribution match the agreed work allocation?

> *Your answer (Yes / Mostly / Partially / No — with explanation):*
yes
#### Peer rating for this teammate

| Criterion | Rating (1–5) | Justification (1–2 sentences) |
|-----------|-------------|-------------------------------|
| Delivered the tasks assigned in the work allocation | 5 | He completed the assigned Neo4j graph seeding and query functions, including shortest route, cheapest route, interchange routing, delay ripple, and station connection queries. |
| Quality of their work was satisfactory | 5 | His graph implementation was complete and well-tested, with proper nodes, relationships, edge properties, and Cypher query logic. |
| Communicated well and kept the team informed | 5 | He clearly reported his Neo4j implementation progress, validation steps, and testing results so the team could understand the graph database status. |
| Met deadlines agreed within the team | 5 | He completed the Neo4j graph design, seeding, and query functions in time for integration and final testing. |
| **Overall rating for this teammate** | 5 | Overall, he made a strong contribution by leading the Neo4j graph database implementation and supporting the project’s routing and transfer functions. |

#### Estimated contribution percentage for this teammate

> My estimate of their contribution: **__25__%**

---

## Section C — Contribution Percentage Summary

All members (including yourself) must sum to 100%.

| Member | Your estimated % | Notes |
|--------|----------------|-------|
| Yourself | 35% |  I contributed to PostgreSQL seeding, vector policy/RAG data preparation, Design Document Section 4, Task 6 My History UI integration, and integration testing. |
| Teammate 1 | 40% | He led the PostgreSQL schema design, relational query functions, write operations, authentication system, Loyalty Points backend/Agent integration, and related documentation. |
| Teammate 2 | 25% | He led the Neo4j graph design, graph seeding, Cypher query functions, route planning logic, and graph database design documentation. |
| **Total** | **100%** | |

---

## Section D — Overall Team Reflection

### D1. What went well in the team's collaboration?

> *Your answer (2–4 sentences):*
The team collaboration went well because each member had a clear technical responsibility: PostgreSQL relational database, Neo4j graph database, and vector/RAG plus UI integration. We were able to work on separate components first and then combine them through seeding scripts, agent tools, and UI testing. Team members also shared implementation updates and validation results, which helped us identify integration issues more quickly.
---

### D2. What would you do differently if you did this project again?

> *Your answer (2–4 sentences):*
If I did this project again, I would set clearer integration checkpoints earlier, especially after schema changes, seed script updates, or agent tool modifications. I would also record each member’s modified files, functions, and commits throughout the project instead of organizing them only near the end. This would make the final work allocation, peer review, and debugging process more accurate and efficient.
---

### D3. Is there anything else the markers should know about team dynamics or individual contributions?

This is optional. Use it only if there is important context that the ratings above do not capture
(e.g., a member had a documented personal emergency, or a member was unresponsive for a significant period).

> *Your answer (or "Nothing to add"):*
nothing to add
---

## Declaration

I confirm that this peer review reflects my honest and independent assessment.
I understand it will be kept confidential from my teammates.

**Signed:** ______________施竑宇___________________ **Date:** _____2026-06-12__________
