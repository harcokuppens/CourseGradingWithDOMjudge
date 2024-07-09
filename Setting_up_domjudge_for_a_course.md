


# Setting up DOMjudge for a course

**Table of Contents**
<!--ts-->
* [Setting up DOMjudge for a course](Setting_up_domjudge_for_a_course.md#setting-up-domjudge-for-a-course)
   * [A. Create a fresh and up to date DOMjudge installation](Setting_up_domjudge_for_a_course.md#a-create-a-fresh-and-up-to-date-domjudge-installation)
      * [A new installation](Setting_up_domjudge_for_a_course.md#a-new-installation)
      * [Reset and update existing installation](Setting_up_domjudge_for_a_course.md#reset-and-update-existing-installation)
   * [B. Basic configuration](Setting_up_domjudge_for_a_course.md#b-basic-configuration)
      * [1. Make demo contest private, and delete demo user](Setting_up_domjudge_for_a_course.md#1-make-demo-contest-private-and-delete-demo-user)
      * [2. Make DOMjudge use external id's for configuration data](Setting_up_domjudge_for_a_course.md#2-make-domjudge-use-external-ids-for-configuration-data)
   * [C. Setup and usage DOMjudge for the course](Setting_up_domjudge_for_a_course.md#c-setup-and-usage-domjudge-for-the-course)
      * [1. Setup part of a course for a specific teams configuration](Setting_up_domjudge_for_a_course.md#1-setup-part-of-a-course-for-a-specific-teams-configuration)
         * [Create teams](Setting_up_domjudge_for_a_course.md#create-teams)
         * [Mailing credentials to students](Setting_up_domjudge_for_a_course.md#mailing-credentials-to-students)
         * [Create contest](Setting_up_domjudge_for_a_course.md#create-contest)
      * [2. Creating, adding, submitting, and downloading results of a problem](Setting_up_domjudge_for_a_course.md#2-creating-adding-submitting-and-downloading-results-of-a-problem)
         * [Create problem for the course](Setting_up_domjudge_for_a_course.md#create-problem-for-the-course)
         * [Add problem to the course](Setting_up_domjudge_for_a_course.md#add-problem-to-the-course)
         * [Students can submit code to the problem](Setting_up_domjudge_for_a_course.md#students-can-submit-code-to-the-problem)
      * [When the problem's deadline is reached, the teacher downloads its final submissions](Setting_up_domjudge_for_a_course.md#when-the-problems-deadline-is-reached-the-teacher-downloads-its-final-submissions)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: harcok, at: wo jul  3 12:02:45 CEST 2024 -->

<!--te-->


## A. Create a fresh and up to date DOMjudge installation

You do not want to load of one course to effect another course. So we advice to use per course a separate DOMjudge installation. 

We have two options, either do a new installation from scratch, or reset and update an existing installation of DOMjudge.

### A new installation

A new installation can be easily be done using by unning DOMjudge in docker by following this quickstart.
    https://github.com/harcokuppens/DOMjudgeDockerCompose/blob/main/README.md#quick-start

### Reset and update existing installation

If we already have DOMjudge installed, we can reset and update its installation:


    docker compose down         # stop everything, and remove the containers: domjudge server/judges and mariadb backend
    BACKUPFOLDER=/tmp/backup    # example BACKUPFOLDER
    sudo cp  -a . $BACKUPFOLDER # backup whole DOMjudge folder to BACKUPFOLDER
    sudo rm -r data             # reset DOMjudge
    git pull                    # to get newer versions of DOMjudge and mariadb, 
                                # or you can edit the version tags of images in the .env!
    docker compose up -d        # restart a possible newer version of domjudge using new containers and fresh data

For more explanation see the github project https://github.com/harcokuppens/DOMjudgeDockerCompose/ .

## B. Basic configuration

The default installation of DOMjudge needs some tweaking before we can use it. For example we disable the demo user for security reasonse. 

### 1. Make demo contest private, and delete demo user

First login as `admin` user with the password from the file `./data/passwords/admin.pw` in the installation folder.

By default a public demo contest is added to DOMjudge. This demo project is nice for students to first practice with DOMjudge.  However we want the DOMjudge website empty for external people, and have the demo project only available for our students. We do this by disabling public showing of demo contest:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Contests' 
    click on 'demo' contest at bottom of window
    click on 'Edit' button at bottom of window
    then change config of 'Demo' contest:
        'Enable public scoreboard' set from 'Yes' to 'No' 
    click on 'Save' button at bottom of window   
     
For security reasons we also remove the `demo` user by:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Users'
    on the row for the 'demo' user click at the end of line on the 'trashcan' icon
    and confirm by pressing the 'Delete' button in the popup window 

Now, when not logged in, you see nothing. When logged in, you can play with demo contest.
   
### 2. Make DOMjudge use external id's for configuration data

To make DOMjudge use external id's for configuration data instead of internal id's we do:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Configuration settings' 
    on the top of the window click on the 'External systems' tab
    at the 'Data source' field change the value 'all local' into the
            value 'configuration data external'    
    click on 'Save all changes' button at bottom of window

Using external id's simplifies setting up teams.

### 3. Setting language timefactors

The python and java languages are higher level languages then C and C++ and thereby having more overhead in executing their code, causing them to have higher execution times for similar code in low level languages. To compare solutions in different languages fairly we correct for higher execution times in higher level languages with a timefactor. 
We use the following timefactors:


| language | timefactor |
| --------- | ---------- |
| C | 1 |
| C++ | 1 |
| java | 3 |
| python | 4 |

This means that for a problem with a timelimit of 1 second, a C or C++ program must solve the problem within 1 second,
but a java program in 3 seconds, and a python program in 4 seconds. 

To set a timefactor for a language in DOMjudge we do:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Languages' 
    click on the edit icon behind the language you want to edit 
    in the edit form for the language set the timefactor field to the desired value  
    click on 'Save' button at bottom of window

### 4. Give teams more information about their submissions

DOMjudge by default does not allow teams to see the output of the output validator of a submission to the test samples.
In below instructions we enable showing them, so that students can see what is wrong with their submission. It can be either that 
their submission does not compile or has runtime errors. But it can also be possible that the submission just gives back the wrong result
value, in which case the expect sample output and the submissions output is shown next to each other. The sample outputs are available to the students, so no reason to hide it, and its very convenient to show this information so that students can immediately see what they did wrong.
In below instructions we also allow to download code from submissions. Can be handy to sometimes go back.

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Configuration settings' 
    on the top of the window click on the 'Display' tab      
    set "Show sample output"  to  "Yes"        
    set "Allow team submission download"  to Yes
    click on 'Save' button at bottom of window



## C. Setup and usage DOMjudge for the course

The course will be given in different parts with each part different team configurations.
For each part we make a separate contest to which only teams of a specific team configuration have access. Within each course part we can add several problems, where each problem has its own sumbmission deadline.

Below we explain first how to setup a part of the course. Other course parts can repeat the procedure. Then we explain how to add a problem to a contest of a course part, how students can practice and submit their final result. Finally we describe how the teacher then can fetch the results of the problem. The same procedure can be used for other problems.

In the following document I explain in more detail the idea's behind my configuration of DOMjudge for use in a course:

* [Explanation course setup](Explanation_course_setup.md)


### 1. Setup part of a course for a specific teams configuration

Below we setup a course part for a teams configuration labeled with the `Team Category` named `teams-config-1`. The same procedure can be repeated for other teams configurations.  


#### Create teams 

We use scripts to create teams and users. Details about the design choices how we make these teams and users you can read here:

* [Explanation of users and teams setup](Explanation_of_users_and_teams_setup.md)


Step by step instructions to create users and teams in DOMjudge:

   1. first add different 'Team Categories' which we can use for each course part


	      click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
              click on 'Team Categories'
	      click on 'Add new category' button
	        in 'Name' box enter text "teams-config-1"
	        and set 'Visible' radio button to "No"
	        note: self-registration is by default 'No'
	      click on 'Save' button at bottom of window 

	
   2. create a `teams.csv` file with two columns:

       1. the first column is  the name of the team. We use as team name a string containing a comma separated set of student names.
       2. the second column we set with a string containing a comma separated set of emails, one per student.

   
      Example:
   
          $ cat teams.csv
          "Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
          "Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
          ....
          
          
      You can offcourse also make teams with only one student with only a single name and email in the columns.           
   
      Often you can export a list of students from some source, eg. brightspace,  and automatically generate the `teams.csv` file with a script. If you then want to combine students in teams you can edit this file manually to group them then in teams.
             
    
   3. generate loginname/password and team category for DOMjudge in `teams_domjudge.csv`
 
          TEAM_CONFIGURATION_NUMBER=1 
          generate_domjudge_data.py teams.csv $TEAM_CONFIGURATION_NUMBER > "teams_domjudge.csv"
   
      The  `TEAM_CONFIGURATION_NUMBER` is used to generate a team category which is unique. For the first part of the course we would use 1, for the second part of the course 2 etc...

   4. use `teams_domjudge.csv` to generate the `teams.yaml` and `users.yaml` import files by running the script:

          create_domjudge_import_files.py  "teams_domjudge.csv" 

   
   5. Import `teams.yaml` and `users.yaml` import files into DOMjudge.

      We can do the import manually via the "Import / export" page linked on the "DOMjudge Jury interface". But we can also do it using the [REST API](https://www.domjudge.org/docs/manual/main/develop.html#api) using the [httpie tool](https://httpie.io):

          DOMJUDGE_SERVER="http://localhost:12345"    
          PASSWORD="secret" 
          # admin password can be read from ./data/passwords/admin.pw 
          # see https://github.com/harcokuppens/DOMjudgeDockerCompose/
    
          # import teams (hack: using json@teams.yaml to allow still import yaml)
          https -a "admin:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/teams" json@teams.yaml

          # import accounts
          https -a "admin:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@accounts.yaml




#### Mailing credentials to students

When we run the script:

    create_domjudge_credentials_mails.py  "teams_domjudge.csv". "mail/" 

we generate in the `mail/` subfolder the mails to be send to the students.
We do not directly send the emails, because we want to be able to inspect the emails before sending.

With the following script we  can send out the emails:

    send_emails.bash  "thesender@gmail.com"  "mail/"

If somehow this method does not work for you, you can use one of the alternative methods described in the page 
[Sending batch of personalized emails](Sending_batch_of_personalized_emails.md).

#### Create contest
          
Then create different contest for each course part.
We take as coursename 'ads2324', but you can use any coursename here.
        
    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Contests'       
    click on 'Add new contest' button
       in 'Shortname' and 'Name' boxes enter 'ads2324-part-1'
       select value in "Activate time" and 
       paste it into "Start time" and "End time" fields.
       In the "End time" field increase the date by 1 year.
       "Record balloons" set to "No"
       "Enable public scoreboard" set to "No"
       "Open contest to all teams" set to "No"
       select in "Team categories" the value 'teams-config-1'
       
    

### 2. Creating, adding, submitting, and downloading results of a problem 


Below we explain how to add a problem to a contest of a course part, how students can practice and submit their final result. Finally we describe how the teacher then can fetch the results of the problem. The same procedure can be used for other problems.

#### Create problem for the course
 
We use [BACPtools](https://github.com/RagnarGrootKoerkamp/BAPCtools) to develop a problem to be used in DOMjudge.


In DOMJudge we will import this problem twice:

  1. One version **for grading** containing both the public and secret samples. The judging is set to be done **none-lazy**, meaning all samples are evaluated.  This folder is the leading problem folder. 
  2. One version **for practicing** which is a copy where we removed all secret samples in the 'data/secret/' folder.  The judging is set to be done **lazy**.

So when developing the problem folder we will first focus on the **grading** leading problem folder. Later we will strip this to create the **practicing** problem.

* use BACPtools to create a problem and develop it : 
       
        bt new_problem  grading_mynewproblem
        
  which after several questions generates a new problem in
  the new subfolder `grading_mynewproblem/` with the problem name   `grading_mynewproblem` set in   `grading_mynewproblem/problem.yaml`.
  
  **IMPORTANT:** 
   * prefix your problem name with `grading_` because this folder will be the leading problem folder which we will use for grading. For practicing we make a copy from which we strip the secret samples.
   * BACPtools generates a file `.timelimit` containing an integer specifying the limelimit of the problem in seconds (default:1). The official problem package format does not support this file, but also does not specify how to specify it. [DOMjudge supports specifying a timelimit](https://www.domjudge.org/docs/manual/main/problem-format.html) in its own specific `domjudge-problem.ini` file. BACPtools does also support this file, but has deprecated it. DOMjudge does also support the `.timelimit` file when importing a problem zipfile. Because we use BACPtools to create a problem we will use the `.timelimit` file! We won't use `domjudge-problem.ini` file. 
      
* edit your problem: 

    * add `problem_statement/problem.en.tex` to describe your problem in English. When exporting the problem as a zipfile it will compile this `tex` file to the file `problem.pdf` at the root of the zipfile.
    * in `submissions/accepted/` folder put good solutions to problem 
    * add a generator,<br>
      A generator generates  test samples for a problem.  In the generator config some inputs are choosen, and using a solution the answers are calculated. The generator then delivers these inputs with there answers in  .in files and .ans files in the `data/` folder.
      See documentation: https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/generators.md
    * optionally add an input and answer validator, <br>
      These validators validate respectively the `.in` or `.ans` file for every test case, 
      typically for syntax, format, and range. They are a safety check on you sample files format and syntax. 
      See documentation: https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/validation.md 
      
      **Note:** The official problem package format does not specify an answer validator. The answer validator is a  BACPtools specific extension. 

    * use the default output validator,<br> 
      The output validator checks whether for a given input the output of a solution is correct. <br>
      In most cases we do not need an specific output validator and can we use the default output validator.
      The [default output validator](https://icpc.io/problem-package-format/spec/2023-07-draft.html#default-output-validator-specification) is essentially a beefed-up diff on a file with the expect output(`.ans` file), and a file containing the submission's output. In its default mode, it tokenizes the files to compare and compares them token by token. 
      


* generate samples for your problem with:

        bt generate 

  The generator generates input and answer files, validates them
  with an input and answer validator. 

* test your problem with 

        bt run  --no-generate  --verbose  --overview accepted

  We apply each solution in the  `submissions/accepted/` folder on the generated input files, and validates its output is the same as the generated answer, finally displaying an overview in single line  as shown in the DOMjudge website for a submission.

  If your accepted submission does not pass within the timelimit then increase its value in the `.timelimit` file. Otherwise if it passes to easily you can decrease it. In this way you can tune your problem.

  Let us explain the command in more detail:
  
  * The `accepted` argument makes `bt` only validate solutions in the  `submissions/accepted/` folder.
  * The `--no-generate` option makes `bt` to skip the `generate` step, which is not needed because we already generated our sample data in the previous step. 
  * The `--verbose` option makes `bt` to display a line per test case, and causes it to not stop if a test case fails, but evaluates them all: greedy or none-lazy evaluation. 
  * The `--overview` option make `bt` to display a per solution a single line result where the line contains per testcase a letter displaying its evaluation result. This is the same line as shown in the DOMjudge website.

  **Note:** in above command we only test the `accepted` solutions. We can also test how the not accepted solutions in the other subfolders of the `submissions/` fail as we would expect.

* if everything is fine can create a zipfile of the problem with        
        
        bt zip -f

  The `-f` option makes `bt zip` skip validation of input and answers. This is already done in the previous steps.  

* then you can import this problem zipfile into DOMjudge. For details how we exaclty do this see next section.

 
* on import DOMjudge creates a problem:
     - with "name" from `problem.yaml`
     - with "problem text"  set to `problem.pdf`
     - and `.in `and `.ans` files from the data folder (both `answers/` and `secrets/`)

  all other data in the zipfile is not imported into the DOMjudge problem.  The submissions/ folder in the zipfile is handled specially. It will not be imported into the problem, but instead submitted to the newly created problem to be judged. This can be convenient to directly evaluate that everything also works the same on the DOMjudge server as on your local machine. **IMPORTANT:** the judging will only happen if the user account doing the import has **admin role** and **belongs to a team**.      
  
 
The problem description in `problem.pdf` contains the submission/deadline date before which a final solution of the problem must be submitted.
  
Always keep the local BACPtools problem folder, because using that we can easily change and test the problem before exporting to DOMjudge. This folder is the leading folder in this process!
                           

#### Add problem to the course

As explained before we need to import the problem twice:

  1. One version **for grading** containing both the public and secret samples. The judging is set to be done **none-lazy**, meaning all samples are evaluated.  This folder is the leading problem folder. 
  2. One version **for practicing** which is a copy where we removed all secret samples in the 'data/secret/' folder.  The judging is set to be done **lazy**.

We create both versions from the leading local BACPtools `grading_$PROBLEM/` by doing the following:

    PROBLEM="mynewproblem"
    cp -a  "grading_$PROBLEM/" "$PROBLEM/"
    rm -r "$PROBLEM_NAME/data/secret"
    sed -i -e "s/grading_$PROBLEM/$PROBLEM/" "grading_$PROBLEM/problem.yaml"
    cd "$PROBLEM/"; bt zip -f; cd ..
    cd "grading_$PROBLEM/"; bt zip -f; cd ..

The `grading_$PROBLEM/` folder is our leading BACPtools problem folder contain both public as secret samples.   We make a copy of the `grading_$PROBLEM/` folder calling it just `$PROBLEM` from which we strip the secret sample. We create then  2 zipfiles `$PROBLEM.zip` and  `grading_$PROBLEM.zip` where the first is used for practicing and the latter for grading.

We then import both zipfiles into DOMjudge. We then configure the practicing problem to be evaluated lazy to reduce the load on the DOMjudge server. We configure the grading problem as non-lazy so that
all our secret grading samples are evaluated.
  
We can easily import a problem directly into a contest:


    - click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    - click on 'Problems'  
    - on bottom of the page click on the 'Import problem' button
    - now we are redirect to the "Import and export" page 
    - in this page on the "Problems; Import archive" 
        a) select the contest where to import the problem
        b) in the "Problem archive" field select the problem zip 
           to import

**IMPORTANT**: it is adviced to import the problem as an user with **admin role** which is also **belonging to a team**, because then on import then directly the solutions in the problem's zipfile are directly judge. We can then directly verify that our solutions are also correct in DOMjudge and are done within the timelimit. It is wise to test also in DOMjudge, but sometimes a little detail can make your solution pass locally, but not in DOMjudge. <br> You can also upload the problem with `admin` user not belonging to a team, and later upload  a zipfile containing only the problem's solutions as another user with **admin role**  and which is **belonging to a team** where the zipfile still needs to adhere to the problem format.  Reference: ["Testing jury solutions" section in the DOMjudge documentation](https://www.domjudge.org/snapshot/manual/config-basic.html#testing-jury-solutions).


We can set the evaluation laziness of a problem with:
  
    - click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    - click on 'Contests'       
    - on the bottom of the page click in the table on the contest of interest
      causing a details page of the contest is shown
    - on bottom of the page click on the 'Edit' button
    - on the edit page of the contest is a table with all problems in the contest
    - for the problem of interest edit the in the last column "Lazy eval" to "Yes"
      for the "practicing" version of the problem, and to "No" for the "grading" 
      version of the problem.    


#### Students can submit code to the problem    

Now students can submit their code to the **practicing problem**. The practicing problem is **judged lazy**, meaning that if the submission fails on a sample, then all remaining samples are not executed anymore.  The idea is that the team first has to solve that sample first, before executing the remaining samples. The lazy evaluation also makes the load less on the juding server.

A team can also download the samples of the practicing problem, and evaluate all the samples locally on their solution without using DOMjudge.

If the team has a final solution they can submit it to the **grading problem** for grading. The grading problem is **judged none-lazy**, because all secret samples must 
be evaluated for the grading. 

A team should only submit once per grading problem, however if something went wrong, they are allowed to submit again. For grading the latest submission is used.
 
### When the problem's deadline is reached, the teacher downloads its final submissions  

In [our approach of applying a deadline to a problem](How_to_handle_ending_time_of_a_problem.md) we do not use any deadline timers in DOMjudge to specify the deadline a problem's final submission, but instead we just inform the students of the deadline, and when its passed we disable submitting to the 'grading' problem, and fetch the results from it. 
 
The problem's deadline is communicated to the students during the course and in the problem description. If the deadline of the problem is passed, the teacher can fetch his results this way:

   1. **Disable submission** to the grading version of the problem: 

         - click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
         - click on 'Contests'       
         - on the bottom of the page click in the table on the contest of interest
           causing a details page of the contest is shown
         - on bottom of the page click on the 'Edit' button
         - on the bottom of the page in the problems table set 'Allow submit'  
           field to 'No' for the "grading" problem of interest.
         - on bottom of the page click on the 'Save' button          
       
      This makes the grading problem invisible for teams, and they cannot 
      submit to it anymore!
      However the teams can still see the none-grading practicing problem.
      
   2. Use download script to **download results** from grading script. 
 

          export CODEDIR="./scripts/"
          # create a new directory to collect all data there
          mkdir grading_data_processed_using_domjudge
          cd grading_data_processed_using_domjudge  
          user=userwithadminrights; password='mypassword'; 
          python3  $CODEDIR/fetch  --auth "${user}:${password}" --url https://domjudge.science.ru.nl/ --contest grading --problem boxesgrading
  

   3. **Process results** of problem and format to excel

          

       Reprocess downloaded data where we take per team only 
       the latest judgement of the latest submission and
       output this list as a csv file:
     
           python3 $CODEDIR/reprocess.py
  
       We output two `csv` files one with more and with less details.
       
       Convert these `csv` files to nice excel files containing a table:
  
           python3 $CODEDIR/csv2xslt.py gradings.csv
           python3 $CODEDIR/csv2xslt.py gradings.details.csv  
