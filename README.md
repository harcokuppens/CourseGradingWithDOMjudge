# CourseGradingWithDOMjudge

Automatically grade submitted programs in a course using DOMjudge.

## Introduction

DOMjudge is a system for running programming contests, like the ICPC regional and
world finals programming contests. Teams submit solutions to problems in the contest,
and can see how well they do by looking at a scoreboard which gives an ranking
overview how well teams are doing.

The DOMjudge system is not designed to be used by courses, however it would be a
handy tool for courses because it provides automated evaluation of student code
submissions.

In the following document I explain the **idea's** behind my configuration of
DOMjudge for use in a course:

- [Explanation course setup](Explanation_course_setup.md)

After reading this document you should have a general idea how we use DOMjudge in the
course.

To really start a course in DOMjudge follow the instructions at:

- [Setting up domjudge for a course](Setting_up_domjudge_for_a_course.md)

In this document I give **step by step setup and usage instructions** to apply
DOMjudge in a grading course. This document brings the idea's of the previous
document in practice. In this document I use several scripts. The following document
gives more information about the scripts:

- [Overview and usage descriptions of scripts](scripts.md)

## Extra background information

For more generaral information about programming contests their specifications and
tooling around read:

- [Overview programming contests, specifications and tooling](Programming_contests_specifications_and_tooling.md)

The scripts used in this repository use the **REST API** referenced in above
document. We use BAPCtools to develop/create and test a problem. When we have a good
problem for the course, only then we import it to DOMjudge. Within DOMjudge there is
a special Test contest which only administrators have access to. We use this Test
contest to also test a new contest problem on the DOMjudge server before using it in
the course.
