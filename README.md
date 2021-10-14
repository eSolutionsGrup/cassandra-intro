# Intro to cassandra


This repository provides a docker-compose needed to start a 3 node cassandra cluster

## Starting the cluster in detached mode
```
$ git clone https://github.com/eSolutionsGrup/cassandra-intro.git

$ cd cassandra-intro

$ docker-compose up -d
```

### Get container name with docker ps and execute cqlsh interactively
```
$ docker exec -ti {container_name} cqlsh
```


### Create keyspace
```
CREATE KEYSPACE bdwvideo WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'} AND durable_writes = true;
use bdwvideo;
```

### Create users tables
```
CREATE TABLE user_credentials(
email text,
password text,
userid uuid,
primary key (email)
);

CREATE TABLE users(
userid uuid,
firstname text,
lastname text,
email text,
created_date timestamp,
primary key (userid)
);
```

### Insert into users table
```
INSERT INTO users (userid , firstname , lastname , email , created_date) VALUES  ( uuid(),'john','doe','john@doe', '2021-10-09');

SELECT uuid from users;

UPDATE users set lastname='Johnson' where userid = ;

SELECT userid from users;

UPDATE users set lastname='Johnson' where userid = a62485ed-f11d-485e-bd24-14b25a5ba0ff;

);
```


### Create videos tables
```
CREATE TABLE videos(
videoid uuid,
userid uuid,
name text,
description text,
location text,
location_type int,
preview_image_location text,
tags set<text>,
added_date timestamp,
primary key (videoid)
);

CREATE TABLE  user_videos(
userid uuid,
added_date timestamp,
videoid uuid,
name text,
preview_image_location text,
primary key (userid, added_date, videoid)
) with clustering order by (
added_date desc,
videoid asc);
```

### Create / insert into latest videos tables
```
CREATE TABLE  latest_videos(
video_date text,
added_date timestamp,
videoid uuid,
name text,
preview_image_location text,
primary key (video_date, added_date, videoid))
 with clustering order by (added_date desc, videoid asc);

insert into latest_videos ( video_date , added_date , videoid , name , preview_image_location ) VALUES ( '2021-10-12', toTimestamp(now()), uuid(),'best name','best preview');

insert into latest_videos ( video_date , added_date , videoid , name , preview_image_location ) VALUES ( '2021-10-12', toTimestamp(now()), uuid(),'best name','best preview');
insert into latest_videos ( video_date , added_date , videoid , name , preview_image_location ) VALUES ( '2021-10-12', toTimestamp(now()), uuid(),'best name','best preview');
```

### Create / insert / query into latest videos bucketed tables
```
CREATE TABLE  latest_videos_bucketed(
video_date text,
bucket int,
added_date timestamp,
videoid uuid,
name text,
preview_image_location text,
primary key ((video_date, bucket), added_date, videoid))
 with clustering order by (added_date desc, videoid asc);


insert into latest_videos_bucketed ( video_date ,bucket, added_date , videoid , name , preview_image_location ) VALUES ( '2021-10-12',0, toTimestamp(now()), uuid(),'best name','best preview');

insert into latest_videos_bucketed ( video_date ,bucket, added_date , videoid , name , preview_image_location ) VALUES ( '2021-10-12',1, toTimestamp(now()), uuid(),'best name','best preview');


select * from latest_videos_bucketed where video_date = '2021-10-12' and bucket in (0,1);

select * from latest_videos_bucketed where video_date = '2021-10-12' and bucket = 0;
select * from latest_videos_bucketed where video_date = '2021-10-12' and bucket = 1;
```

### Stress test
```
https://cassandra.apache.org/doc/latest/cassandra/tools/cassandra_stress.html
```
