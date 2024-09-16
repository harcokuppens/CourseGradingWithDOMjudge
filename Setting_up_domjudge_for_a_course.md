# Setting up DOMjudge for a course

**Table of Contents**

<!---
update toc by running command:
   markdown_insert_github_toc Setting_up_domjudge_for_a_course.md
-->

<!--ts-->

- [Setting up DOMjudge for a course](#setting-up-domjudge-for-a-course)
  - [A. Create a fresh and up to date DOMjudge installation](#a-create-a-fresh-and-up-to-date-domjudge-installation)
    - [New installation](#new-installation)
    - [Reset and update existing installation](#reset-and-update-existing-installation)
  - [B. Basic configuration](#b-basic-configuration)
    - [1. Make demo contest private, and delete demo user](#1-make-demo-contest-private-and-delete-demo-user)
    - [2. Make DOMjudge use external id's for configuration data](#2-make-domjudge-use-external-ids-for-configuration-data)
    - [3. Setting language timefactors](#3-setting-language-timefactors)
    - [4. Give teams more information about their submissions](#4-give-teams-more-information-about-their-submissions)
  - [C. Install helper scripts of CourseGradingWithDOMjudge repo](#c-install-helper-scripts-of-coursegradingwithdomjudge-repo)
  - [D. Initial setup DOMjudge for the course](#d-initial-setup-domjudge-for-the-course)
    - [Setup Test contest](#setup-test-contest)
    - [Setup Administrators](#setup-administrators)
      - [Step 1: generate the admin-accounts.yaml and emails](#step-1-generate-the-admin-accountsyaml-and-emails)
      - [Step 2: Import admin-accounts.yaml file into DOMjudge.](#step-2-import-admin-accountsyaml-file-into-domjudge)
      - [Step 3: Inform the new administrators by mail.](#step-3-inform-the-new-administrators-by-mail)
  - [E. Per course part setup and usage DOMjudge](#e-per-course-part-setup-and-usage-domjudge)
    - [1. Setup part of a course for a specific teams configuration](#1-setup-part-of-a-course-for-a-specific-teams-configuration)
      - [Create user and teams in DOMjudge](#create-user-and-teams-in-domjudge)
      - [Mailing credentials to students](#mailing-credentials-to-students)
      - [Create contest](#create-contest)
    - [2. Creating, testing and importing a problem](#2-creating-testing-and-importing-a-problem)
      - [Create problem for the course](#create-problem-for-the-course)
        - [Use BACPtools to create a problem](#use-bacptools-to-create-a-problem)
        - [Edit your problem](#edit-your-problem)
        - [Generate samples for your problem](#generate-samples-for-your-problem)
        - [Test your problem](#test-your-problem)
        - [Create a zip file](#create-a-zip-file)
      - [Add problem to the course](#add-problem-to-the-course)
        - [Create practicing problem from grading problem](#create-practicing-problem-from-grading-problem)
        - [Import problem](#import-problem)
        - [Set laziness of problem](#set-laziness-of-problem)
        - [Test problem in DOMjudge](#test-problem-in-domjudge)
    - [3. Students can submit code to the problem](#3-students-can-submit-code-to-the-problem)
    - [4. At problem's deadline fetch and process student results.](#4-at-problems-deadline-fetch-and-process-student-results)
    - [5. Ending contest and course](#5-ending-contest-and-course)

<!--te-->

## A. Create a fresh and up to date DOMjudge installation

You do not want to load of one course to effect another course. So we advice to use per course a
separate DOMjudge installation.

We have two options, either do a new installation from scratch, or reset and update an existing
installation of DOMjudge.

### New installation

A new installation can be easily be done using by unning DOMjudge in docker by following this
quickstart. https://github.com/harcokuppens/DOMjudgeDockerCompose/blob/main/README.md#quick-start

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

The default installation of DOMjudge needs some tweaking before we can use it. For example we
disable the demo user for security reasons.

### 1. Make demo contest private, and delete demo user

First login as `admin` user with the password from the file `./data/passwords/admin.pw` in the
installation folder.

By default a public demo contest is added to DOMjudge. This demo project is nice for students to
first practice with DOMjudge. However we want the DOMjudge website empty for external people, and
have the demo project only available for our students. We do this by disabling public showing of
demo contest:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Contests'
    click on 'demo' contest at bottom of window
    click on 'Edit' button at bottom of window
    then change config of 'Demo' contest:
        'Enable public scoreboard' set from 'Yes' to 'No'
        copy 'Start Time' to 'Scoreboard freeze time' and 'End time' fields
        then increase the year with one year in the 'Scoreboard freeze time' and 'End time' fields
        which makes the demo contest open for submissions for one year
        "Record balloons" set to "No"
        "Enable public scoreboard" set to "No"
    click on 'Save' button at bottom of window

For security reasons we also remove the `demo` user and `exteam` by:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Users'
    on the row for the 'demo' user click at the end of line on the 'trashcan' icon
    and confirm by pressing the 'Delete' button in the popup window

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Teams'
    on the row for the 'exteam' team click at the end of line on the 'trashcan' icon
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

The python and java languages are higher level languages then C and C++ and thereby having more
overhead in executing their code, causing them to have higher execution times for similar code in
low level languages. To compare solutions in different languages fairly we correct for higher
execution times in higher level languages with a timefactor. We use the following timefactors:

| language | timefactor |
| -------- | ---------- |
| C        | 1          |
| C++      | 1          |
| java     | 3          |
| python   | 4          |

This means that for a problem with a timelimit of 1 second, a C or C++ program must solve the
problem within 1 second, but a java program in 3 seconds, and a python program in 4 seconds.

To set a timefactor for a language in DOMjudge we do:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Languages'
    click on the edit icon behind the language you want to edit
    in the edit form for the language set the timefactor field to the desired value
    click on 'Save' button at bottom of window

### 4. Give teams more information about their submissions

DOMjudge by default does not allow teams to see the output of the output validator of a submission
to the test samples. In below instructions we enable showing them, so that students can see what is
wrong with their submission. It can be either that their submission does not compile or has runtime
errors. But it can also be possible that the submission just gives back the wrong result value, in
which case the expect sample output and the submissions output is shown next to each other. The
sample outputs are available to the students, so no reason to hide it, and its very convenient to
show this information so that students can immediately see what they did wrong. In below
instructions we also allow to download code from submissions. Can be handy to sometimes go back.

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Configuration settings'
    on the top of the window click on the 'Display' tab
    set "Show sample output"  to  "Yes"
    set "Allow team submission download"  to Yes
    click on 'Save' button at bottom of window

## C. Install helper scripts of CourseGradingWithDOMjudge repo

To set up DOMjudge for a course we implemented some helper scripts in the
[CourseGradingWithDOMjudge repo](https://github.com/harcokuppens/CourseGradingWithDOMjudge) to help
you with this task.

To use the scripts of the CourseGradingWithDOMjudge repo we need to clone the repo. We take
`REPO_PATH` as the location where the repository gets cloned:

    cd "$REPO_PATH"
    git clone https://github.com/harcokuppens/CourseGradingWithDOMjudge

Then add its scripts folder to your PATH by adding to your `.bashrc`:

    # add scripts to PATH
    export PATH="$REPO_PATH/bin/:$PATH"

## D. Initial setup DOMjudge for the course

Initially when DOMjudge is installed we get an 'admin' account. However we rather give the people
giving the course each their own administrator account, so that we later always can change the set
of administrators.

The administrators should also be able to test a new contest problem on the DOMjudge server before
using it in the course. The best practice is to have a separate Test contest to which problems can
be added and be tested. We only give administrators access the Test contest.

### Setup Test contest

To create the Test contest do:

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Contests'
    click on 'Add new contest' button
       in 'External ID', 'Shortname' and 'Name' boxes enter 'Test'
       select value in "Activate time" and
       paste it into "Start time" and "End time" fields.
       In the "End time" field increase the date by 1 year.
       "Record balloons" set to "No"
       "Enable public scoreboard" set to "No"
       "Open contest to all teams" set to "No"
       select in "Team categories" the value 'Jury'

By only allowing the Jury team category we give access to only administrators.

### Setup Administrators

When in DOMjudge importing with an "accounts.yaml" with user accounts of type 'admin' DOMjudge
behaves different then when importing normal team users (type 'team'). It then not only creates the
user with the administrator role, but also gives it the 'team' role. DOMjudge also automatically
creates a team only for this user, where the team is in the 'Jury' team category. Having both roles
allows the adminstrator user switch role between 'Jury' and 'Team' interface in DOMjudge, allowing
is also submit solutions as team member to the Test contest we made in the previous section.

Because the teams are automatically created in DOMjudge we only need to import an `accounts.yaml`
file into DOMjudge describing the administrators.

To make it convenient to create administrators I defined a simple CSV file format in which you can
only need to define the names and emails of the administrators. For example:

     $ cat admins.csv
     name, email
     Henk Groot, henk.groot@gmail.com
     Piet Klein, piet.klein@gmail.com

then using the `create-domjudge-admins` script we can easily generate the `admin-accounts.yaml`
file. By importing this file in DOMjudge the admin accounts are created. The
`create-domjudge-admins` script also generates emails to inform the new administrators of their
credentials in a folder.

Now let setup the new admins in 3 steps.

#### Step 1: generate the `admin-accounts.yaml` and emails

Create an `admins.csv` file, and then run the following script:

     create-domjudge-admins "admins.csv" "mail/" "thesender@gmail.com" "The Sender Name"

Which will create an `admin-accounts.yaml` import file for DOMjudge, and the mails in the `mail/`
folder, where each mail is from the sender "The Sender Name" with email address
"thesender@gmail.com".

#### Step 2: Import `admin-accounts.yaml` file into DOMjudge.

We can do the import manually via the "Import / export" page linked on the "DOMjudge Jury
interface". But we can also do it using the REST API using the httpie tool:

    DOMJUDGE_SERVER="http://localhost:12345"
    ADMINUSER="admin"
    PASSWORD="secret"
    # admin password can be read from ./data/passwords/admin.pw
    # see https://github.com/harcokuppens/DOMjudgeDockerCompose/

    # import accounts
    https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@admin-accounts.yaml

#### Step 3: Inform the new administrators by mail.

Finall with the following script, we use the Linux sendmail command to send out the emails:

    send-emails "thesender@gmail.com" "mail/"

Make sure you use a Linux machine for which sendmail is configured. If somehow this method does not
work for you, you can use one of the alternative methods described in the page
[Sending batch of personalized emails](Sending_batch_of_personalized_emails.md).

All steps from now on in this page, can be done by one of the new administrators.

## E. Per course part setup and usage DOMjudge

The course will be given in different parts, where each part arranges the students differently over
the teams. Each part has a different teams configuration.

We implement each course part with DOMjudge using a separate contest only accessible by a specific
teams configuration. Each team in that teams configuration is given the same `Team Category` in
DOMjudge, and we give only access to the contest to teams in that `Team Category`. We also set the
`visible` property of that `Team Category` to false, causing the different teams to become invisible
to each other in the contest. Finally, each course part can have several problems to solve, which we
implement by adding several problems to that course part's contest. Although several problems are in
the same contest, each problem has still its own submission deadline.

Below we explain first how to setup a part of the course. Other course parts can repeat the
procedure. Then we explain how to add a problem to a contest of a course part, how students can
practice and submit their final result. Finally we describe how the teacher then can fetch the
results of the problem. The same procedure can be used for other problems.

In the following document I explain in more detail the idea's behind my configuration of DOMjudge
for use in a course:

- [Explanation course setup](Explanation_course_setup.md)

### 1. Setup part of a course for a specific teams configuration

Below we setup a course part for a teams configuration labeled with the `Team Category` named
`part1-teams-config`. The same procedure can be repeated for other teams configurations.

#### Create user and teams in DOMjudge

We use scripts to create teams and users. Details about the design choices how we make these teams
and users you can read here:

- [Explanation of users and teams setup](Explanation_of_users_and_teams_setup.md)

Step by step instructions to create users and teams in DOMjudge:

1.  First add the `Team Category` named `part1-teams-config` which we can use for the first course
    part

        click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
        click on 'Team Categories'
        click on 'Add new category' button
        in 'Name' box enter text "part1-teams-config"
        and set 'Visible' radio button to "No"
        note: self-registration is by default 'No'
        click on 'Save' button at bottom of window

    All teams in the first part of the course get the same `Category` value `part1-teams-config`.
    This category is used in two ways:

    1. For this specific category we set the `visible` field to "No" causing all teams unable to see
       each others submissions. See above.

    2. We allow that course part's contest only be accessible by teams with that category. See
       [Create contest](#create-contest).

    For details see [Explanation of users and teams setup](Explanation_of_users_and_teams_setup.md).

2.  Create data folder per course part

        COURSEPART_NUMBER=1
        mkdir part${COURSEPART_NUMBER}
        cd part${COURSEPART_NUMBER}

    It is a good practice to store the files to setup users and teams in a separate folder per
    coursepart eg. `part${COURSEPART_NUMBER}`. Then we have all user and team data for all
    courseparts nicely stored on disk as backup.

3.  Create a `teams.csv` file, where each line represents a team, with the two columns:

    1.  the first column is the name of the team. We use as team name a string containing a comma
        separated set of student names.
    2.  the second column we set with a string containing a comma separated set of emails, one per
        student.

    You can offcourse also make teams with only one student with only a single name and email in the
    columns.

    Example:

        $ cat teams.csv
        "names", "emails"
        "Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
        "Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
        ....

    The first line in the `teams.csv` file is the header row which declares the names of the
    different columns.

    Often you can export groups of students from some source, eg.
    [Brightspace](Import_teams_from_brightspace_groups_into_DOMjudge.md), and automatically generate
    the `teams.csv` file with a script. Sometimes we want the same numbering scheme of groups as in
    an external system such as Brightspace, so that teamX in DOMjudge matches groupX in Brightspace.
    To make this possible we allow also a `teamname` column in our `teams.csv` file:

    Example:

        $ cat teams.csv
        "teamname","names", "emails"
        "group1","Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
        "group2","Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
        ....

    The `teams.csv` file contains the minimal information required to create users and teams in
    DOMjudge. We use scripts to generate from this `teams.csv` file a `users.yaml` and `teams.yaml`
    to import users and teams into DOMjudge, and to generate emails which sends users their
    credentials. The scripts only need the `teams.csv` and the course part's number as input. The
    course part number is required because, as explained in the document
    ["Explanation of users and teams setup"](Explanation_of_users_and_teams_setup.md), for each
    coursepart we generate a different user account for each user.

4.  From the `teams.csv` file create users and teams in DOMjudge.

    We do this in 2 steps:

    1.  Then from `teams.csv` and the `COURSEPART_NUMBER` we generate the `teams.yaml` and
        `users.yaml` import files by running the script:

            COURSEPART_NUMBER=1
            create-domjudge-import-files  "teams.csv" $COURSEPART_NUMBER

        The `COURSEPART_NUMBER` is required to generate unique user and team IDs for that course's
        part.

        The script first creates a subfolder `part${COURSEPART_NUMBER}` in which it generates
        `teams.yaml` and `users.yaml` files.

        In the generate `teams.yaml` all teams are assigned to the Team Category named
        `part${COURSEPART_NUMBER}-teams-config`. That is `part1-teams-config` for the first course
        part.

        In case the `teams.csv` already contains a `teamname` column the script only adds the prefix
        `part${COURSEPART_NUMBER}` to the existing `teamname`. For example for a given teamname
        "group3" in course part 2 we would get a team name "part2-group3" in DOMjudge.

        The generated `teams.yaml` and `users.yaml` files are also used later to generate emails
        which sends users their credentials and team info.

    2.  Import `teams.yaml` and `accounts.yaml` import files into DOMjudge.

        We can do the import manually via the "Import / export" page linked on the "DOMjudge Jury
        interface". But we can also do it using the
        [REST API](https://www.domjudge.org/docs/manual/main/develop.html#api) using the
        [httpie tool](https://httpie.io):

            DOMJUDGE_SERVER="http://localhost:12345"
            ADMINUSER="admin"
            PASSWORD="secret"
            # admin password can be read from ./data/passwords/admin.pw
            # see https://github.com/harcokuppens/DOMjudgeDockerCompose/

            # import teams (hack: using json@teams.yaml still allows import of yaml)
            https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/teams" json@teams.yaml

            # import accounts
            https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@accounts.yaml

#### Mailing credentials to students

Here we explain how to mail the credentials to the students. This step can off course also be done
later when actual problems are added to the course. But because in the previous sections we
explained how to create the teams in DOMjudge this is the most logical place to explain the mailing
procedure.

We run the script with the `teams.yaml` and `accounts.yaml` DOMjudge import files which we generated
in the previous section:

    create-domjudge-credentials-mails  "accounts.yaml" "teams.yaml" "mail/" "thesender@gmail.com" "The Sender Name"

This will generate in the `mail/` subfolder the mails which we will send to the students. We do not
directly send the emails, because we want to be able to inspect the emails before sending.

With the following script, we use the Linux `sendmail` command to send out the emails:

    send-emails "thesender@gmail.com" "mail/"

Make sure you use a Linux machine for which `sendmail` is configured. If somehow this method does
not work for you, you can use one of the alternative methods described in the page
[Sending batch of personalized emails](Sending_batch_of_personalized_emails.md).

#### Create contest

Then create a different contest for each course part. We take as coursename 'mycourse', but you can
use any coursename here.

    click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    click on 'Contests'
    click on 'Add new contest' button
       in 'External ID', 'Shortname' and 'Name' boxes enter 'mycourse-part-1'
       select value in "Activate time" and
       paste it into "Start time" and "End time" fields.
       In the "End time" field increase the date by 1 year.
       "Record balloons" set to "No"
       "Enable public scoreboard" set to "No"
       "Open contest to all teams" set to "No"
       select in "Team categories" the value 'part1-teams-config'

### 2. Creating, testing and importing a problem

Below we explain how to add a problem to a contest of a course part, how students can practice and
submit their final result. Finally we describe how the teacher then can fetch the results of the
problem. The same procedure can be used for other problems.

#### Create problem for the course

We use [BACPtools](https://github.com/RagnarGrootKoerkamp/BAPCtools) to develop a problem to be used
in DOMjudge.

In DOMJudge we will import this problem twice:

1. One version **for grading** containing both the public and secret samples. The judging is set to
   be done **none-lazy**, meaning all samples are evaluated. This folder is the leading problem
   folder.
2. One version **for practicing** which is a copy where we removed all secret samples in the
   'data/secret/' folder. The judging is set to be done **lazy**.

So when developing the problem folder we will first focus on the **grading** leading problem folder.
Later we will strip this to create the **practicing** problem.

##### Use BACPtools to create a problem

Use BACPtools to create a problem:

      bt new_problem  grading_mynewproblem

which after several questions generates a new problem in the new subfolder `grading_mynewproblem/`
with the problem name `grading_mynewproblem` set in `grading_mynewproblem/problem.yaml`.

It is **important** to prefix your problem name with `grading_` because this folder will be the
leading problem folder which we will use for grading. For practicing we make a copy from which we
strip the secret samples.

The new generated problem folder now looks like:

    $ tree -aF --filesfirst grading_mynewproblem
    grading_mynewproblem/
    ├── problem.yaml
    ├── .timelimit
    ├── answer_validators/
    │   └── answer_validator/
    │       ├── answer_validator.cpp
    │       └── validation.h
    ├── generators/
    │   ├── example.py
    │   └── generators.yaml
    ├── input_validators/
    │   └── input_validator/
    │       ├── input_validator.cpp
    │       └── validation.h
    ├── problem_statement/
    │   ├── problem.en.tex
    │   └── solution.en.tex
    └── submissions/
        ├── accepted/
        │   └── .gitkeep
        ├── run_time_error/
        │   └── .gitkeep
        ├── time_limit_exceeded/
        │   └── .gitkeep
        └── wrong_answer/
            └── .gitkeep

where the `problem.yaml` file contains the metadata about the project, and the `.gitkeep` files are
only added to allow you to push an empty folder to git.  
BACPtools generates the problem as described by
[the official icpc standard for the problem format](https://icpc.io/problem-package-format/spec/legacy-icpc.html).
BAPCtools extends the standard by adding a `.timelimit` file and supporting also an answer
validator. By default it doesn't add an output validator because the default diff-based output
validator suffices for most cases. See 'Edit your problem' below for more details.

BACPtools generates a file `.timelimit` containing an integer specifying the limelimit of the
problem in seconds (default:1). The official problem package format does not support this file, but
also does not specify how to specify a timelimit.
[DOMjudge supports specifying a timelimit](https://www.domjudge.org/docs/manual/main/problem-format.html)
in its own specific `domjudge-problem.ini` file. BACPtools does also support this file, but has
deprecated it. DOMjudge does also support the `.timelimit` file when importing a problem zipfile.
Because we use BACPtools to create a problem we will use the `.timelimit` file! We won't use a
`domjudge-problem.ini` file, only a `problem.yaml` file.

##### Edit your problem

When creating a new problem it is always nice to have a good example to look at. I recommend to look
at the example at the BAPCtools
[different problem example](https://github.com/RagnarGrootKoerkamp/BAPCtools/tree/master/test/problems/different/).
The different problem provides the input and output samples statically. For an example where a
generator is used look at the BAPCtools
[identity problem example](https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/test/problems/identity/).

Steps to follow to define your new problem:

- add `problem_statement/problem.en.tex` to describe your problem in English. When exporting the
  problem as a zipfile it will compile this `tex` file to the file `problem.pdf` at the root of the
  zipfile.
- in `submissions/accepted/` folder put good solutions to problem
- add a generator,<br> A generator generates test samples for a problem. In the generator config
  some inputs are choosen, and using a solution the answers are calculated. The generator then
  delivers these inputs with there answers in .in files and .ans files in the `data/` folder. See
  documentation: https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/generators.md . An
  example generator you can find at
  https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/test/problems/identity/generators/generators.yaml
- optionally add an input and answer validator, <br> These validators validate respectively the
  `.in` or `.ans` file for every test case, typically for syntax, format, and range. They are a
  safety check on you sample files format and syntax. See documentation:
  https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/validation.md . A good example
  problem with an input and answer valicator you can find at
  https://github.com/RagnarGrootKoerkamp/BAPCtools/tree/master/test/problems/different/

  **Note:** The official problem package format does not specify an answer validator. The answer
  validator is a BACPtools specific extension.

- use the default output validator,<br> The output validator checks whether for a given input the
  output of a solution is correct. <br> In most cases we do not need an specific output validator
  and can we use the default output validator. The
  [default output validator](https://icpc.io/problem-package-format/spec/2023-07-draft.html#default-output-validator-specification)
  is essentially a beefed-up diff on a file with the expect output(`.ans` file), and a file
  containing the submission's output. In its default mode, it tokenizes the files to compare and
  compares them token by token.

##### Generate samples for your problem

Generate samples for your problem with:

    bt generate

The generator generates input and answer files, validates them with an input and answer validator.

##### Test your problem

Test your problem with:

    bt run  --no-generate  --verbose  --overview accepted

We apply each solution in the `submissions/accepted/` folder on the generated input files, and
validates its output is the same as the generated answer, finally displaying an overview in single
line as shown in the DOMjudge website for a submission.

If your accepted submission does not pass within the timelimit then increase its value in the
`.timelimit` file. Otherwise if it passes to easily you can decrease it. In this way you can tune
your problem.

Let us explain the command in more detail:

- The `accepted` argument makes `bt` only validate solutions in the `submissions/accepted/` folder.
- The `--no-generate` option makes `bt` to skip the `generate` step, which is not needed because we
  already generated our sample data in the previous step.
- The `--verbose` option makes `bt` to display a line per test case, and causes it to not stop if a
  test case fails, but evaluates them all: greedy or none-lazy evaluation.
- The `--overview` option make `bt` to display a per solution a single line result where the line
  contains per testcase a letter displaying its evaluation result. Lowercase letters are used for
  the public samples, and uppercase letters for secret test cases. This is almost the same line as
  shown in the DOMjudge website. In the website icons are used instead of letters, and the public
  and private samples are separated with a bar.

**Note:** in above command we only test the `accepted` solutions. We can also test how the not
accepted solutions in the other subfolders of the `submissions/` fail as we would expect.

##### Create a zip file

If everything is fine can create a zipfile of the problem with

      bt zip -f

The `-f` option makes `bt zip` skip validation of input and answers. This is already done in the
previous steps.

Now you can import this problem zipfile into DOMjudge. For details how we exactly do this see next
section.

On import DOMjudge creates a problem:

- with "name" from `problem.yaml`
- with "problem text" set to `problem.pdf`
- and `.in `and `.ans` files from the data folder (both `answers/` and `secrets/`)

All other data in the zipfile is not imported into the DOMjudge problem.

**IMPORTANT**:

- Always keep the local BACPtools problem folder, because using that we can easily change and test
  the problem before exporting to DOMjudge. This folder is the leading folder in this process!

- Add the submission/deadline date, before which a final solution of the problem must be submitted,
  in the problem description in `problem.pdf` contains.

- The **name** of the problem is on import to DOMjudge set to value of the `name` field in
  `problem.yaml`. The basename of the zipfile is used as the problem **shortname**. See
  https://www.domjudge.org/snapshot/manual/problem-format.html). The command `bt zip -f` creates a
  zipfile with its basename set to directory name of the problem. Using the directory name and the
  basename of a zipfile is a very implicit way of configuring the shortname of the problem. I rather
  would have preferred that there was a special shortname field in the `problem.yaml` config file.
  <br> **KISS**: To prevent any confusion I advise to always set the (long)name and shortname of the
  problem the same. That is use the same name for the problem's directory, and its derivated zipfile
  [setting the problem's shortname], as for the name field in the `problem.yaml` config file
  [setting the problem's (long)name].

- It is advised to not import the problem to a contest directly. The `submissions/` folder is never
  imported to the problem. But, the `submissions/` folder in the zipfile is handled specially if
  the user account doing the import
  has **admin role** , **belongs to a team** and **imports the problem to a contest directly**. Then
  the `submissions/` folder will be submitted to the newly
  created problem to be judged. This can be convenient to directly evaluate that everything also
  works the same on the DOMjudge server as on your local machine. However as we describe below in the
  'Test problem in DOMjudge' section I prefer to do the testing separately in a separate test
  contest, because then testing submissions are then not included in the student contests, so we
  then later do not have to filter out test submissions when processing the student results.

#### Add problem to the course

##### Create practicing problem from grading problem

As explained before we need to import the problem twice:

1. One version **for grading** containing both the public and secret samples. The judging is set to
   be done **none-lazy**, meaning all samples are evaluated. This folder is the leading problem
   folder.
2. One version **for practicing** which is a copy where we removed all secret samples in the
   'data/secret/' folder. The judging is set to be done **lazy**.

We create both versions from the leading local BACPtools `grading_$PROBLEM/` by doing the following:

    PROBLEM="mynewproblem"
    cp -a  "grading_$PROBLEM/" "$PROBLEM/"
    rm -r "$PROBLEM_NAME/data/secret"
    sed -i -e "s/grading_$PROBLEM/$PROBLEM/" "grading_$PROBLEM/problem.yaml"
    cd "$PROBLEM/"; bt zip -f; cd ..
    cd "grading_$PROBLEM/"; bt zip -f; cd ..

**NOTE**: in above instructions we assume that the problem folder is set to the same name, as the
name field in `problem.yaml` file. In the previous section we explained that this is the best
policy.

The `grading_$PROBLEM/` folder is our leading BACPtools problem folder contain both public as secret
samples. We make a copy of the `grading_$PROBLEM/` folder calling it just `$PROBLEM` from which we
strip the secret sample. We create then 2 zipfiles `$PROBLEM.zip` and `grading_$PROBLEM.zip` where
the first is used for practicing and the latter for grading.

We then import both zipfiles into DOMjudge. We then configure the practicing problem to be evaluated
lazy to reduce the load on the DOMjudge server. We configure the grading problem as non-lazy so that
all our secret grading samples are evaluated.

##### Import problem

We can easily import a problem directly into a contest:

- click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
- click on 'Problems'
- on bottom of the page click on the 'Import problem' button
- now we are redirect to the "Import and export" page
- in this page on the "Problems; Import archive"

  1. select the contest where to import the problem.  
      Either select a contest's name or you can also select no contest, by selecting the value 'Do not
     add / update contest data'.
  2. in the "Problem archive" field select the problem zip to import

##### Set laziness of problem

We can set the evaluation laziness of a problem with:

- click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
- click on '**Contests**'
- on the bottom of the page click in the table on the contest of interest causing a details page of
  the contest is shown
- on bottom of the page click on the 'Edit' button
- on the edit page of the contest is a table with all problems in the contest
- for the problem of interest edit the in the last column "**Lazy eval**" to "**Yes**" for the
  "**practicing**" version of the **problem**, and to "**No**" for the "**grading**" version of the
  **problem**.

##### Test problem in DOMjudge

It is wise to test a new problem also in DOMjudge, because sometimes a little detail can make your
solution pass locally, but not in DOMjudge. It is advice to test the **grading** version of the
problem because this one has both public and secret samples and is set to run non-lazy.

**Best Practice:** import a problem without selecting a contest. You add a problem to a contest at
the bottom of the contest's edit form. Make a special test contest in which you add a problem to
test, and test it with a test user which submits the solutions. By using a special test contest we
separate the course contests used in production from the testing contest. Otherwise we would need to
filter out the submissions of the testuser from the student users when doing the grading. You can
also import the problem directly to test contest if it needs testing.

When setting up administrator accounts earlier in this page we already
[setup a **Test** contest](#setup-test-contest), which only the administrators can use. So I suggest
use this contest to test your new problem. Use the following steps:

- open the Test contest, and select Edit. Then at the bottom of the edit form it allows you to add problems
  to the contest. So, add the grading version of the problem to the Test contest.
- the administrator accounts all have both the team as admin role permissions.  When logging in as administrator
  then by default you are logged in with the admin role. In our test we want to test submissions which can
  only be done in the team role. So to switch to the team role we click at the top bar on '-> team' to open the team interface.
  When switching to the team interface we see in the top bar that    '-> team' is change to  '-> Jury'. By clicking
  on '-> Jury' we can switch back to the admin interface.
- when in the team role we select the 'Test' contest in the select box to the right of the logout button. Then we press the
  Submit button and submit a solution to the test problem.
- in the team interface we see the judging of the solution as seen by a team member. You only see the evaluation of
  the public answers, but not for the secret answers.  To also see the latter, we can switch back to the admin interface
  by pressing on  '-> Jury' in the top bar. In the admin interface we click on "submissions" in the top bar. Then a list
  of submissions is shown, and you can click on the submission of interest to see the judgement for both the public as
  secret answers.
- by manually sumbitting solutions this way we can test our problem in DOMjudge  

**REMARK**: when importing a problem **into a contest** as an user with **admin role** which is also
**belonging to a team**, then on import the solutions in the problem's zipfile are directly judge.
We can then directly verify that our solutions are also correct in DOMjudge and are done within the
timelimit. Reference:
["Testing jury solutions" section in the DOMjudge documentation](https://www.domjudge.org/snapshot/manual/config-basic.html#testing-jury-solutions).
DOMjudge claims you can also later upload a zipfile containing only the problem's solutions as
another user with **admin role** and which is **belonging to a team** where the zipfile still needs
to adhere to the problem format. However I never succeeded to get this to work! Maybe there is a bug
breaking this feature?? This feature is also underdocumented. I personally prefer separating
importing and testing, and do the testing in a separate test contest which separates testing concern
from production concern. See 'Best practice' above.

### 3. Students can submit code to the problem

When a problem is added to DOMjudge, the students can start working on it. We provide the students
with [instructions](Student_instructions.md) of how to use DOMjudge in the course.

From then on students can submit their code to the **practicing problem**. The practicing problem is
**judged lazy**, meaning that if the submission fails on a sample, then all remaining samples are
not executed anymore. The idea is that the team first has to solve that sample first, before
executing the remaining samples. The lazy evaluation also makes the load less on the juding server.

A team can also download the samples of the practicing problem, and evaluate all the samples locally
on their solution without using DOMjudge.

If the team has a final solution they can submit it to the **grading problem** for grading. The
grading problem is **judged none-lazy**, because all secret samples must be evaluated for the
grading. The student only sees the results for the public input and answers, and not for the secret
input and answers. However the student does see the overall result of all public and secret input
and answers together. This means if a student has all the public input/answer sets good, but
he has one of the secret ones wrong, is overall result is wrong. He cannot see which secret
input/output set he has wrong. Maybe he can ask the teacher for an hint.

A team should only submit once per grading problem, however if something went wrong, they are
allowed to submit again. For grading the latest submission is used.

### 4. At problem's deadline fetch and process student results.

When problem's deadline reached the teacher fetches and processes the judgements of the students
final grading submissions to easily grade the students.

In [our approach of applying a deadline to a problem](How_to_handle_ending_time_of_a_problem.md) we
do not use any deadline timers in DOMjudge to specify the deadline a problem's final submission, but
instead we just inform the students of the deadline, and when its passed we disable submitting to
the 'grading' problem, and fetch the results from it. The problem's deadline is communicated to the
students during the course and in the problem description.

When the deadline of the problem is passed, the teacher has enfore the deadline on the problem and
fetch its results by doing:

1.  **Disable submission** to the grading version of the problem:

    - click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
    - click on '**Contests**'
    - on the bottom of the page click in the table on the contest of interest causing a details page
      of the contest is shown
    - on bottom of the page click on the 'Edit' button
    - on the bottom of the page in the problems table set '**Allow submit**'  
      field to '**No**' for the "grading" problem of interest.
    - on bottom of the page click on the 'Save' button

    This makes the **grading problem invisible** for teams, and they cannot submit to it anymore!
    However the teams can **still see the none-grading practicing problem**.

2.  Use download script to **download results** from grading problem:

        # create a new directory to collect all data there
        mkdir grading_data_processed_using_domjudge
        cd grading_data_processed_using_domjudge
        user=userwithadminrights; password='mypassword';
        fetch  --auth "${user}:${password}" --url https://domjudge.science.ru.nl/ \
               --contest yourcontest --problem grading_yourproblem --files --last-per-team

    The `fetch` script by default will download data about all submissions to a problem in a data
    file `contest_problem_data.pod`. By using the `--files` option it will also download the files
    in each submission as a zip file. We also use the `--last-per-team` option to limit the number
    of submisssions downloaded to only the last submission per team, because we are only interested
    in the last submission of each team to the grading problem.

3.  **Process results** of problem and format to excel

    Process the downloaded data where we take per team only the latest judgement of the latest
    submission and output this list as a csv file:

         NUMBER_OF_PUBLIC_SAMPLES=40
         process --skip $NUMBER_OF_PUBLIC_SAMPLES

    We skip the public samples when doing the grading, because we only want to grade on the secret
    samples. The `process` scripts outputs two `csv` files a basic one `gradings.csv` and one with
    more details `gradings.details.csv`.

4.  **Convert** these `csv` files to nice **excel** files containing a table:

         csv2xslt gradings.csv
         csv2xslt gradings.details.csv

### 5. Ending contest and course

If the contest is done, we **disable submitting** to the contest: the practicing problems stay
visible, but you cannot submit to it anymore. This prevents people submitting to old problems, but
let them still see them. This allows students to look back.

If the course is done, you can **disable all contests**: then all contests are hidden. Teams can
login but do not see anything anymore. Course is over, students do not need to look back anymore.

Every year start DOMjudge fresh! Don't use a system with old teams and old contests, because this
could only lead to unnecessary conflicts and confusion.
