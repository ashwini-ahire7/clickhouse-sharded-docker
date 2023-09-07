# clickhouse-sharded-docker
Creation of only Sharded Cluster using Docker

```
root@docker:~/inception_infra# git clone https://github.com/ashwini-ahire7/clickhouse-sharded-docker.git
Cloning into 'clickhouse-sharded-docker'...
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 13 (delta 1), reused 10 (delta 1), pack-reused 0
Receiving objects: 100% (13/13), 41.79 KiB | 6.96 MiB/s, done.
Resolving deltas: 100% (1/1), done.
root@docker:~/inception_infra#

root@docker:~/inception_infra/clickhouse-sharded-docker# ls -lhtr
total 128K
-rw-r--r-- 1 root root  956 Sep  7 02:55 zookeeper.xml
-rw-r--r-- 1 root root  114 Sep  7 02:55 macros_2.xml
-rw-r--r-- 1 root root  114 Sep  7 02:55 macros_1.xml
-rw-r--r-- 1 root root   61 Sep  7 02:55 listen_host.xml
-rw-r--r-- 1 root root 2.0K Sep  7 02:55 docker-compose.yaml
-rw-r--r-- 1 root root  73K Sep  7 02:55 config.xml
-rw-r--r-- 1 root root  21K Sep  7 02:55 complete-project.tar.gz
-rw-r--r-- 1 root root  439 Sep  7 02:55 clusters.xml
-rw-r--r-- 1 root root   74 Sep  7 02:55 README.md
root@docker:~/inception_infra/clickhouse-sharded-docker#

root@docker:~/inception_infra/clickhouse-sharded-docker# docker-compose up -d
Creating network "clickhouse-sharded-docker_my-network" with driver "bridge"
Creating zookeeper ... done
Creating clickhouse01 ... done
Creating clickhouse02 ... done
root@docker:~/inception_infra/clickhouse-sharded-docker#
root@docker:~/inception_infra/clickhouse-sharded-docker#
root@docker:~/inception_infra/clickhouse-sharded-docker#
root@docker:~/inception_infra/clickhouse-sharded-docker# docker-compose ps


    Name                  Command               State                                                          Ports
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------
clickhouse01   /entrypoint.sh                   Up      0.0.0.0:8124->8123/tcp,:::8124->8123/tcp, 0.0.0.0:9001->9000/tcp,:::9001->9000/tcp, 9009/tcp
clickhouse02   /entrypoint.sh                   Up      0.0.0.0:8125->8123/tcp,:::8125->8123/tcp, 0.0.0.0:9002->9000/tcp,:::9002->9000/tcp, 9009/tcp
zookeeper      /opt/bitnami/scripts/zooke ...   Up      0.0.0.0:2182->2181/tcp,:::2182->2181/tcp, 0.0.0.0:2888->2888/tcp,:::2888->2888/tcp,
                                                        0.0.0.0:3888->3888/tcp,:::3888->3888/tcp, 8080/tcp
root@docker:~/inception_infra/clickhouse-sharded-docker#

root@docker:~/inception_infra/clickhouse-sharded-docker# docker exec -it clickhouse01 clickhouse-client
ClickHouse client version 23.3.1.2823 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 23.3.1 revision 54462.

Warnings:
 * Linux is not using a fast clock source. Performance can be degraded. Check /sys/devices/system/clocksource/clocksource0/current_clocksource

clickhouse01 :)
clickhouse01 :)
clickhouse01 :) SELECT *
                FROM system.clusters where cluster='cluster_demo_ash';

SELECT *
FROM system.clusters
WHERE cluster = 'cluster_demo_ash'

Query id: 401130f8-6725-4a9f-be5f-76ba82c6e0a1

┌─cluster──────────┬─shard_num─┬─shard_weight─┬─replica_num─┬─host_name────┬─host_address─┬─port─┬─is_local─┬─user────┬─default_database─┬─errors_count─┬─slowdowns_count─┬─estimated_recovery_time─┐
│ cluster_demo_ash │         1 │            1 │           1 │ clickhouse01 │ 172.20.0.2   │ 9000 │        1 │ default │                  │            0 │               0 │                       0 │
│ cluster_demo_ash │         2 │            1 │           1 │ clickhouse02 │ 172.20.0.3   │ 9000 │        0 │ default │                  │            0 │               0 │                       0 │
└──────────────────┴───────────┴──────────────┴─────────────┴──────────────┴──────────────┴──────┴──────────┴─────────┴──────────────────┴──────────────┴─────────────────┴─────────────────────────┘

2 rows in set. Elapsed: 0.002 sec.

clickhouse01 :) SELECT *
                FROM system.clusters
                WHERE cluster = 'cluster_demo_ash'

SELECT *
FROM system.clusters
WHERE cluster = 'cluster_demo_ash'

Query id: f6143aba-3b03-4765-a407-fcf34aec2ce7

┌─cluster──────────┬─shard_num─┬─shard_weight─┬─replica_num─┬─host_name────┬─host_address─┬─port─┬─is_local─┬─user────┬─default_database─┬─errors_count─┬─slowdowns_count─┬─estimated_recovery_time─┐
│ cluster_demo_ash │         1 │            1 │           1 │ clickhouse01 │ 172.20.0.2   │ 9000 │        1 │ default │                  │            0 │               0 │                       0 │
│ cluster_demo_ash │         2 │            1 │           1 │ clickhouse02 │ 172.20.0.3   │ 9000 │        0 │ default │                  │            0 │               0 │                       0 │
└──────────────────┴───────────┴──────────────┴─────────────┴──────────────┴──────────────┴──────┴──────────┴─────────┴──────────────────┴──────────────┴─────────────────┴─────────────────────────┘

2 rows in set. Elapsed: 0.002 sec.

clickhouse01 :) CREATE TABLE db1.table1 on cluster '{cluster}' (
                    `id` UInt64,
                    `column1` String
                )
                ENGINE = MergeTree
                ORDER BY column1

CREATE TABLE db1.table1 ON CLUSTER `{cluster}`
(
    `id` UInt64,
    `column1` String
)
ENGINE = MergeTree
ORDER BY column1

Query id: 29299892-76ce-4c42-a408-7135379488b5


0 rows in set. Elapsed: 0.002 sec.

Received exception from server (version 23.3.1):
Code: 81. DB::Exception: Received from localhost:9000. DB::Exception: Database db1 doesn't exist. (UNKNOWN_DATABASE)

clickhouse01 :) CREATE DATABASE db1 ON CLUSTER 'cluster_demo_ash';

CREATE DATABASE db1 ON CLUSTER cluster_demo_ash

Query id: bd246ff8-ebe7-4f8e-b351-9656812f3b72

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse01 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse02 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.125 sec.

clickhouse01 :)
clickhouse01 :) CREATE TABLE db1.table1 on cluster '{cluster}' (
                    `id` UInt64,
                    `column1` String
                )
                ENGINE = MergeTree
                ORDER BY column1

CREATE TABLE db1.table1 ON CLUSTER `{cluster}`
(
    `id` UInt64,
    `column1` String
)
ENGINE = MergeTree
ORDER BY column1

Query id: c786da19-586c-45cc-8986-dad6d8f5da14

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse01 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse02 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.120 sec.

clickhouse01 :)
clickhouse01 :) INSERT INTO db1.table1
                    (id, column1)
                VALUES
                    (1, 'abc'),
                    (2, 'def')

INSERT INTO db1.table1 (id, column1) FORMAT Values

Query id: 0c1b5965-68e7-4a0b-a289-cdb06b4f131a

Ok.

2 rows in set. Elapsed: 0.004 sec.

clickhouse01 :)
clickhouse01 :) select * from db1.table1;

SELECT *
FROM db1.table1

Query id: 73ea60fd-712b-45b3-aef4-960e75cfe5de

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘

2 rows in set. Elapsed: 0.002 sec.

clickhouse01 :) CREATE TABLE db1.dist_table (
                    id UInt64,
                    column1 String
                )
                ENGINE = Distributed(cluster_demo_ash,db1,table1)


CREATE TABLE db1.dist_table
(
    `id` UInt64,
    `column1` String
)
ENGINE = Distributed(cluster_demo_ash, db1, table1)

Query id: 9d281c67-7aaa-419a-9fb8-5bc8fc20821d

Ok.

0 rows in set. Elapsed: 0.005 sec.

clickhouse01 :)
clickhouse01 :) SELECT *
                FROM db1.dist_table

SELECT *
FROM db1.dist_table

Query id: 2dfef44c-e7b9-4de2-b491-535e70a994f7

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘

2 rows in set. Elapsed: 0.005 sec.

clickhouse01 :)
clickhouse01 :) exit
Bye.
root@docker:~/inception_infra/clickhouse-sharded-docker#
root@docker:~/inception_infra/clickhouse-sharded-docker# docker exec -it clickhouse02 clickhouse-client
ClickHouse client version 23.3.1.2823 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 23.3.1 revision 54462.

Warnings:
 * Linux is not using a fast clock source. Performance can be degraded. Check /sys/devices/system/clocksource/clocksource0/current_clocksource

clickhouse02 :)
clickhouse02 :)
clickhouse02 :)  INSERT INTO db1.table1
                    (id, column1)
                VALUES
                    (3, 'ghi'),
                    (4, 'jkl')

INSERT INTO db1.table1 (id, column1) FORMAT Values

Query id: 397ca3a5-0bec-456b-bdb4-f06726c44763

Ok.

2 rows in set. Elapsed: 0.003 sec.

clickhouse02 :)
clickhouse02 :) show tables from db1;

SHOW TABLES FROM db1

Query id: 6610e5a8-ec33-418b-86d3-f21b8cd9006f

┌─name───┐
│ table1 │
└────────┘

1 row in set. Elapsed: 0.002 sec.

clickhouse02 :)
clickhouse02 :)
clickhouse02 :) select * from db1.table1;

SELECT *
FROM db1.table1

Query id: 10bdbfeb-47df-45e9-a2de-4005c82255f6

┌─id─┬─column1─┐
│  3 │ ghi     │
│  4 │ jkl     │
└────┴─────────┘

2 rows in set. Elapsed: 0.002 sec.

clickhouse02 :)
clickhouse02 :)
clickhouse02 :) SELECT *
                FROM db1.dist_table

SELECT *
FROM db1.dist_table

Query id: e609dec6-ba15-453a-962a-31e9a6ee643f


0 rows in set. Elapsed: 0.002 sec.

Received exception from server (version 23.3.1):
Code: 60. DB::Exception: Received from localhost:9000. DB::Exception: Table db1.dist_table doesn't exist. (UNKNOWN_TABLE)

clickhouse02 :)
clickhouse02 :)
clickhouse02 :) exit
Bye.
root@docker:~/inception_infra/clickhouse-sharded-docker#
root@docker:~/inception_infra/clickhouse-sharded-docker# docker exec -it clickhouse01 clickhouse-client
ClickHouse client version 23.3.1.2823 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 23.3.1 revision 54462.

Warnings:
 * Linux is not using a fast clock source. Performance can be degraded. Check /sys/devices/system/clocksource/clocksource0/current_clocksource

clickhouse01 :)
clickhouse01 :)
clickhouse01 :) SELECT *
                FROM db1.dist_table

SELECT *
FROM db1.dist_table

Query id: eb6b9e14-b3be-4cda-bc6d-9841e82f723e

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘
┌─id─┬─column1─┐
│  3 │ ghi     │
│  4 │ jkl     │
└────┴─────────┘

4 rows in set. Elapsed: 0.005 sec.

clickhouse01 :)
```
