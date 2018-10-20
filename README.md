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

CREATE TABLE organizations(
    o_id        SERIAL PRIMARY KEY,
    name        text NOT NULL,
    create_time date NOT NULL,
    creator_id  int NOT NULL REFERENCES users (u_id),
    type        text NOT NULL CHECK (type in ('Course', 'Event'))
);

CREATE TABLE terms(
    t_id        SERIAL PRIMARY KEY,
    semester    text NOT NULL,
    year        interval YEAR NOT NULL
);

CREATE TABLE courses(
    course_id   int PRIMARY KEY REFERENCES organizations (o_id)
);

CREATE TABLE offer(
    o_id        SERIAL PRIMARY KEY,
    course_id   int REFERENCES courses (course_id),
    term_id     int NOT NULL REFERENCES terms (t_id),
    UNIQUE (course_id, term_id)
);

CREATE TABLE events(
    event_id    int PRIMARY KEY REFERENCES organizations (o_id)
);

CREATE TABLE Question_belong(
Q_id int PRIMARY KEY,
Up_number int,
Down_number int,
Creation_time varchar(100) NOT NULL,
solved_status enum('Resolved','Unresolved') NOT NULL,
Public_status enum('Private','Public') NOT NULL,
Views int NOT NULL,
Content varchar(100) NOT NULL,
Last_updated_time varchar(100) NOT NULL,
Pinned enum('Pinned','Unpinned'),
Lable varchar(100) NOT NULL,
O_id int NOT NULL,
Email varchar(100) NOT NULL,
FOREIGN KEY (Content) REFERENCES Tag(Content),
FOREIGN KEY (O_id) REFERENCES Organization(O_id)
ON DELETE CASCADE,
FOREIGN KEY (Email) REFERENCES User(Email)
)

CREATE TABLE Tag(
Content varchar(100) PRIMARY KEY
)

CREATE TABLE Comment(
C_id int PRIMARY KEY,
Creation_time varchar(100) NOT NULL,
Down_number int,
Up_number int,
Q_id int NOT NULL,
Email varchar(100) NOT NULL,
FOREIGN KEY (Q_id) REFERENCES Question_belong(Q_id)
ON DELETE CASCADE,
FOREIGN KEY (Email) REFERENCES User(Email)
)

CREATE TABLE Comment_Comment( 
Comment_target int,
C_id int,
FOREIGN KEY (C_id) REFERENCES Comment(C_id)
)

CREATE TABLE Vote_for_question(
Q_id int NOT NULL,
Email varchar(100) NOT NULL,
Up_Down enum('Up','Down'),
PRIMARY KEY (Q_id, Email),
FOREIGN KEY (Q_id) REFERENCES Question_belong(Q_id),
FOREIGN KEY (Email) REFERENCES User(Email)
)

CREATE TABLE Vote_for_comment(
C_id int NOT NULL,
Email varchar(100) NOT NULL,
Up_Down enum('Up','Down'),
PRIMARY KEY (C_id, Email),
FOREIGN KEY (C_id) REFERENCES Comment(C_id),
FOREIGN KEY (Email) REFERENCES User(Email)
)

CREATE TABLE Attend(
Email varchar(100) NOT NULL,
O_id int NOT NULL,
PRIMARY KEY (Email, O_id),
FOREIGN KEY (Email) REFERENCES User(Email),
FOREIGN KEY (O_id) REFERENCES Organization(O_id)
)

CREATE TABLE Create_Instruct(
Email varchar(100),
O_id int,
PRIMARY KEY (Email, O_id),
FOREIGN KEY (Email) REFERENCES User(Email),
FOREIGN KEY (O_id) REFERENCES Organization(O_id)
)
```













