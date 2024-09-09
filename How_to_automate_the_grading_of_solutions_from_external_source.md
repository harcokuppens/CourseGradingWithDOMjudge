# How to automate the grading of solutions from external source

**Table of Contents**

<!--ts-->
* [How to automate the grading of solutions from external source](#how-to-automate-the-grading-of-solutions-from-external-source)
   * [setup domjudge for grading which is invisible for students](#setup-domjudge-for-grading-which-is-invisible-for-students)
      * [Create a special grading contest with team category "grading"](#create-a-special-grading-contest-with-team-category-grading)
      * [create grading teams and users](#create-grading-teams-and-users)
   * [Add a problem for grading in the grading contest](#add-a-problem-for-grading-in-the-grading-contest)
   * [Collect grading solutions for a problem](#collect-grading-solutions-for-a-problem)
      * [cleanup tricks](#cleanup-tricks)
   * [How to RUN the grading of solutions in DOMjudge](#how-to-run-the-grading-of-solutions-in-domjudge)
   * [Fetch grading results](#fetch-grading-results)
<!--te-->

## setup domjudge for grading which is invisible for students

### Create a special grading contest with team category "grading"

     create a 'grading' contest where we give access to only teams in the "System" category.

        SEE:  grading_contest_with_boxesgrading_problem_linked_to_run_non_lazy_DOMjudge.pdf

     note: the students teams are in the "participation" category, and have therefor no access
           to the grading contest.
     note: attaching problems to grading contest we do later!

### create grading teams and users

    Per student team we create both a gradinguserX and gradingteamX in domjudge,
    where X is a 3 digit number filled with team number padded with zeros.
    We give all grading users the same password, so that we can conveniently
    commit for multiple users in a loop.
    The grading teams are put in the "System" category so that they automatically
    get access to the grading contest!

    eg. for team 12
            user:  gradinguser012
            team:  gradingteam012


    note: initially I thought I could gave a team label to the submissed file, but that was not possible for java files for which the
          filename must match the class name inside. Also some student groups still did not hold to the single file requirement, and their domjudge submission contained several source files.
          Thus we needed to make a grading team per student group so we
           - can keep original file names
           - can make a folder, named with team id, which contains multiple sourcefiles


    The process of creating grading users and teams can be done automatically with the
    shell scripts in create_grading_teams_and_accounts/.
    Intstructions are at:

       create_grading_teams_and_accounts/__README__create_grading_teams_and_accounts.txt

    note:  all grading users are create with the same password,
           The password used in the script is:

             password="qd4WHeJXbd"




## Add a problem for grading in the grading contest

say we have a problem called 'boxes' then create a new problem called 'gradingboxes' where we
replace the answers with new answers for grading (which students never saw)

upload this grading problem to domjudge and do NOT attach it to a contest!! note: problems are only
visible to students when they are attached to a contest where they have access to as an team. By
default a team has no access to any contest.

then edit the grading contest, and its edit from at the end you can attach the grading problem to
this grading contest. When doing this it is IMPORTANT that you set "Lazy eval" to "No". This makes
that for each submission all testcases are always run. When "Lazy eval" is set to "Yes" then the
judging is stopped early if any of the testcases is not successful. This reduces the load, and the
student can focus on this specific testcase to fix his code.

SEE: grading_contest_with_boxesgrading_problem_linked_to_run_non_lazy_DOMjudge.pdf

create grading problem and attach to

## Collect grading solutions for a problem

Collect from the students all solutions and put them all in folder where each team gets a subfolder
named by the team id. In this subfolder are the source files of the team.

An example folder, where we also allow multiple source files per team:

      $ tree  solutionsteams/
      ..

      ├── 155
      │   └── working_stuff.py
      ├── 158
      │   └── FinalProject.py
      ├── 16
      │   └── main.cpp
      ├── 161
      │   └── algo.py
      ├── 162
      │   └── pot1.py
      ├── 169
      │   └── main.cpp
      ├── 17
      │   ├── Box.java
      │   ├── Main.java                   => multiple files in submission
      │   └── Sort.java

      ..

### cleanup tricks

However sometimes we get our external source delivered per team as a folder containing also some
documentation, or the source is in a zip, etc..

For example $ tree dirty_solutionsteams/ ..

      ├── 169
      |   ├── report.pdf
      │   └── main.cpp
      ├── 17
      │   └── team17.zip                    => zip file containing code and documentation

      ..


Some folders have a zip containing the source code. We automatically unzip and remove the zip file:

    # get zip paths in array
    readarray -t myarray < <(find . -name '*.zip')

    # unzip the zips
    for p in "${myarray[@]}"; do d=$(dirname "$p"); b=$(basename "$p"); cd $d; unzip "$b"; rm "$b"; cd -; done

Finally we need to manually cleanup all folder from pdf's text etc..

## How to RUN the grading of solutions in DOMjudge

Setup some paths; change to your specific case

    export TEAM_SOLUTIONS_DIR="./solutionsteams/"
    export CODEDIR="./bin/"

Per team we an submit a solution for grading:

    python3 $CODEDIR/submit -y -q --auth gradinguser036:qd4WHeJXbd --url https://domjudge.science.ru.nl/ --contest grading --problem boxesgrading solutions/reruns_on_fixed_code/36/App.java

But we can do it automatically for multiple users

    # all gradingusers have same password
    password=qd4WHeJXbd
    for f in $TEAM_SOLUTIONS_DIR/*
    do
        num=$(basename $f)
        # commit each team sources to its own grading team using its own grading user!
        printf -v user "gradinguser%03d" $num
        python3 $CODEDIR/submit -y -q --auth "${user}:${password}" --url https://domjudge.science.ru.nl/ --contest grading --problem boxesgrading  $f/* ;
        printf -- "--------\n"
        sleep 5;
    done


IMPORTANT: sleep 5 is important so that we not overload the domserver with posts of files to server!

## Fetch grading results

Finally fetching and processing grading result is done in exact the same manner as for

...
