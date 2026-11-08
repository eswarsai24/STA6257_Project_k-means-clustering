# Instructions

Create a GitHub page for the project

## GitHub:
#### 1. Create a GitHub account and Sign in
#### 2. Go to https://github.com/acohenstat/STA6257_Project and fork (create a copy to your GitHub)
![fork](fork.png)
#### 3. Change the name of the repo to *STA6257_Project_NameofGroup*
#### 4. Go to *Settings* -> *Pages* -> under *Branch* -> select *main*
#### 5. Wait for a few seconds and refresh the page. You see the link of the page. 

## RStudio:
#### 1. Go to RStudio
#### 2. Create a Version Control Project and Clone the repo.
#### 3. Commit and push to see changes on the website et Voilà!

More information:
- [GitHub](https://happygitwithr.com/index.html)
- [Video1 RStudio connection to GitHub](https://www.youtube.com/watch?v=MdmnE3AnkQE)
- [Video2 RStudio connection to GitHub](https://www.youtube.com/watch?v=jN6tvgt3GK8)



Act as a Staff/Principal Software Engineer and Solution Architect with expertise in Python, Scala, Apache Spark, HBase, Hive, Hadoop/HDFS, AWS, and enterprise data engineering.

Help me plan and implement a production-quality migration of existing Scala-based configuration scripts to Python. Apply the engineering rigor expected at MAANG companies: demonstrable correctness, clear design, maintainability, security, scalability, meaningful tests, and operational readiness. Keep the solution proportionate to the actual requirements and consistent with the existing architecture.

## 1. Project context and objective

- **Existing code location:** `[EXISTING_CODE_PATH]`
- **Target location for the Python implementation:** `[TARGET_PYTHON_PATH]`
- The application runs in an on-premises environment and needs to connect to HBase or Hive to extract the required data.
- The intended final destination is AWS S3.
- AWS S3 bucket provisioning is currently in progress, so HDFS is the temporary output destination for development and testing.
- The design must support the later transition to S3 while preserving shared extraction, configuration, validation, and processing logic.

First establish exactly what “Scala configuration scripts” covers in this repository. Identify whether the migration affects configuration loading, orchestration, source connectivity, transformations, distributed processing, or destination handling.

Do not assume that moving configuration scripts to Python requires rewriting the underlying Scala/Spark processing engine. Determine which components should be migrated, reused, or retained based on repository evidence.

## 2. Working approach and approval boundary

**Start with repository analysis and planning only. Wait for my explicit approval before beginning implementation.**

You may inspect the repository and run available, non-destructive baseline checks. Before approval, do not modify application code, create implementation scaffolding, change dependencies, or alter infrastructure.

Complete all useful analysis possible with the available information before asking questions. Clearly distinguish:

- Verified facts supported by code or configuration.
- Proposed design decisions.
- Assumptions requiring confirmation.
- Dependencies or blockers outside this task.

If a path, file, runtime, or environment is inaccessible, state that limitation precisely. Do not claim to have inspected code or executed tests that you could not access.

## 3. Assess the existing implementation

Read the relevant repository instructions, documentation, entry points, configuration files, source connectors, processing logic, output writers, tests, and deployment scripts.

Explain:

- How the current application starts and how configuration reaches each component.
- How it connects to HBase and Hive, including relevant authentication requirements.
- How datasets, tables, schemas, mappings, filters, and date ranges are selected, where applicable.
- Which components perform distributed processing and which run locally.
- How output format, partitioning, write modes, and destination paths are determined.
- How validation, logging, errors, retries, resource cleanup, and completion reporting currently work.
- How the application is built, packaged, submitted, and deployed.

Identify the actual runtime and dependency versions involved, including Python, Java, Scala, Spark, Hadoop, and source connectors where relevant. Assess compatibility before recommending replacements.

Provide an evidence-based assessment table:

| Capability | Existing file/class/function | Current behavior | Reuse or change | Evidence | Remaining gap |
|---|---|---|---|---|---|

Run the documented baseline build or tests when feasible. Record the command, revision if available, outcome, and any environment limitations. Do not upgrade dependencies merely to make the baseline pass.

## 4. Recommend the migration design

Recommend the smallest maintainable approach that satisfies the requirements.

### Migration boundary and behavior preservation

Explain whether the appropriate approach is Python, PySpark, Python orchestration around existing Scala components, or another option already supported by the repository.

Justify the recommendation using the current architecture, connector compatibility, deployment environment, and expected workload.

Preserve existing behavior unless an intentional change is clearly documented and approved. Specifically assess:

- Configuration names, defaults, validation, and precedence.
- Source-selection and filtering behavior.
- Schema, data types, mappings, null handling, and serialization.
- Date boundaries and time zones, where applicable.
- Output format, partitioning, naming, and write modes.
- Error behavior, exit status, and downstream integration contracts.

Identify any behavior that cannot be preserved directly and explain the proposed resolution.

### HDFS now and S3 later

Propose the smallest practical separation between extraction/processing and destination-specific writes.

Explain:

- How configuration selects HDFS or S3.
- Which components remain shared and which require destination-specific behavior.
- How paths, partitions, permissions, and output validation are handled.
- How partial writes, retries, reruns, conflicting outputs, and cleanup are managed.
- How successful completion is reported so downstream consumers do not treat incomplete output as complete.

Assess destination-specific behavior explicitly. Identify what HDFS testing can validate and which S3 checks must remain pending until the AWS environment is available.

### Configuration contract

Define the necessary configuration fields, including source, dataset/table, filters or date ranges where applicable, destination, output location, and relevant execution settings.

Provide a table containing:

| Setting | Type | Required/default | Validation | Environment-specific | Secret/reference |
|---|---|---|---|---|---|

Follow the existing configuration format unless there is a justified reason to change it. Include a synthetic example without real credentials or internal infrastructure details.

## 5. Coding and scalability standards

The proposed implementation must meet these standards:

- Follow existing repository conventions and use clear, consistent package, folder, file, class, and function names.
- Keep components cohesive, interfaces explicit, and responsibilities easy to understand.
- Use appropriate Python type hints and document public interfaces and non-obvious decisions.
- Apply DRY and SOLID principles where they improve the design. Justify new abstractions through actual reuse or complexity.
- Externalize environment-specific settings and validate configuration early.
- Use specific exceptions and actionable error messages. Preserve meaningful failure status.
- Use bounded retries for transient failures and define resource cleanup behavior.
- Preserve distributed execution where the workload requires it.
- Avoid unbounded memory usage, unnecessary full scans, per-record remote calls, or collecting large distributed datasets into a single process.
- Make batching, partitioning, and parallelism decisions based on actual workload and runtime evidence.
- Follow the repository’s supported dependency versions and quality tooling. Explain any proposed addition or upgrade.

Identify missing workload information, such as expected data volume, batch size, execution window, or performance targets. State assumptions instead of inventing performance guarantees.

## 6. Security and infrastructure dependencies

Use established authentication, authorization, and secret-management patterns.

Assess applicable requirements for source authentication, Kerberos/keytabs if used, AWS identity, permissions, approved network connectivity, TLS, and encryption.

Ensure the design:

- Keeps credentials and sensitive configuration out of source code.
- Avoids logging secrets or sensitive records.
- Uses appropriate permissions for source reads and destination writes.
- Preserves source data and prevents unintended destination overwrites.

Distinguish application changes from infrastructure prerequisites. List dependencies such as network routes, firewall rules, DNS, source access, S3 bucket/prefix, AWS role permissions, and encryption configuration.

Mark each dependency as confirmed, pending, or unknown, and identify its owner when that information is available. Keep infrastructure provisioning outside the implementation scope unless explicitly included.

## 7. Testing and operational readiness

Provide a practical validation strategy covering:

- Positive and negative configuration tests.
- Behavioral parity between the existing implementation and the proposed Python implementation.
- Source selection, filtering, schema handling, and transformations where applicable.
- Empty datasets, malformed inputs, and boundary conditions.
- Authentication failures, unavailable sources, and destination write failures.
- Retry behavior, partial outputs, reruns, and overwrite protection.
- HDFS integration testing in the available environment.
- S3 integration testing once provisioning and access are ready.

Use representative synthetic or sanitized fixtures. Define meaningful pass/fail criteria rather than relying only on a coverage percentage.

Clearly separate unit tests, mocked integration checks, and tests against actual services. Report what was executed, what passed or failed, and what remains unverified.

Propose operational logging and metrics appropriate to the workload, such as run identifiers, duration, records processed where practical, output location, and completion/failure status. Consider the processing cost of collecting metrics.

Include a concise runbook covering configuration, launch commands, troubleshooting, recovery from failed runs, and rollback to the existing implementation.

## 8. Required planning deliverables

Organize your initial response in this order:

1. **Understanding of the task:** objective, scope, constraints, and unresolved assumptions.
2. **Current-state assessment:** architecture, code evidence, versions, reusable components, and gaps.
3. **Recommended design:** migration boundary, alternatives considered, and trade-offs.
4. **Proposed folder structure:** aligned with the repository’s naming and packaging conventions.
5. **File-by-file implementation plan:** identify each proposed addition or modification.

   | File/module | Responsibility | Reused component | Proposed change | Reason | Validation |
   |---|---|---|---|---|---|

6. **Configuration and connectivity plan:** required settings, authentication, dependencies, and synthetic usage examples.
7. **Implementation sequence:** small, reviewable steps with dependencies and acceptance criteria.
8. **Testing and rollout plan:** behavioral parity, HDFS verification, later S3 activation, and rollback.
9. **Risks and essential questions:** prioritize items that materially affect correctness, compatibility, security, or scope.

Define completion criteria separately for the HDFS testing milestone and the S3 integration milestone.

For every major recommendation, explain the problem it solves, why it fits the existing system, and the trade-off involved. Keep explanations clear enough for me to discuss with my technical lead.

Once I approve the plan, implement it incrementally, validate each meaningful change, and report the results with evidence. Surface material discoveries that change the approved scope before proceeding with those changes.

**Begin with the repository assessment and proposed plan.**
