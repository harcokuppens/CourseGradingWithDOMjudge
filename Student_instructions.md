# Student instructions

This documents contains general information about the use of DOMjudge in the course that you can use to test your solutions.

* As a student you should have received on your student mail the user credentials and the url of the DOMjudge server.
* After logging in you can submit your source code (you can select multiple files) for a given problem. 
  If your solution fails, you can see what went wrong on the sample test cases. [^1]  
* For each problem ***X*** there are 2 problem entries in DOMjudge:
  
  * problem ***X***

    The practicing instance of the problem. The idea is that you test your code on this problem.  You can submit your code as many times as needed. It is even possible to download the sample test cases from the problem, and test your solution locally on your own machine.

  * problem  **grading**_***X***

    The grading instance of the problem. The idea is that you submit your code to this problem. If something goes wrong, you can submit again. The last submission to this problem will be used for the final assessment. This problem will only available until the submission deadline.

* The time limits for the problems are language-dependent, so you should be able to write a correct solution 
  for any sufficiently efficient algorithm in any of the available languages.
* The scoreboard exists because DOMjudge is made for programming contests, but you can ignore it here. 
  The only thing that matters is whether or not your solution passes all test cases for a problem. [^2]
* Do not use the clarification request system in DOMjudge, but just send us an email if you have any questions or issues.
* The DOMjudge version used is 8.2.3 and it supports the following language versions:

      * Python 3.9.16 (7.3.11+dfsg-2+deb12u1, Feb 02 2024, 18:54:53) [PyPy 7.3.11 with GCC 12.2.0]
      * javac 17.0.11
      * gcc (Debian 12.2.0-14) 12.2.0
      * g++ (Debian 12.2.0-14) 12.2.0

  You can easily fetch these results with the script [domjudge-language-versions](https://raw.githubusercontent.com/harcokuppens/CourseGradingWithDOMjudge/main/scripts/domjudge-language-versions) on any machine running docker.


 [^1]: If you cannot figure out your mistake, feel free to contact us.
 
 [^2]: In programming contests, the winner is determined by first the number of problems solved and then the sum of submission times for correct problems (plus time penalties for incorrect solutions).