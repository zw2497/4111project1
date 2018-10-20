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

## SQL schema
```
CREATE TABLE users(
    u_id        SERIAL PRIMARY KEY,
    email       text NOT NULL,
    name        text NOT NULL
);

CREATE TYPE o_type AS ENUM ('course', 'event');
CREATE TABLE organizations_create(
    o_id        SERIAL PRIMARY KEY,
    Description text,
    name        text NOT NULL,
    create_time date NOT NULL,
    creator_id  int NOT NULL REFERENCES users (u_id),
    type        o_type NOT NULL
);

CREATE TABLE enroll(
    user_id     int REFERENCES users (u_id),
    org_id      int REFERENCES organizations_create(o_id),
    PRIMARY KEY (user_id, org_id)
);

CREATE TABLE terms(
    t_id        SERIAL PRIMARY KEY,
    semester    text NOT NULL,
    year        interval YEAR NOT NULL
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
CREATE TYPE pin_type        AS ENUM ('pined', 'unpined');
CREATE TABLE question_belong_ask(
    q_id        SERIAL PRIMARY KEY,
    creator     int NOT NULL REFERENCES users(u_id),
    org_id      int NOT NULL REFERENCES organizations_create(o_id) ON DELETE CASCADE,
    up_number   int DEFAULT 0,
    down_number int DEFAULT 0,
    create_time date NOT NULL,
    solved_type resolve_type NOT NULL,
    public_type public_type NOT NULL,
    views       int NOT NULL,
    content     text NOT NULL,
    update_time date NOT NULL,
    pin         pin_type NOT NULL DEFAULT 'unpined',
    tag_id      int NOT NULL REFERENCES tags(t_id)
);

CREATE TABLE comments(
    c_id        int PRIMARY KEY,
    create_time date NOT NULL,
    creator_id  int REFERENCES users (u_id),
    up_number   int DEFAULT 0,
    down_number int DEFAULT 0,
    q_id        int NOT NULL REFERENCES question_belong_ask(q_id) ON DELETE CASCADE
);

CREATE TABLE reply( 
    r_id SERIAL PRIMARY KEY,
    comment_source int REFERENCES comments(c_id),
    comment_target int REFERENCES comments(c_id),
    UNIQUE(comment_source, comment_target)
);

CREATE TYPE vote_type        AS ENUM ('up', 'down');
CREATE TABLE vote_question(
    question_id int NOT NULL REFERENCES question_belong_ask (q_id),
    user_id int NOT NULL REFERENCES users (u_id),
    vote vote_type NOT NULL,
    PRIMARY KEY (question_id, user_id)
);

CREATE TABLE vote_comment(
    comment_id int NOT NULL REFERENCES comments(c_id) ,
    user_id int NOT NULL REFERENCES users(u_id) ,
    Vote vote_type NOT NULL,
    PRIMARY KEY (comment_id, user_id)
);
```













