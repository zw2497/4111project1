# 4111project1
# COMS 4111 Database Project 1
#course/4111

## 1. Team
* Zhicheng Wu           UNI: `zw2497` 
* Pengchong Wang        UNI: `pw2480`      


## 2. Project Description 
We would like to implement a /Piazza/ on our own and add a few more functions to improve it.  Student can interact with instructors with Posts. One of the functions we want to add is *Votes*. Further, we will reorder the *post list*  and *answer list* by this score like /Reddit/. The other additional function is that we will expand the domain of this tool to *Event Organizer.* This project will include entities such as *Post, Comment, User, Tag, Course, Vote, Instructor, Student* and so on. *User* can be *instructor and Student*.  *Post* have attributes such as *Content, Views, Thank_Number, Status*. Course will have some attributes such as *Number, Term, Name*. We will make up our own data by conversing  content from piazza and inserting some random users. User can also enrolled in many courses. They can reply to comments and posts, add questions to their favorite and vote for the questions they think is valuable. Specific user can pin questions or comments and choose whether questions are resolved.

## 3. Contingency Plan
I will not implement the entities such as *Vote, Organizer, Event*. only implement the basic function of piazza with 4 entities: *User, Course, Post and Comment*.

## 4. SQL schema
```
CREATE TABLE users(
    u_id        SERIAL PRIMARY KEY,
    email       text NOT NULL,
    password    text NOT NULL,
    name        text NOT NULL
);

CREATE TYPE o_type AS ENUM ('course', 'event');
CREATE TABLE organizations_create(
    o_id        SERIAL PRIMARY KEY,
    name        text NOT NULL,
    create_time date NOT NULL,
    creator_id  int NOT NULL REFERENCES users (u_id),
    type        o_type NOT NULL,
    description text
);

CREATE TYPE enroll_type AS ENUM ('instructor', 'student');
CREATE TABLE enroll(
    user_id     int REFERENCES users (u_id),
    org_id      int REFERENCES organizations_create(o_id),
    type        enroll_type NOT NULL,
    PRIMARY KEY (user_id, org_id)
);

CREATE TABLE terms(
    t_id        SERIAL PRIMARY KEY,
    semester    text NOT NULL,
    year        int NOT NULL
);

CREATE TABLE courses(
    course_id   int PRIMARY KEY REFERENCES organizations_create (o_id)
);

CREATE TABLE offer(
    o_id        SERIAL PRIMARY KEY,
    course_id   int NOT NULL REFERENCES courses (course_id),
    term_id     int NOT NULL REFERENCES terms (t_id),
    UNIQUE (course_id, term_id)
);

CREATE TABLE events(
    event_id    int PRIMARY KEY REFERENCES organizations_create (o_id)
);

CREATE TABLE tags(
    t_id        SERIAL PRIMARY KEY,
    content     text NOT NULL
);

CREATE TYPE resolve_type    AS ENUM ('resolved', 'unresolved');
CREATE TYPE public_type     AS ENUM ('public', 'private');
CREATE TYPE pin_type        AS ENUM ('pinned', 'unpinned');
CREATE TYPE question_type   AS ENUM ('question', 'note');
CREATE TABLE question_belong_ask(
    q_id        SERIAL PRIMARY KEY,
    creator_id  int NOT NULL REFERENCES users(u_id),
    org_id      int NOT NULL REFERENCES organizations_create(o_id) ON DELETE CASCADE,
    create_time date NOT NULL,
    solved_type resolve_type,
    public_type public_type NOT NULL,
    views       int DEFAULT 1,
    title       text NOT NULL,
    content     text,
    update_time date NOT NULL,
    pin         pin_type NOT NULL DEFAULT 'unpinned',
    tag_id      int NOT NULL REFERENCES tags(t_id),
    q_type      question_type NOT NULL
);

CREATE TABLE comments(
    c_id        int,
    create_time date NOT NULL,
    creator_id  int NOT NULL REFERENCES users (u_id),
    content     text NOT NULL,
    q_id        int REFERENCES question_belong_ask (q_id) ON DELETE CASCADE,
    PRIMARY KEY (c_id, q_id)
);

CREATE TABLE reply( 
    source int,
    source_qid int,
    target int,
    target_qid int,
    FOREIGN KEY (source, source_qid) REFERENCES comments (c_id, q_id),
    FOREIGN KEY (target, target_qid) REFERENCES comments (c_id, q_id),
    PRIMARY KEY (source, source_qid, target, target_qid)
);

CREATE TYPE vote_type        AS ENUM ('up', 'down');
CREATE TABLE vote_question(
    question_id int NOT NULL REFERENCES question_belong_ask (q_id),
    user_id int NOT NULL REFERENCES users (u_id),
    vote vote_type NOT NULL,
    PRIMARY KEY (question_id, user_id)
);

CREATE TABLE vote_comment(
    comment_id int NOT NULL,
    comment_qid int NOT NULL,
    user_id int NOT NULL REFERENCES users(u_id) ,
    Vote vote_type NOT NULL,
    PRIMARY KEY (comment_id, user_id)
);
```

