# Student instructions

This documents contains general information about the use of DOMjudge in the course that you can use
to test your solutions.

- As a student you should have received on your student mail the user credentials and the url of the
  DOMjudge server.
- After logging in you can submit your source code (you can select multiple files) for a given
  problem. If your solution fails, you can see what went wrong on the sample test cases. [^1]
- For each problem **_X_** there are 2 problem entries in DOMjudge:

  - problem **_X_**

    The practicing instance of the problem. The idea is that you test your code on this problem. You
    can submit your code as many times as needed. It is even possible to download the sample test
    cases from the problem, and test your solution locally on your own machine.

  - problem **grading**\_**_X_**

    The grading instance of the problem. The idea is that you submit your code to this problem. If
    something goes wrong, you can submit again. The last submission to this problem will be used for
    the final assessment. This problem will only available until the submission deadline.

- The time limits for the problems are language-dependent, so you should be able to write a correct
  solution for any sufficiently efficient algorithm in any of the available languages.
- The scoreboard exists because DOMjudge is made for programming contests, but you can ignore it
  here. The only thing that matters is whether or not your solution passes all test cases for a
  problem. [^2]
- Do not use the clarification request system in DOMjudge, but just send us an email if you have any
  questions or issues.
- The DOMjudge version used is 8.3.1 and it supports the following language versions:

      * Python 3.9.16 (7.3.11+dfsg-2+deb12u1, Feb 02 2024, 18:54:53) [PyPy 7.3.11 with GCC 12.2.0]
      * javac 17.0.15
      * gcc (Debian 12.2.0-14+deb12u1) 12.2.0
      * g++ (Debian 12.2.0-14+deb12u1) 12.2.0
      * kotlinc-jvm 2.1.21 (JRE 17.0.15+6-Debian-1deb12u1)
      * rustc 1.88.0 (6b00bc388 2025-06-23)

  Note that we used the harcokuppens/judgehost-extra-languages:8.3.1 image which is the
  domjudge/judgehost:8.3.1 image extended with rust and kotlin.  

[^1]: If you cannot figure out your mistake, feel free to contact us.
[^2]:
    In programming contests, the winner is determined by first the number of problems solved and
    then the sum of submission times for correct problems (plus time penalties for incorrect
    solutions).





    
