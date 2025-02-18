# ReadySet Core Releases

ReadySet releases a new version of ReadySet Core on a monthly basis. This page summarizes the changes in each version and links to binaries and docker images.

!!! warning

    Beta versions of ReadySet are backward-incompatible. To upgrade between beta versions, you must therefore clear all data files. Rolling upgrades will be supported with future ReadySet major releases.

## beta-2023-06-29

### Downloads

=== ":material-docker: Docker"

    - ReadySet Server (linux/amd64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2023-06-29
        ```

    - ReadySet Adapter (linux/amd64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset:beta-2023-06-29
        ```

=== ":material-source-repository: Source"

    !!! note

        This release does not include pre-built binaries. However, you can build binaries from source. For guidance, see the ReadySet [README](https://github.com/readysettech/readyset#development).

    - [`zip`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-06-29.zip)
    - [`tar.gz`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-06-29.tar.gz)

### Changes

#### What's New

- Helm chart improvements. [[c6ded74](https://github.com/readysettech/readyset/commit/c6ded74), [cc1cf3f](https://github.com/readysettech/readyset/commit/cc1cf3f)]
- The custom SHOW READYSET STATUS SQL statement now includes the minimum and maximum replication offset. [da50579](https://github.com/readysettech/readyset/commit/da50579)
- Apache Zookeeper is no longer supported. [83ced41](https://github.com/readysettech/readyset/commit/83ced41)
- Reduced the time ReadySet waits before retrying after a replication error from 30s to 1s. [2649e2f](https://github.com/readysettech/readyset/commit/2649e2f)
- SQL support:
   - Allow spaces between and around arguments to aggregate functions. [529ba86](https://github.com/readysettech/readyset/commit/529ba86)
   - MySQL:
      - Allow arbitrary expressions as arguments to GROUP_CONCAT. [6f16afe](https://github.com/readysettech/readyset/commit/6f16afe)
      - Support bit literals (already supported for Postgres). [071678b](https://github.com/readysettech/readyset/commit/071678b) (thanks @cameronbraid!)
   - Postgres:
      - Support COMMENT statements. [a2e9302](https://github.com/readysettech/readyset/commit/a2e9302)
- Added the custom EXPLAIN DOMAINS SQL statement, which gives a list of domain replica shards and the URLs of the workers they're running on, sorted by the worker URL. [[8e402e7](https://github.com/readysettech/readyset/commit/8e402e7), [12090f8](https://github.com/readysettech/readyset/commit/12090f8)]

#### Performance Improvements

- RocksDB-based persistent storage now uses the block-based SST format, improving post-snapshot compaction speed by 10x for large tables (100+ GiB), and improving get/put performance by 7-28% across current benchmarks. [[8a32b30a](https://github.com/readysettech/readyset/commit/8a32b30a), [d0bec66](https://github.com/readysettech/readyset/commit/d0bec66)]

#### Bug Fixes

- Update to rust-openssl 0.10.55 to patch [RUSTSEC-2023-0044](https://rustsec.org/advisories/RUSTSEC-2023-0044). [5fb5e88](https://github.com/readysettech/readyset/commit/5fb5e88)
- Correctly handle concurrent updates to Consul keys. [cba739a](https://github.com/readysettech/readyset/commit/cba739a)

## beta-2023-05-31

### Downloads

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2023-05-31
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset:beta-2023-05-31
        ```

=== ":material-source-repository: Source"

    !!! note

        This release does not include pre-built binaries. However, you can build binaries from source. For guidance, see the ReadySet [README](https://github.com/readysettech/readyset#development).

    - [`zip`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-05-31.zip)
    - [`tar.gz`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-05-31.tar.gz)

### Changes

#### What's New
  - Revamped helm chart with changes to align with best practices and support
    multiple cloud platforms. [[a51ac40](https://github.com/readysettech/readyset/commit/a51ac40), [b739acb1](https://github.com/readysettech/readyset/commit/b739acb1), [931d0b91](https://github.com/readysettech/readyset/commit/931d0b91), [c4ff836b](https://github.com/readysettech/readyset/commit/c4ff836b)]
  - Created the [proptest-stateful](https://crates.io/crates/proptest-stateful)
    crate and added extensive stateful property tests.
  - ReadySet can now infer the `database_type` from an `upstream_db_url`,
    making the `--database-type` argument optional. If `--database-type` is
    provided with an `upstream_db_url`, readyset will validate the database
    type. [8ad44824](https://github.com/readysettech/readyset/commit/8ad44824)
  - Improve error messages for why something is unsupported when running
    without an upstream database. [41f35326](https://github.com/readysettech/readyset/commit/41f35326)
  - Add `--controller-address` option that exports prometheus metrics for the
    readyset-server when running in standalone mode. [688ab939](https://github.com/readysettech/readyset/commit/688ab939)
  - Add `replication-server-id` flag to allow multiple readyset deployments to
    connect to the same upstream db. Each deployment must have a unique
    replication server id. [896b423c](https://github.com/readysettech/readyset/commit/896b423c)
  - Add logging that uses row estimates to report snapshotting progress.
    [9f857769](https://github.com/readysettech/readyset/commit/9f857769)
  - Added an experimental feature to automatically inline
    placeholders in a query with literal values when the placeholders are
    unsupported, so that the query can be run against ReadySet. This
    feature is enabled with the `--experimental-placeholder-inlining` flag.
    This feature is experimental as it could degrade the performance of
    the ReadySet instance if too many inlined instances of a query are
    created.  It is recommended that you drop the cache of any query with
    inlined placeholders with high cardinality. [4a31acdf](https://github.com/readysettech/readyset/commit/4a31acdf)
  - Add support for `INT2`, `INT4`, and `INT8` postgres type aliases. [e9518f11](https://github.com/readysettech/readyset/commit/e9518f11)

#### Performance Improvements

  - Controller requests now run asynchronously. [120fb301](https://github.com/readysettech/readyset/commit/120fb301)
  - RocksDB compaction is now done as a background process. [51d5a4c3](https://github.com/readysettech/readyset/commit/51d5a4c3)
  - Tuned the retry behavior of `extend_recipe`. [1b771836](https://github.com/readysettech/readyset/commit/1b771836)
  - Initialize persistent state in a different thread. [c78bd61d](https://github.com/readysettech/readyset/commit/c78bd61d)
  - Numerous miscelaneous query performance optimizations.

#### Bug Fixes

  - Update metrics-rs crate to 0.21 to [fix a bug in summary
    calculations](https://github.com/metrics-rs/metrics/pull/306) where they
    were not being calculated over [sliding windows of
    time](https://prometheus.io/docs/instrumenting/writing_clientlibs/#summary).
    [1557db04](https://github.com/readysettech/readyset/commit/1557db04)
  - Fixed a bug where DDL actions were lost when we encountered a particular
    error in replication. [1101215c](https://github.com/readysettech/readyset/commit/1101215c)
  - Fixed an issue where we returned an empty value instead of `BEGIN` for
    certain transactional queries. [fd08ff18](https://github.com/readysettech/readyset/commit/fd08ff18)
  - Fixed an incorect timezone conversion that prevented lookups into a date
    field using timestamps that had the same date. [33990c76](https://github.com/readysettech/readyset/commit/33990c76)
  - Fixed a bug with optimized string serialization. [5dec2e14](https://github.com/readysettech/readyset/commit/5dec2e14)
  - Fixed a panic if offset is longer than result set, specifically in the case
    that a query, has `ORDER BY`, `LIMIT`, and `OFFSET`, has aggregates with a
    group by, and *doesn't* filter by a primary or unique column. [5d9c5cee](https://github.com/readysettech/readyset/commit/5d9c5cee)
  - Fixed a panic that could happen if a connection failed during a migration.
    [020f58a3](https://github.com/readysettech/readyset/commit/020f58a3)
  - Fixed a bug where the ordering of project and distinct nodes would return
    incorrect results. [236ed823](https://github.com/readysettech/readyset/commit/236ed823)
  - Properly mark PostgreSQL partitioned tables as unsupported. [2abffe13](https://github.com/readysettech/readyset/commit/2abffe13)
  - Fix handling of a partial replay where a source node has been dropped.
    [94be3fcb](https://github.com/readysettech/readyset/commit/94be3fcb)
  - Properly support creating tables with FKs. [f10319eb](https://github.com/readysettech/readyset/commit/f10319eb)
  - Properly handle replication offsets for uninitialized base tables. [582b68fb](https://github.com/readysettech/readyset/commit/582b68fb)

## beta-2023-02-15

### Downloads

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2023-02-15
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset:beta-2023-02-15
        ```

=== ":material-source-repository: Source"

    !!! note

        This release does not include pre-built binaries. However, you can build binaries from source. For guidance, see the ReadySet [README](https://github.com/readysettech/readyset#development).

    - [`zip`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-02-15.zip)
    - [`tar.gz`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-02-15.tar.gz)

### Changes

- Multiple instances of ReadySet can now be run against a single Postgres instance. [ab92510](https://github.com/readysettech/readyset/commit/ab92510e365bb585339c04e50c2acea2f7a71276)
- The [`--eviction-policy`](../reference/cli/readyset.md#-eviction-policy) CLI option now defaults to `lru`, which performs best for most workloads. [998dd34](https://github.com/readysettech/readyset/commit/998dd341701e325ec2e951496b54c39c1306f629)
- The [`--username`](../reference/cli/readyset.md#-username) and [`--password`](../reference/cli/readyset.md#-password) CLI options now default to the username and password for the upstream database in the [`--upstream-db-url`](../reference/cli/readyset.md#-upstream-db-url) option. [8989d30](https://github.com/readysettech/readyset/commit/8989d30ddb97da9fc6ab91fa528ba2b8abe6472b)
- Added a new `--worker-request-timeout-seconds` CLI option and `WORKER_REQUEST_TIMEOUT_SECONDS` environment variable for configuring the timeout for requests made to workers. [875d0da](https://github.com/readysettech/readyset/commit/875d0da5f181d403372eca31920761cc7bc5b2fa)
- Improved error messages for [`CREATE CACHE`](../guides/cache-queries/#cache-queries_1) statements with queries that ReadySet is unable to parse. [1035e26](https://github.com/readysettech/readyset/commit/1035e262d8d3c5585296f19d2b64959687d42840)
- The following Prometheus metrics no longer contain domain and
shard tags, and have been renamed to have the `domain_` prefix removed:
`domain_forward_time_us`, `domain_total_forward_time_us`,
`domain_eviction_time_us`, `domain_node_state_size_bytes`,
`domain_reader_state_size_bytes`, `domain_base_tables_estimated_size_bytes`,
`domain_eviction_requests`, `domain_eviction_freed_memory`, and
`domain_node_added`. [00305d9](https://github.com/readysettech/readyset/commit/00305d9b50ef1eea7efc2cc2650d9d8069c33acc)
- When restarting ReadySet, changes to the [`--replication-tables`](../reference/cli/readyset/#-replication-tables) CLI option are now applied. [3151117](https://github.com/readysettech/readyset/commit/3151117554cb034826b91bcb589a11952ea684f2)
- Fixed a bug where ReadySet running in PostgreSQL mode would return an error for queries with integer literals in the `SELECT` list. [f0062db](https://github.com/readysettech/readyset/commit/f0062db89f8f01deac1fb856de5a2a29640cfabd)
- When ReadySet receives an unsupported value in the replication stream (e.g., `infinity` or `-infinity` for a Postgres date or timestamp), ReadySet now stops replicating the affected table and removes all caches accessing that table. This allows ReadySet to continue with the replication of other tables instead of retrying that replication event infinitely. [bc47adf](https://github.com/readysettech/readyset/commit/bc47adf6bff87a39805c3a551d92e68fc83677b8)
- Fixed a bug that could, in certain cases, cause ReadySet to panic when executing a query that ReadySet is unable to parse. [caacfca](https://github.com/readysettech/readyset/commit/caacfca6192e7d220a0e19a9b480ebd98498cd75)
- Fixed a bug that could cause snapshotting and replication to fail for deeply nested `JSON` values. [551dda8](https://github.com/readysettech/readyset/commit/551dda850f22824ae6f61c98a2be7ea89a14b1a7)
- Fixed an issue where the Postgres triggers that ReadySet installs for DDL replication could cause errors when attempting to create or alter types. [ef34ede](https://github.com/readysettech/readyset/commit/ef34ede425167f1958166f55992b42ea9eba1a72)
- Fixed an error decoding parse messages with unspecified types. [9ca36fa](https://github.com/readysettech/readyset/commit/9ca36fa5e894dec278128db46447c98445c70cb1)
- Fixed an issue where the ReadySet replicator wasn't gracefully handling every kind of Postgres connection failure. Now, the replicator will always retry in the event of a lost connection to the upstream database. [318be1d](https://github.com/readysettech/readyset/commit/318be1d00ed58b53bc1a67a51215f09ad4bb05b5)
- Fixed an issue where executing prepared statements with `CITEXT` would fail with an unsupported error. [37ff026](https://github.com/readysettech/readyset/commit/37ff026b3a69d643b002e52eca41a265d1044a86)

## beta-2023-01-18

### Downloads

=== ":material-linux: Linux"

    !!! note

        ReadySet binaries for Linux require the OpenSSL 1.1.x package. OpenSSL 3.x+ is not currently supported.

    Binary (linux-x84_64) | Sha256Sum
    ----------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/releases/download/beta-2023-01-18/readyset-server-beta-2023-01-18.x86_64.tar.gz) | d56e663e6fab9b83b12dbe03008623c7c4e3e19bb3ba14eb1c7d45d2b3c5b9d7
    [ReadySet Adapter](https://github.com/readysettech/readyset/releases/download/beta-2023-01-18/readyset-beta-2023-01-18.x86_64.tar.gz) | 696308d6720b16515aa9ab7587766c858d12910c00f4760f756d9630b44011f8

=== ":material-apple: Mac"

    Binary (darwin-arm64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/releases/download/beta-2023-01-18/readyset-server-beta-2023-01-18.arm64.tar.gz) | 0289a07e7dde2fcf94718680328b09300a0f92915be24dcbb00160692d0b40f1
    [ReadySet Adapter](https://github.com/readysettech/readyset/releases/download/beta-2023-01-18/readyset-beta-2023-01-18.arm64.tar.gz) | eb287021e25ce7e46386b8bb9629000f4328464796caed43d08cd7c33def83d0

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2023-01-18
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset:beta-2023-01-18
        ```

=== ":material-source-repository: Source"

    - [`zip`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-01-18.zip)
    - [`tar.gz`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2023-01-18.tar.gz)

### Changes

- The new custom `SHOW READYSET TABLES` SQL command returns a list of all tables ReadySet is aware of, along with their snapshotting status if they are being replicated by ReadySet, or "Not replicated" if ReadySet is not replicating them either because the table is unsupported or because the table was not specified as part of [`--replication-tables`](../reference/cli/readyset.md#-replication-tables). [764f0ed](https://github.com/readysettech/readyset/commit/764f0edbf55c5c648229df2c25751c36b88cb831)
- ReadySet now always successfully snapshots SQL views that select from other views. [85d89ed](https://github.com/readysettech/readyset/commit/85d89ed6930d6b6e1fbfded615c0dfc3cb281297)
- When running the `readyset` binary, set the new `-V`/`--version` option to output build/release information. [0d54fc6](https://github.com/readysettech/readyset/commit/0d54fc64bcc36a8b9eb40e8620ad0b38f6b0467e)
- ReadySet now correctly handles MySQL clients setting the database in the initial connection handshake. Previously, this was ignored and the client would instead be connected to the default database specified in the `--upstream-db-url` option. [3ace2e6](https://github.com/readysettech/readyset/commit/3ace2e651108dda9e2b9ed1874ebe1428d448872)
- Added caching support for the MySQL `JSON_OVERLAPS()` function. [56e2710](https://github.com/readysettech/readyset/commit/56e2710da416ee8104a7568e6b8467c29fb0139c)
- Added caching support for the MySQL `JSON_QUOTE()` function. [67a7051](https://github.com/readysettech/readyset/commit/67a70513c98704e26d15e7a502b8a41273458593)
- Added caching support for the PostgreSQL `JSONB_SET_LAX()` function. [d461cdb](https://github.com/readysettech/readyset/commit/d461cdb558852017d72cc253d8056fc93b6fe1b0)
- Fixed a bug that could cause snapshotting and replication to stall after encountering certain types of errors while loading a view from the upstream database. [3107da9](https://github.com/readysettech/readyset/commit/3107da98fa2a83f2d272016e5db5b04bd9503313)
- Fixed a bug that caused replication to fail on Aurora Postgres version 13 databases. [9be6443](https://github.com/readysettech/readyset/commit/9be644320a7521d8f2c30c3fda811c7736c4fb26)
- Fixed a bug that could cause a failure loop when replicating a table. [adc8f25](https://github.com/readysettech/readyset/commit/adc8f25e01f4e14b2b99e764785ec9b4dd6e7daa)
- Fixed an issue where ReadySet would not correctly write out the row description or correct tag for `SELECT`s that return no results. [02b3fb6](https://github.com/readysettech/readyset/commit/02b3fb6b6c8a21509f6922956dd978e39105425c)

## beta-2022-12-15

### Downloads

=== ":material-linux: Linux"

    !!! note

        ReadySet binaries for Linux require the OpenSSL 1.1.x package. OpenSSL 3.x+ is not currently supported.

    Binary (linux-x84_64) | Sha256Sum
    ----------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/releases/download/beta-2022-12-15/readyset-server.x86_64.tar.gz) | 376a3c31923af51abf5a7ec181181eecaa1308de51efd7f336ebce54222abae0
    [ReadySet Adapter](https://github.com/readysettech/readyset/releases/download/beta-2022-12-15/readyset.x86_64.tar.gz) | 7468de0b2611bfdd6df79198709c06d389f1df32223735e851c5070ca77fa82d

=== ":material-apple: Mac"

    Binary (darwin-arm64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/releases/download/beta-2022-12-15/readyset-server.arm64.tar.gz) | a8a2eb79e7c22fe5c6ce140e29931f4a474b89ead8c5f201a7eb4d370ab98861
    [ReadySet Adapter](https://github.com/readysettech/readyset/releases/download/beta-2022-12-15/readyset.arm64.tar.gz) | 0e02417a0435232529cd1ec4b3a2bedb2164334e83c8f92ffb5d2d442e3ac12c

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2022-12-15
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset:beta-2022-12-15
        ```

=== ":material-source-repository: Source"

    - [`zip`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2022-12-15.zip)
    - [`tar.gz`](https://github.com/readysettech/readyset/archive/refs/tags/beta-2022-12-15.tar.gz)

### Changes

- ReadySet now distributes a single `readyset` adapter binary and docker image for MySQL and Postgres instead of separate `readyset-mysql` and `readyset-psql` binaries. When running the `readyset` binary, set the new `--database-type` flag or `DATABASE_TYPE` environment variable to `mysql` or `postgresql` to specify which database ReadySet is integrating with. The `DATABASE_TYPE` environment variable replaces the `ENGINE` variable, which has been removed. Also, the `readyset-adapter` docker image has been renamed `readyset`. [e8004b0](https://github.com/readysettech/readyset/commit/e8004b0c779e463bc0343defb7675c14c90a26b7), [8315587](https://github.com/readysettech/readyset/commit/831558777c4a8c5737273a71de438cb618e7824b)
- Parallelized the snapshotting of Postgres tables, greatly reducing the time it takes snapshotting to finish. [b1999a6](https://github.com/readysettech/readyset/commit/b1999a66fa57eaaec8444c164538dd8d2c572209)
- Added caching support for the Postgres `"CHAR"` special character type. [82f87a8](https://github.com/readysettech/readyset/commit/82f87a8432617572b59e821e3cae771168657bed)
- Added caching support for the Postgres `JSONB_INSERT()` function. [b25d6c7](https://github.com/readysettech/readyset/commit/b25d6c7288ec4a01be28d5e5bd017c7c2ea69649)
- Added caching support for the Postgres `JSONB_SET()` function. [9c91a77](https://github.com/readysettech/readyset/commit/9c91a77f8a90ed1f5ea5911956ed7c19bd0c36dc)
- Added caching support for the Postgres `JSONB_PRETTY()` function. [055d2fc](https://github.com/readysettech/readyset/commit/055d2fcf271ad9e296e612c2397cf7fd228b7674)
- Added caching support for the MySQL `JSON_DEPTH()` function. [9186017](https://github.com/readysettech/readyset/commit/9186017b0eaa76bd0d6735620cfafd413c853d46)
- Added caching support for the MySQL `JSON_VALID()` function. [b72cfa1](https://github.com/readysettech/readyset/commit/b72cfa1ce9aed75ca3a690b795b87758e22e2b75)
- Added caching support for the Postgres `#-` operator. [a69f0d9](https://github.com/readysettech/readyset/commit/a69f0d9913f9fa4d2ddf199117a526785bd5036f)
- Added caching support for `CASE WHEN` expressions with more than one `WHEN` branch. [e7973bf](https://github.com/readysettech/readyset/commit/e7973bf9d007368dc152553b8b2842b8dbc36cd1)
- Added caching support for subqueries directly in the `FROM` clause of the query. [8d0a633](https://github.com/readysettech/readyset/commit/8d0a63302a37b3a2aa19a3ea9245caeea4797c9a)
- For queries that access tables that ReadySet has not replicated, the `SHOW PROXIED QUERIES` command now returns `no` for `readyset supported` instead of `pending`. Also, such queries now log at warn-level instead of error-level. [92e258f](https://github.com/readysettech/readyset/commit/92e258f7342129b53c160b540551bab66edd9d8d), [75ba85e](https://github.com/readysettech/readyset/commit/75ba85e79b2e83d876d351655f06b922714cd9a5)
- Added replication support for "unchanged" Postgres [TOAST](https://www.postgresql.org/docs/current/storage-toast.html) fields. [9a05b63](https://github.com/readysettech/readyset/commit/9a05b631cb4de13d23696200caa6aa64944007c9)
- Added replication support for the Postgres `TRUNCATE` statement. [405ac80](https://github.com/readysettech/readyset/commit/405ac8085a4a3d03ef01ea4d038f39357470a78f)
- Renamed the `NORIA_DEPLOYMENT` environment variable to `DEPLOYMENT` to match the `--deployment` flag. [7ac9220](https://github.com/readysettech/readyset/commit/7ac922083fe4aee6b1d3c61753416851b190056a)
- Renamed the Prometheus metrics endpoint from `/prometheus` to `/metrics`. [d10d142](https://github.com/readysettech/readyset/commit/d10d142fb4b12fa4f0d355a543c925e2e8103b6f)
- Fixed a bug with caching the Postgres `CHAR` data type. [9f151f6](https://github.com/readysettech/readyset/commit/9f151f60c3a3e14c994abc8964765a315214312e)
- Fixed a bug that could lead to missing cache data and/or crashes after inserting large amounts of table data with the Postgres `COPY` or `\copy` commands. [daa5e1c](https://github.com/readysettech/readyset/commit/daa5e1c610d2341c6452de0f389e7fdbeb38fa29)
- Fixed a panic that could be triggered when using Postgres as the upstream database. [58c3bd3](https://github.com/readysettech/readyset/commit/58c3bd3b770bd70f8688e86377a418629865b134)
- Fixed a bug where tables and views that failed to replicate from the upstream database would not participate in schema resolution, which could cause certain queries with non-schema-qualified table references to read from a table in the wrong schema rather than being proxied to the upstream database. [c8e5c6b](https://github.com/readysettech/readyset/commit/c8e5c6b7bddc7c02f724120e3bd23082b2750d2e)
- Fixed a bug where a query that projects two columns with the same name but different tables would return values from the first of the two columns twice. [6054a1e](https://github.com/readysettech/readyset/commit/6054a1e562ad7328af9434b8edb5de11e6ce18cf)

## beta-2022-11-16

### Downloads

=== ":material-linux: Linux"

    !!! note

        ReadySet binaries for Linux require the OpenSSL 1.1.x package. OpenSSL 3.x+ is not currently supported.

    Binary (linux-x84_64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/releases/download/beta-2022-11-16/readyset-server-2022-11-16.x86_64.tar.gz) | 3347c7de63b7a487f493408191d1e9dfb1dd0f46d01653afb9bafe62f5008154
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/releases/download/beta-2022-11-16/readyset-mysql-2022-11-16.x86_64.tar.gz) | 921ae4d9ad41b77addbdc7a9eb3439b547711ca1acdc4bc9a9df690e40fa643d
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/releases/download/beta-2022-11-16/readyset-psql-beta-2022-11-16.darwin-arm64.tar.gz) | 13472459c23efa590cbe1251d79ff9af4f309bf2d3ee7c0762d0a6e150cf22ff

=== ":material-apple: Mac"

    Binary (darwin-arm64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/files/9974631/readyset-server-beta-2022-11-16.darwin-arm64.tar.gz) | ed33bff2375a574f65628813e2eadcc7d4ea0914436dfdd87db936afa3054d18
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/releases/download/beta-2022-11-16/readyset-mysql-beta-2022-11-16.darwin-arm64.tar.gz) | cda080d4f231d89b2c95c99961b10ed119b696d13c453765dc394c95fe275a35
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/releases/download/beta-2022-11-16/readyset-psql-beta-2022-11-16.darwin-arm64.tar.gz) | 7fc278eff4e08de62a9cee655a4b3d721f92653571b9de063e7dea90d6db6c2e

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2022-11-16
        ```
    !!! bug

        ReadySet Adapter images may be broken on certain Mac platforms in this release!

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-adapter:beta-2022-11-16
        ```

### Changes

- Added the ability to filter `SHOW PROXIED QUERIES` and `SHOW CACHES` by `query_id`, e.g, `SHOW PROXIED QUERIES WHERE query_id = 'q_7b44f013d9995ffe';`. [b150cfc](https://github.com/readysettech/readyset/commit/b150cfc74f57c5b639005671065044337d0ab9b6)
- Added caching support for several Postgres JSON operators: `-`, `||`, `->`, `->>`, `@>`, `<@`, `#>`, and `#>>`. [c20aa47](https://github.com/readysettech/readyset/commit/c20aa47fc8cae4fc25b5dc6bffd9ca3729ae1), [b59a0f5](https://github.com/readysettech/readyset/commit/b59a0f56ed8aa83af43d2d278cf556b5137c7edf), [1804acc](https://github.com/readysettech/readyset/commit/1804acc764263ac552d4d20c4652356076d40c36), [d3c6085](https://github.com/readysettech/readyset/commit/d3c6085ab4e4d13eaacfb0b9aa1861a964f28931), [d29d7e7](https://github.com/readysettech/readyset/commit/d29d7e71a8a165220427af744a7cd7783a9d4022), [3753cb7](https://github.com/readysettech/readyset/commit/3753cb71d990831b63f584878aea6b0eab7866e9), [ee0cfaf](https://github.com/readysettech/readyset/commit/ee0cfaf99d4cbda99cca04735da525ba3c0ac536), [fe8b332](https://github.com/readysettech/readyset/commit/fe8b332bfc209047438507ad1ca0290311fe5bd4), [045321b](https://github.com/readysettech/readyset/commit/045321b3afc643f9474f0c79386b283766c06845), [60e0352](https://github.com/readysettech/readyset/commit/60e0352190c76fa2cdb6cb99ef2bf44db03e7ea4), [42f944b](https://github.com/readysettech/readyset/commit/42f944b088d9e112f0b03fe53fcae80f881e9f32), [f9ece02](https://github.com/readysettech/readyset/commit/f9ece029626913d427ac1cdf900a898700acd39f)
- Added caching support for the MySQL `||` operator, which equivalent to `OR`. [d1796d1](https://github.com/readysettech/readyset/commit/d1796d1927f8c7d088150d80f152d840ba0ef769), [fa8bb66](https://github.com/readysettech/readyset/commit/fa8bb660fd6248e0f789ef71c0230c0476c60f0e)
- Added caching support for the MySQL `DATE_FORMAT()` function. [b5041a7](https://github.com/readysettech/readyset/commit/b5041a797c20eae596bf2af943f703e3fe2ddc3d)
- ReadySet now logs when replication slots are created and dropped. [9f85d98](https://github.com/readysettech/readyset/commit/9f85d98201d651eddf3f827ae70c3fa765850f6f)
- Improved logging to ensure `ERROR` messages highlight situations that may require manual attention and `INFO` messages are not overly noisy with debugging details. [fb4d42c](https://github.com/readysettech/readyset/commit/fb4d42cd1bf86824494439a2e43ca64d17192ac6)
- Fixed a bug that could cause Postgres replication to repeatedly fail on certain tables. [7207efa](https://github.com/readysettech/readyset/commit/7207efa7096c54168cebd7f79e7e6e7bed66391b)
- Fixed a bug where arrays of Postgres enums would fail to replicate from the upstream database. [74e0e71](https://github.com/readysettech/readyset/commit/74e0e71282fa2a3ff573f81d7d3e11b35a954772), [d964876](https://github.com/readysettech/readyset/commit/d964876f224a59ef0f2b9936fea792bf98e3a666)


## beta-2022-11-09

### Downloads

=== ":material-linux: Linux"

    !!! note

        ReadySet binaries for Linux require the OpenSSL 1.1.x package. OpenSSL 3.x+ is not currently supported.

    Binary (linux-x84_64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/files/9974540/readyset-server-beta-2022-11-09.linux-x86_64.tar.gz) | d52631fb7b8b0a912aa205f005ffd32b8494f38fb43ea96ccee08b41c216002d
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/files/9974536/readyset-mysql-beta-2022-11-09.linux-x86_64.tar.gz) | 966f4701beca80faf8d572fd261ff8431bbda374557680c2687e452c6cc228d6`
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/files/9974538/readyset-psql-beta-2022-11-09.linux-x86_64.tar.gz) | 6a1a5a47b3ee138c38d1d493fe5d442dabd001abb4858982dc2dfba3fbd6eaaf

=== ":material-apple: Mac"

    Binary (darwin-arm64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/files/9974631/readyset-server-beta-2022-11-09.darwin-arm64.tar.gz) | 76d7a65b9ef953af5ea337fa7ed731524c463d961835f7cb80444c6c9cc7a7d3
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/files/9974630/readyset-mysql-beta-2022-11-09.darwin-arm64.tar.gz) | 0c00931f87900e5570cdb83421ceddba64e55abe5316e0f29bd59848849f7884
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/files/9974629/readyset-psql-beta-2022-11-09.darwin-arm64.tar.gz) | 81faacc0e463f5dbe3b1a0fa5d139ea86e203f36e39d91814cfc1fc391c52d10

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2022-11-09
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-adapter:beta-2022-11-09
        ```

### Changes

- Added replication and caching support for Postgres enumerated data types. [d8e428d](https://github.com/readysettech/readyset/commit/d8e428d5cf63fc704d2b9242066c2bccafffd76b), [e282ae9](https://github.com/readysettech/readyset/commit/e282ae99fe0f7e2491f56203b455b9f2e72ebd8e), [40d217f](https://github.com/readysettech/readyset/commit/40d217fdb8b55f004a8e3cbca9d2e30b379bd269), [ef95276](https://github.com/readysettech/readyset/commit/ef95276d4c3498ce77eed81e2d2d2185c0b21791), [5b401a5](https://github.com/readysettech/readyset/commit/5b401a5ecd805bba26421eaaf1a5f42ce5bd7dcd), [23e6a12](https://github.com/readysettech/readyset/commit/23e6a120ac3090cdd9cdf408824b24973ebc80d6), [312dfa0](https://github.com/readysettech/readyset/commit/312dfa04574cd7d471d5b35598c92df13d496168), [2fe679f](https://github.com/readysettech/readyset/commit/2fe679f20348a74e7e07ce2517073348ce365c11), [2295188](https://github.com/readysettech/readyset/commit/2295188268b744e630ef5f6adbf03c0fd3e3e68a)
- Added caching support for the `?|` and `?&` JSON operators. [f5ae4d](https://github.com/readysettech/readyset/commit/f5ae4d0c004dfe9095523155cf0ceb78fe14309b), [6598431](https://github.com/readysettech/readyset/commit/659843199836c321ed4fecbce31c894dd2100fa2)
- Fixed a bug that would cause `CREATE CACHE` commands to fail for MySQL `JOIN` queries with join conditions in the `WHERE` clause instead of the `ON` clause. [f9be6d6](https://github.com/readysettech/readyset/commit/f9be6d6e99c284f2a30163075a272063bab2f449)
- Upgraded OpenSSL to address a recently identified [vulnerability](https://github.com/advisories/GHSA-8rwr-x37p-mx23). [4525073](https://github.com/readysettech/readyset/commit/4525073eb2a505ef0ef2489f79a0149cf6a29fcd)
- ReadySet now logs the names of table/views that fail to snapshot or replicate. [7ffaf07](https://github.com/readysettech/readyset/commit/7ffaf07cbfca81691760b3ebf0b53afc66ac85e0)
- ReadySet now logs queries that fail to parse. [a25c401](https://github.com/readysettech/readyset/commit/a25c401b450a7a919cb380e13aa14a089be7333f)
- Added node column types to Graphviz data flow visualizations. [30f023b](https://github.com/readysettech/readyset/commit/30f023bf41c9c6a0548b83f71e76c35ee7f336a0)
- The ReadySet Adapter now fails to start and returns and error when its version does not match the ReadySet Server. [e00c742](https://github.com/readysettech/readyset/commit/e00c742bdee9dd7c23361cade496fee1402ecaa3)

## beta-2022-11-01

### Downloads

=== ":material-linux: Linux"

    !!! note

        ReadySet binaries for Linux require the OpenSSL 1.1.x package. OpenSSL 3.x+ is not currently supported.

    Binary (linux-x84_64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/files/9910818/readyset-server-beta-2022-11-01.linux-x86_64.tar.gz) | ea2b82f1faedf5836f704b496277d44906ad85a259dae7631e4749a40f9ba6b6
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/files/9910814/readyset-mysql-beta-2022-11-01.linux-x86_64.tar.gz) | 7aaeba77e70fa5c8f64b0d3ab1603365ae25b3f9bf4d9048a7b84cf63744b73a
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/files/9910812/readyset-psql-beta-2022-11-01.linux-x86_64.tar.gz) | 7f474cb5c7fc9f799fd84be735660e0abf7595ff108afaabe8c502737c2ffc76

=== ":material-apple: Mac"

    Binary (darwin-arm64) | Sha256Sum
    ------------------------------|----------
    [ReadySet Server](https://github.com/readysettech/readyset/files/9974664/readyset-server-beta-2022-11-01.darwin-arm64.tar.gz) | 8a204cd07b85fbbc375ee7e2c9b87ce44b6df3a2dab8e78454d2047f7c02cfd0
    [ReadySet Adapter for MySQL](https://github.com/readysettech/readyset/files/9974662/readyset-mysql-beta-2022-11-01.darwin-arm64.tar.gz) | 44ba62376b0a34fd099cd9c7b83fbb2714a868615d2792e63104b5a6862759b4
    [ReadySet Adapter for Postgres](https://github.com/readysettech/readyset/files/9974663/readyset-psql-beta-2022-11-01.darwin-arm64.tar.gz) | cc220b6d1dfba79e9a0ad9946205000a072fd29fdcd09d6f510842fde827066f

=== ":material-docker: Docker"

    - ReadySet Server (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-server:beta-2022-11-01
        ```

    - ReadySet Adapter (linux-x84_64)
        ``` sh
        docker pull public.ecr.aws/readyset/readyset-adapter:beta-2022-11-01
        ```

### Changes

Since first announcing ReadySet Core in [July 2022](https://readyset.io/blog/readyset-core), ReadySet has made steady progress on increasing SQL support and improving deployment options, including the following highlights:

**SQL support**

Area | Description
-----|------------
[Data types](../reference/sql-support.md#data-types) | ReadySet can now replicate tables with even more MySQL and Postgres data types, including `DATE` types, the MySQL `ENUM` type, and the Postgres `ARRAY` and `CITEXT` types.
[Table namespaces](../reference/sql-support.md#namespaces) | ReadySet can now replicate tables in multiple schemas of a Postgres database.
[Schema changes](../reference/sql-support.md#schema-changes) | When ReadySet receives certain `ALTER TABLE` schema change commands via the replication stream, including `ADD COLUMN` and `DROP COLUMN`, ReadySet now updates its snapshot and clears all cached queries for the affected table.
[Query caching](../reference/sql-support.md#query-caching) | ReadySet can now cache the results of `SELECT` queries containing the `HAVING` clause or subqueries in the `FROM` clause. In addition, all queries that ReadySet does not support are now proxied to the upstream database.

**Deployment**

Area | Description
-----|------------
[Helm chart](../guides/deploy/deploy-readyset-kubernetes.md) | ReadySet's new Helm chart lets you quickly deploy ReadySet Core into a Kubernetes cluster in front of an existing Amazon RDS database.
[SSL verification](../guides/deploy/deploy-readyset-kubernetes.md#step-4-configure-readyset) | When starting the ReadySet Server and Adapter, you can now tell ReadySet where to find the root certificate for SSL verification using the `--ssl_root_cert` command-line flag or `SSL_ROOT_CERT` environment variable.
[Cache location](../guides/deploy/deploy-readyset-kubernetes.md#step-4-configure-readyset) | When starting the ReadySet Server and Adapter, you can now choose to store cached query results on the Adapter rather than on the Server (the default behavior) by passing the `--embedded-readers` flag to the Adapter and the `--no-readers` flag to the Server.
[Table replication scope](../guides/deploy/deploy-readyset-kubernetes.md#step-4-configure-readyset) | If the queries you want to cache with ReadySet touch only specific tables, when starting the ReadySet Server, you can now restrict the scope of replication accordingly using the `--replication_tables` command-line flag or `REPLICATION_TABLES` environment variable.