## Populate the tables
### **Below content is from** 
* https://piazza.com/class/jgwnwiy186d6pu?cid=148
* https://piazza.com/class/jgwnwiy186d6pu?cid=210
* https://piazza.com/class/jgwnwiy186d6pu?cid=233
```
INSERT INTO users(email, password, name) VALUES
('ewu@cs.columbia.edu ','123456','Eugene Wu');

INSERT INTO organizations_create (name, create_time, creator_id, type, description) VALUES
('DATABASES W4111: Introduction to Databases', '2018-9-2', 1, 'course', null);

INSERT INTO courses (course_id) VALUES
(1);

INSERT INTO tags (content) VALUES ('exam');

INSERT INTO question_belong_ask (creator_id, org_id, create_time, solved_type, public_type, title, content, update_time, pin, tag_id, q_type) VALUES
(1, 1, '2018-10-12', null, 'public', 'Moved OH to 10/17 3:30', 'I have moved my office hours to Wednesday 10/17 at 3:30-4:30PM for last minute midterm questions.  I will not have thursday office hours after class.', '2018-10-12', 'pinned', 1, 'note');

INSERT INTO tags (content) VALUES ('other');

INSERT INTO question_belong_ask (creator_id, org_id, create_time, solved_type, public_type, title, content, update_time, pin, tag_id, q_type) VALUES
(1, 1, '2018-10-12', null, 'public', 'midterm study guide', $$I'd like to point to the study guide compiled by students from the students from last year's class.  I have not fully checked it for correctness, but it seemed to help previous students.  I encourage the class to edit and improve the wiki! https://github.com/w4111/scribenotes/wiki/Midterm-Study-Guide$$, '2018-10-7', 'pinned', 1, 'note');

INSERT INTO question_belong_ask (creator_id, org_id, create_time, solved_type, public_type, title, content, update_time, pin, tag_id, q_type) VALUES
(1, 1, '2018-10-7', 'resolved', 'public', 'The Web as a Database', $$Here is a great article about thinking about web scraping as a database, which is related to the last demo from the lecture discussing user defined functions.

https://www.mixnode.com/blog/posts/turn-the-web-into-a-database-an-alternative-to-web-crawling-scraping$$, '2018-10-7', 'unpinned', 2, 'question');

UPDATE question_belong_ask SET q_type = 'note' WHERE q_id = 3;

INSERT INTO users(email, password, name) VALUES
('Anonymous@piazza.edu ','123456','Anonymous');

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-7', 2, 3, $$There's no article here :)$$);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-7', 1, 3, $$argh. fixed!$$);
INSERT INTO reply (comment_source, comment_target, question_id) VALUES
(2,1,3);

INSERT INTO users(email, password, name) VALUES
('IvyChen@columbia.edu ','123456','Ivy Chen');
INSERT INTO users(email, password, name) VALUES
('danyangxiang@columbia.edu ','123456','danyang xiang');

INSERT INTO question_belong_ask (creator_id, org_id, create_time, solved_type, public_type, title, content, update_time, pin, tag_id, q_type) VALUES
(2, 1, '2018-10-17', 'unresolved', 'public', $$Clarification on "Writing Another Person's UNI"$$,$$Hi,

Just wanted to ask for a clarification on how the "writing another person's UNI" works on the exam? I think I gapped out a little when I heard this initially being brought up in class, but according to my understanding, basically another person would consent to having any problem that I did wrong/didn't do be substituted by his/hers if they did it correctly?

Thanks a lot!$$, '2018-10-17', 'unpinned', 1, 'question');

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-17', 3, 4, $$Yep, you write the UNI of the student whose answer you'd like to substitute in place of your own.$$);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-17', 2, 4, $$Cool, does this substitution apply to one/a few questions we designate or does it automatically apply to all questions? The latter would make less sense of course, just wanted to make sure.$$);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-17', 3, 4, $$We will specify the one question where this is applicable on the exam.$$);
INSERT INTO reply (comment_source, comment_target, question_id) VALUES
(5,4,4);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-17', 2, 4, $$Got it, that makes sense. Thanks!$$);
INSERT INTO reply (comment_source, comment_target, question_id) VALUES
(6,5,4);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-18', 4, 4, $$So if I write other student's UNI, will his/her answer replace mine or would I be given the credit of the higher one of the credits of  my answer and that student's?$$);

INSERT INTO comments(create_time, creator_id, q_id, content) VALUES
('2018-10-18', 3, 4, $$ The higher one$$);
INSERT INTO reply (comment_source, comment_target, question_id) VALUES
(8,7,4);

```

```
insert into users(email, password, name) values
('JohnPaisley@ee.columbia.edu','123456','John Paisley'),
('CYLin@ee.columbia.edu','123456','Chingyung Lin'),
('AshishMaheshwari@columbia.edu','123456','AshishMaheshwari'),
('my2398@columbia','123456','ming yao'),
('hx6494@columbia','123456','he xiao'),
('py3981@columbia','123456','peng ying');

insert into organizations_create(name, create_time, creator_id, type, description) values
('EECSE6720 BAYESIAN MOD MACHINE LEARNING','2018-9-2',5,'course',null),
('EECSE6893 TOPICS-INFORMATION PROCESSING','2018-9-2',6,'course',null),
('EECSE6895 Advanced Big Data Analytics','2018-2-2',9,'course',null),
('ECBMB4040 NEURAL NETWRKS & DEEP LEARNING','2018-2-2',7,'course',null),
('ELENE4702 DIGITAL COMMUNICATIONS','2018-2-2',8,'course',null),
('ELENE4810 DIGITAL SIGNAL PROCESSING','2018-2-2',9,'course',null),
('ELENE4944 PRINCIPALS OF DEVICE MICROFAB','2018-2-2',10,'course',null),
('ELENE4998 INTERMEDIATE PROJECTS','2018-2-2',7,'course',null),
('ELENE6333 SEMICONDUCTOR DEVICE PHYSICS','2018-2-2',5,'course',null);

INSERT INTO courses (course_id) VALUES
(2),
(3),
(4),
(5),
(6),
(7),
(8),
(9),
(10);

INSERT INTO tags (content) VALUES 
('HW1'),
('HW2'),
('HW3'),
('HW4'),
('HW5'),
('PROJ1'),
('PROJ2'),
('PROJ3'),
('PROJ4');

INSERT INTO question_belong_ask (creator_id, org_id, create_time, solved_type, public_type, title, content, update_time, pin, tag_id, q_type) VALUES
(6, 4, '2018-10-12', null, 'public', 'Clarifications about HW3 Q1', 'For the wiki data, you are not required to download the whole 15GB dataset. You can download smaller datasets (such as enwikivoyage, enwikinews, etc.) and merge them to create you target dataset for clustering. This will also provide you with ground truth labels to compare your clustering results with.', '2018-10-12', 'pinned', 1, 'note'),
(6, 4, '2018-9-12', null, 'public', 'Some LIBSVM format datasets', 'Here is a link where you could find some LIBSVM format datasets, which may be useful for the regression problem.', '2018-10-12', 'pinned', 1, 'note'),
(6, 4, '2018-9-22', null, 'public', 'Mode of communication for queries and doubts', 'Reiterating over earlier announcements: please use Piazza for queries, so all TAs / students can pitch in', '2018-10-14', 'pinned', 3, 'note'),
(5, 3, '2018-10-3', null, 'public', 'Sidharth’s OH this week shifted to Friday', 'I am traveling today and hence will be shifting my OH for this week to Friday (10/26): 10 am - 12 noon. Outside of this change, my usual schedule remains the same.', '2018-10-12', 'pinned', 7, 'note'),
(5, 3, '2018-10-14', 'unresolved','public', 'Request for extension of deadline for Homework 2', 'Would it be possible to please extend the deadline by a 2-3 days?', '2018-10-14', 'unpinned', 3, 'question'),
(5, 3, '2018-9-28', 'resolved', 'public', 'The value of M and N?', 'In problem2, we have to initialize each ui and vj, but whats the value of M and N? N can be known from movies.txt, but what about N?', '2018-10-12', 'unpinned', 7, 'question');

insert into comments(create_time, creator_id, content, q_id) values
('2018-10-1', 7, 'This assignment has a lot of complexity when it comes to coding the work - and I went to 3 TA sessions but they were always full. Please extend the deadline and have more office hours so they can help us out', 9),
('2018-10-3', 8, 'See the max() and unique() items in ratings.txt to find M, N', 10);

```

## Run some queries

#### Display the number of comments of every question which have more than one comment
```
SELECT q.q_id AS questionid, q.title AS title, Count(*) AS comment_num
FROM comments AS c, question_belong_ask AS q
WHERE c.q_id = q.q_id
GROUP BY q.q_id
Having Count(*) > 0
ORDER BY q.q_id
```

#### Display all replies in piazza
```
SELECT us.name AS sourcename, cs.content AS sourcecontent, ut.name AS targetname, ct.content AS targetcontent, q.title AS question
FROM users AS us, comments AS cs, reply AS r, comments AS ct, users AS ut, question_belong_ask AS q
WHERE us.u_id = cs.creator_id AND ut.u_id = ct.creator_id AND cs.c_id = r.comment_source AND ct.c_id = comment_target AND r.question_id = q.q_id
```

#### Display the quetion which has reply to it's comments
```
select count(distinct q.q_id)
from question_belong_ask AS q
where q.q_id in(SELECT q.q_id AS questionid
FROM comments AS c, question_belong_ask AS q
WHERE c.q_id = q.q_id)
```













