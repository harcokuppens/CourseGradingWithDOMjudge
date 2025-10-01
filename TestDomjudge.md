# Test DOMjudge for use in a Course

<!--ts-->
<!-- prettier-ignore -->
   * [Intro](#intro)
   * [Step 1 : install DOMjudge with basic setup](#step-1--install-domjudge-with-basic-setup)
   * [Step 2 : get DOMjudge's admin password](#step-2--get-domjudges-admin-password)
   * [Step 3 : install CourseGradingWithDOMjudge scripts and test files](#step-3--install-coursegradingwithdomjudge-scripts-and-test-files)
   * [Step 4 : set up a work folder for your test](#step-4--set-up-a-work-folder-for-your-test)
   * [Step 5: create administrator accounts](#step-5-create-administrator-accounts)
   * [Step 6: create contest and team category for a course part](#step-6-create-contest-and-team-category-for-a-course-part)
   * [Step 7: create and mail users of a course part](#step-7-create-and-mail-users-of-a-course-part)
   * [Step 8: add problem to a course part](#step-8-add-problem-to-a-course-part)
   * [Step 9: students submitting solutions to a problem](#step-9-students-submitting-solutions-to-a-problem)
   * [Step 10: teacher fetching a problem's submissions and processes them into an Excel table](#step-10-teacher-fetching-a-problems-submissions-and-processes-them-into-an-excel-table)
   * [Step 11: Test another Course part with problem "Float special compare test"](#step-11-test-another-course-part-with-problem-float-special-compare-test)
<!--te-->

## Intro

This is a semi-automatic test to test DOMjudge for use in a course. By following the
steps in this document you can test and experience how to use DOMjudge for a course.
We will test a course setup where fake students commit their solutions to. After that
a fake teacher will fetch the results and processes them into a nice Excel table. We
repeat this for a second course part with a different configuration of the teams. We
will check all scripts work fine, and that student teams can only see the contest
they are given access to, and that they can only see their own data, and cannot see
each other's data. During this test we will test whether the setup fulfills point 1
till 5 of the specification in the
[Explanation course setup](Explanation_course_setup.md) page.

In this test we follow the instructions at
[Setting up DOMjudge for a course](Setting_up_domjudge_for_a_course.md), but apply it
for a test contest with test teams to test the system. In this document we use the
same commands as in
[Setting up DOMjudge for a course](Setting_up_domjudge_for_a_course.md), however for
more explanation about the commands and their context we refer to read the latter
document in which that is explained in detail.

In this document we use a local DOMjudge installation on a Linux machine using
Docker, where we followed installation instructions from
https://github.com/harcokuppens/DOMjudgeDockerCompose/. This server is available only
locally on the machine available with the URL:

```console
DOMJUDGE_SERVER="http://localhost:1080"
```

When running the test we will use scripts and test data from the git repo
https://github.com/harcokuppens/CourseGradingWithDOMjudge/. From the `test/`
subfolder of this repo we will set up a "Test course" in 2 parts where each part has
its own team configuration. For this setup we use the data files:

- `test/admins.csv` : teacher admin accounts
- `test/teams-part9001.csv` : fake student accounts for test part 1 of the course. We
  use the high number 9001 to make it clear this is a test.
- `test/teams-part9002.csv` : fake student accounts for test part 2 of the course
- `test/hello/` : folder for the "Hello World" problem which contains:
  - `publications.zip` : student submissions in a zip file The zip file contains a
    single root `submissions/` folder which contains subfolders for the separate
    submission. Each submission subfolder just contains the source code for that
    submission. DOMjudge automatically recognizes the language when the source files
    are submitted. Note that we have submissions with more than one source file.
  - `gradings.details.xlsx`: the final excel data table you will get when you have
    processed the submission results. This file will be regenerated in this test
    again, which we can then compare with this older version to see we still get
    similar results. Note: the files can differ in timing data.
  - `gradings.xlsx` : same as for `gradings.details.xlsx` but then with fewer details
- `test/fltcmp/` : folder for the "Float special compare test" problem. It contains
  the same files as described for the "Hello World" problem above, but then for this
  problem.

From the `bin/` subfolder we will use scripts described in detail [here](scripts.md).

We will show how to run the command with their output in a console. When showing
these examples the `$` is the prompt of your console. All lines without a starting
`$` are output lines of the command. E.g.

```console
$ echo "hello"
hello
```

In this test setup we import at several times import files to DOMjudge. We can do the
import manually via the "Import / export" page linked on the "DOMjudge Jury
interface". But we can also do it using the REST API using the `httpie` tool, which
we will use. Therefore, install this tool on your Linux system with the following
commands:

```console
$ sudo apt update
$ sudo apt install httpie
```

To split up this long test it is executed in 8 logical steps.

## Step 1 : install DOMjudge with basic setup

To start a course in DOMjudge follow from
[Setting up DOMjudge for a course](Setting_up_domjudge_for_a_course.md) the following
steps:

- step A:
  [Create a fresh and up to date DOMjudge installation](Setting_up_domjudge_for_a_course.md#a-create-a-fresh-and-up-to-date-domjudge-installation)
- step B:
  [Basic configuration](Setting_up_domjudge_for_a_course.md#b-basic-configuration)

To get an initial DOMjudge setup working on your local machine.

## Step 2 : get DOMjudge's admin password

Get admin password in ADMINPASSWORD variable. When DOMjudge is started you can read
the password for the `admin` user from inside the checked out repository from
https://github.com/harcokuppens/DOMjudgeDockerCompose/ at `./data/passwords/admin.pw`

```console
$ PASSWORD=$(cat ./data/passwords/admin.pw)
```

## Step 3 : install CourseGradingWithDOMjudge scripts and test files

To set up DOMjudge for a course we implemented some helper scripts in the
[CourseGradingWithDOMjudge repo](https://github.com/harcokuppens/CourseGradingWithDOMjudge)
to help you with this task.

To use the scripts of the CourseGradingWithDOMjudge repo we need to clone the repo.
We take `COURSE_GRADING_REPO_PATH` as the location where the repository gets cloned:

```console
$ COURSE_GRADING_REPO_PATH=~/git/CourseGradingWithDOMjudge
$ mkdir -p "$COURSE_GRADING_REPO_PATH"
$ git clone https://github.com/harcokuppens/CourseGradingWithDOMjudge "$COURSE_GRADING_REPO_PATH"
```

Then use its script's folder add it to your PATH:

```console
$ # add scripts to PATH
$ export PATH="$COURSE_GRADING_REPO_PATH/bin/:$PATH"
```

The following document gives more information about the scripts:

- [Overview and usage descriptions of scripts](scripts.md)

Finally, to easily access test data from the
[CourseGradingWithDOMjudge](https://github.com/harcokuppens/CourseGradingWithDOMjudge)
repository's `test` subfolder we define an environment variable:

```console
$ TEST_DATA_DIR=$COURSE_GRADING_REPO_PATH/test/
```

From now on we will use `$TEST_DATA_DIR/x` to access test data file `x`.

## Step 4 : set up a work folder for your test

It's wise to create and change to a work folder in which all test files are generated

```console
$ WORKDIR="path-where-to-store-my-work"
$ mkdir -p "$WORKDIR"
$ cd "$WORKDIR"
```

From now all test commands are run from this work folder.

## Step 5: create administrator accounts

In a normal DOMjudge installation teachers will use an administrator account in the
DOMjudge system. In this test we use the following administrator accounts file to set
up 2 fake teachers:

```console
$ cat  "$TEST_DATA_DIR/admins.csv"
name, email
Teacher1, teacher1.domjudge@example.com
Teacher2, teacher2.domjudge@example.com
```

We then create a `admin-accounts.yaml` import file for DOMjudge, by running the
command

```console
$ YOUR_EMAIL_ADDRESS="fill-in-your-email-address"
$ YOUR_NAME="fill-in-your-name"
$ YOUR_COURSE="Test course "
$ MAIL_FOLDER="mail/"
$ create-domjudge-admins "$TEST_DATA_DIR/admins.csv" "$MAIL_FOLDER" "$YOUR_COURSE" "$YOUR_EMAIL_ADDRESS" "$YOUR_NAME"
The folder 'mail/' has been created.
```

The resulting `admin-accounts.yaml` contains generated passwords for the admin users.

```console
$ cat admin-accounts.yaml
- id: "teacher1.domjudge_example.com"
  username: "teacher1.domjudge@example.com"
  password: "KR64XSYY7O"
  type: "admin"
  name: "Teacher1"
  email: "teacher1.domjudge@example.com"
- id: "teacher2.domjudge_example.com"
  username: "teacher2.domjudge@example.com"
  password: "T0M4KVPP0B"
  type: "admin"
  name: "Teacher2"
  email: "teacher2.domjudge@example.com"
```

This command also create a `mail/` folder, where each mail is from the sender
"\$YOUR_NAME" with email address "\$YOUR_EMAIL_ADDRESS":

```console
$ ls -1 mail
'teacher1.domjudge@example.com.eml'
'teacher2.domjudge@example.com.eml'

$ cat mail/'teacher1.domjudge@example.com.eml'

To: teacher1.domjudge@example.com
From: fill-in-your-email-address
Subject: Test course : DOMjudge login credentials for Admin account
Content-Type: text/plain; charset=us-ascii

Dear Teacher1,

Below are the login credentials for your DOMjudge admin account.

   User     : teacher1.domjudge@example.com
   Password : KR64XSYY7O

DOMjudge generated automatically also a team for this account on import.

   Team          : Teacher1
   Team Category : Jury

I also created a 'Test' contest which only allows submission from users in
the 'Jury' team category. This contest can be used to test new problems
in DOMjudge itself.

Best regards,
fill-in-your-name
```

First note that by generating both the import file and emails they both contain the
same generated passwords. Second the email talks a Test contest, however this
document we skipped setting up the Test contest, because that is a test playground
for admins, which we do not test here. We test a course setup where fake students
commit their solutions to.

The `mail/` folder contain raw emails you can send out with the sendmail command, but
before we do that we first create these admin accounts in DOMjudge using its REST API
with the command

We then create import files for user accounts and their teams from this CSV file

```console
$ DOMJUDGE_SERVER="http://localhost:1080"
$ ADMINUSER='admin'
$ PASSWORD=$(cat ./data/passwords/admin.pw)
$ # import accounts
$ https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@admin-accounts.yaml
```

Finally, we can send out the emails with the command:

```
$ MAIL_FOLDER="mail/"
$ send-emails -t "$YOUR_EMAIL_ADDRESS" "$MAIL_FOLDER"
TEST MODE: sending emails to the sender's email address instead; Sending to 'YOUR_EMAIL_ADDRESS'.
sending mail/teacher1.domjudge@example.com.eml
sleep 6s to prevent overflowing mail server
sending mail/teacher2.domjudge@example.com.eml
sleep 6s to prevent overflowing mail server
```

Make sure you use a Linux machine for which sendmail is configured. If somehow the
`send-emails` method does not work for you, you can use one of the alternative
methods described on the page
[Sending batch of personalized emails](Sending_batch_of_personalized_emails.md).

**IMPORTANT**: The `-t` option enables test mode. In our test data we use fake
invalid email addresses to which never mail can be sent to. Depending on the mail
server you directly get an error, or you will get a bounce message back which says
the email is not deliverable, or the mail server just drops the message without
informing you. To still test sending email with our test data with fake invalid email
addresses we use the '-t' option in the `send-emails` command above. This brings the
`send-emails` command in test mode which causes it to send all emails to the sender's
email address instead to the recipient's email address specified in the `.eml` files.
This means all emails get send to the specified `$YOUR_EMAIL_ADDRESS` in the
`send-emails` command. However, when you view the emails in the inbox they still say
the recipient is the email address specified in the `.eml` file. The email in the
inbox looks exactly the same as it would be delivered to the real recipient in the
`.eml` file!

For real world cases off course you would not use the `-t` option, and send the
emails to the recipients specified in the `.eml` files.

As an alternative to this test mode, you could use the https://www.mailinator.com/
service which provides temporary disposable email addresses. E.g. if you instead would
use an email address like testperson@mailinator.com, then you can check if the email
is really send by looking at the
[mailinator inbox for testperson](https://www.mailinator.com/v4/public/inboxes.jsp?to=testperson).
However, if you test server is reachable from the internet, do not send credentials
of an online available test server to mailinator.com, because the data is publicly
readable there. Make sure you test server is not reachable from the internet when
using mailinator.com.

All steps from now on in this page can be done by one of the new administrators. We
will use from now on:

```console
$ DOMJUDGE_SERVER="http://localhost:1080"
$ ADMINUSER='teacher1.domjudge@example.com'
$ PASSWORD='KR64XSYY7O'
```

Just run these commands now in your console once. On the following command examples
in this page we assume these variables are already set.

## Step 6: create contest and team category for a course part

The course will be given in different parts, where each part arranges the students
differently over the teams. Each part has a different teams' configuration. The first
gets number 1, the second 2, etc... For this test we use number 9001, to make it
clear it is a test. We later also have 9002 for the test for the second course part.

```
COURSEPART_NUMBER=9001
```

Step-by-step instructions to set up course part:

1.  Create data folder per course part

        cd "$WORKDIR"
        COURSEPART_NUMBER=9001
        mkdir part${COURSEPART_NUMBER}
        cd part${COURSEPART_NUMBER}

    It is a good practice to store the files to set up users and teams in a separate
    folder per course part e.g. `part${COURSEPART_NUMBER}`. Then we have all user and
    team data for all course parts nicely stored on disk as backup.

2.  First add the `Team Category` named `part9001-teams-config` which we can use for
    the first course part

        click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
        click on 'Team Categories'
        click on 'Add new category' button
        in 'Name' box enter text "part9001-teams-config"
        and set 'Visible' radio button to "No"
        note: self-registration is by default 'No'
        click on 'Save' button at bottom of window

3.  Then create a different contest for each course part. We take as course name
    'testcourse', but you can use any course name here.

         click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
         click on 'Contests'
         click on 'Add new contest' button
           in 'External ID', 'Shortname' and 'Name' boxes enter 'testcourse-part-9001'
           select value in "Activate time" and
           paste it into "Start time" and "End time" fields.
           In the "End time" field increase the date by 1 year.
           "Record balloons" set to "No"
           "Medals enabled" set to "No"
           "Enable public scoreboard" set to "No"
           "Open contest to all teams" set to "No"
           select in "Team categories" the value 'part9001-teams-config'

## Step 7: create and mail users of a course part

Step-by-step instructions to create users and teams in DOMjudge:

1.  Create a `teams-part9001.csv` file, where each line represents a team, with two
    or three columns "teamname","names", and "emails", where the first is optional.
    The `teams-part9001.csv` file contains the minimal information required to create
    users and teams in DOMjudge.

    For our Test we use a pre-generated file in `$TEST_DATA_DIR/teams-part9001.csv`
    file with 50 teams of each 2 students. Each group is called "GroupX" and each
    student is called "StudentX", and has email "student01.domjudge@example.com":

    ```console
    $ cat "$TEST_DATA_DIR/teams-part9001.csv"
    "teamname","names", "emails"
    "group001","Student 001, Student 002","student001@example.com, student 002@example.com"
    "group002","Student 003, Student 004","student003@example.com, student 004@example.com"
    ....
    ```

    Note that you also can generate this list with the shell script:

    ```shell
    printf '"teamname","names", "emails"\n'  > teams-part9001.csv
    NUM_TEAMS=50
    for i in `seq 1 $NUM_TEAMS`
    do
        k=$((i*2)); j=$((k-1))
        printf '"group%03d","Student %03d, Student %03d","student%03d@example.com, student%03d@example.com"\n' $i $j $k $j $k
    done >> teams-part9001.csv
    ```

    Note later we will use a different teams' configuration for test course part 2:

    ```console
    $ cat "$TEST_DATA_DIR/teams-part9002.csv"
    "teamname","names", "emails"
    "group001","Student 001, Student 051","student001@example.com, student 051@example.com"
    "group002","Student 002, Student 052","student002@example.com, student 052@example.com"
    ....
    ```

    Note that you also can generate this list with the shell script:

    ```shell
    printf '"teamname","names", "emails"\n'  > teams-part9002.csv
    NUM_TEAMS=50
    for i in `seq 1 $NUM_TEAMS`
    do
        k=$((i+NUM_TEAMS));
        printf '"group%03d","Student %03d, Student %03d","student%03d@example.com, student%03d@example.com"\n' $i $i $k $i $k
    done >> teams-part9002.csv
    ```

2.  From the `teams-part9001.csv` file create users and teams in DOMjudge.

    We do this in 2 steps:

    1.  Then from `teams-part9001.csv` and the `COURSEPART_NUMBER` we generate the
        `teams.yaml` and `users.yaml` import files by running the script:

        ```console
        $ COURSEPART_NUMBER=9001
        $ create-domjudge-import-files  "$TEST_DATA_DIR/teams-part9001.csv" $COURSEPART_NUMBER
        ```

        The `COURSEPART_NUMBER` is required to generate unique user and team IDs for
        that course's part.

    2.  Import `teams.yaml` and `accounts.yaml` import files into DOMjudge.

        We can do the import manually via the "Import / export" page linked on the
        "DOMjudge Jury interface". But we can also do it using the
        [REST API](https://www.domjudge.org/docs/manual/main/develop.html#api) using
        the [httpie tool](https://httpie.io):

        ```console
        $ # import teams (hack: using json@teams.yaml still allows import of yaml)
        $ https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/teams" json@teams.yaml

        $ # import accounts
        $ https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@accounts.yaml
        ```

3.  Email users to inform them their credentials

    The generated `teams.yaml` and `users.yaml` files are also used to generate
    emails which sends users their credentials and team info.

    We generate emails with the command:

    ```console
    $ YOUR_EMAIL_ADDRESS="fill-in-your-email-address"
    $ YOUR_NAME="fill-in-your-name"
    $ YOUR_COURSE="Test course "
    $ MAIL_FOLDER="studentmail/"
    $ create-domjudge-credentials-mails  "accounts.yaml" "teams.yaml" "$MAIL_FOLDER"  "$YOUR_COURSE" "$YOUR_EMAIL_ADDRESS" "$YOUR_NAME"
    The folder 'studentmail/' has been created.
    ```

    This will generate in the `studentmail/` subfolder the mails which we will send
    to the students. We do not directly send the emails, because we want to be able
    to inspect the emails before sending.

    With the following script, we use the Linux `sendmail` command to send out the
    emails:

    ```console
    $ send-emails -t "$YOUR_EMAIL_ADDRESS" "studentmail/"
    ```

    This is the same test command we used to send out the administrator emails in
    step 5. You can test sending emails again, however remember, as in detailed
    explained in step 5, we use invalid email addresses in our example data, and
    therefore use the test mode to send the emails to "\$YOUR_EMAIL_ADDRESS" instead.
    This causes the inbox of "\$YOUR_EMAIL_ADDRESS" to get many emails in case you
    have many students. However, after every email send, the `send-emails` command
    does sleep a few seconds to not overload the mail servers. During this sleep we
    can use the `CTRL-C` key to stop the command early if you want to test sending
    only the first few emails.

## Step 8: add problem to a course part

In this test description we use the simple 'hello world' problem which is already
imported by default in DOMjudge. We just add the Hello world problem to our just
created 'testcourse-part-9001' contest. Below are instructions to add an existing
problem in DOMjudge to a contest, and how to disable lazy evaluation for the problem
to test it thoroughly!

We can easily add an existing problem into a contest:

- click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
- click on '**Contests**'
- on bottom of the page click on the 'Edit' button
- on the edit page of the contest is a table with all problems in the contest
- at the last line is a + button to add your problem to this contest.

For our test case we edit the 'testcourse-part-9001' contest and to it the "Hello
World" problem.

We can set the evaluation laziness of a problem with:

- click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
- click on '**Contests**'
- on the bottom of the page click in the table on the contest of interest causing a
  details page of the contest is shown
- on bottom of the page click on the 'Edit' button
- on the edit page of the contest is a table with all problems in the contest
- for the problem of interest edit the in the last column "**Lazy eval**" to
  "**Yes**" for the "**practicing**" version of the **problem**, and to "**No**" for
  the "**grading**" version of the **problem**.

For our test case we set laziness to "**No**".

In case you also want to test other problems. You can of course also test DOMjudge
with other problems we can
[import](Setting_up_domjudge_for_a_course.md#add-problem-to-the-course). Below are
also instructions how to import a new problem into DOMjudge:

- click on 'DOMjudge' in top right corner, to get 'DOMjudge Jury interface'
- click on 'Problems'
- on bottom of the page click on the 'Import problem' button
- now we are redirected to the "Import and export" page
- in this page on the "Problems; Import archive"

  1. Select the contest where to import the problem.  
      Either select a contest's name or you can also select no contest, by selecting the
     value 'Do not add / update contest data'.
  2. In the "Problem archive" field select the problem zip to import

## Step 9: students submitting solutions to a problem

We can then use the `batch-submit` script to send all submissions to DOMjudge, where
we send each submission by a separate account from the `$ACCOUNTS_YAML_FILE` file.
The first submission in the `hello/submissions.zip` is submitted with the first
account in the `$ACCOUNTS_YAML_FILE` file. The second submission with the second
account, etc...

```console
$ batch-submit -a "$ACCOUNTS_YAML_FILE" -c testcourse-part-9001 -p hello -f 'part9001-student%s@mailinator.com' $TEST_DATA_DIR/hello/submissions.zip
```

Because in test part 9001 student 1 and student 2 are together in a team 1, you will
get two submissions in team 1. When we fetch the latest submission per team, we will
fetch only half of the submissions submitted. Later in test part 9002 the student 1 a
student 51 are together in team 1, and you will only get on submission per team. When
we fetch then the latest submission per team, we will fetch all the submissions
submitted.

## Step 10: teacher fetching a problem's submissions and processes them into an Excel table

In the next steps we fetch and process the student result to a table in an Excel
spreadsheet.

1.  Use download script to **download results** from grading problem:

        fetch  --auth "${ADMINUSER}:${PASSWORD}" --url "$DOMJUDGE_SERVER" \
               --contest testcourse-part-9001 --problem hello --files --last-per-team

    The `fetch` script by default will download data about all submissions to a
    problem in a data file `contest_problem_data.pod`. By using the `--files` option
    it will also download the files in each submission as a zip file. We also use the
    `--last-per-team` option to limit the number of submissions downloaded to only
    the last submission per team, because we are only interested in the last
    submission of each team to the grading problem.

2.  **Process results** of problem and format to excel

    Process the downloaded data where we take per team only the latest judgement of
    the latest submission and output this list as a CSV file:

         NUMBER_OF_PUBLIC_SAMPLES=0
         process --skip $NUMBER_OF_PUBLIC_SAMPLES

    We skip the public samples when doing the grading, because we only want to grade
    on the secret samples. The `process` scripts outputs two `csv` files a basic one
    `gradings.csv` and one with more details `gradings.details.csv`.

    Note that for our specific problem we do not skip any samples.

3.  **Convert** these `csv` files to nice **Excel** files containing a table:

         csv2xslt gradings.csv
         csv2xslt gradings.details.csv

    For the "Hello World!" problem you can compare whether the newly created
    **Excel** files are similar to those in `$TEST_DATA_DIR`. Note, they cannot be
    exactly equivalent because they contain timings.

## Step 11: Test another Course part with problem "Float special compare test"

We repeat step 6 till 10 for test Course part 9002 with problem "Float special
compare test" (fltcmp), but now name it COURSEPART_NUMBER=9002, to make it clear it
is a test for the second test part.

For a new course part we have to create a new contest, team category, users and
teams. Then add the problem "Float special compare test" and batch submit as students
the submissions in `$TEST_DATA_DIR/fltcmp/submissions.zip`.

Below is a summary of the commands to run:

```console

$ # create new data dir for second part
$ cd "$WORKDIR"
$ COURSEPART_NUMBER=9002
$ mkdir part${COURSEPART_NUMBER}
$ cd part${COURSEPART_NUMBER}

$ # now manually create in the jury interface in DOMjudge
$ # 1. add the Team Category named 'part9002-teams-config'
$ # 2. add the contest 'testcourse-part-9002' which may only be accessed by Team Category 'part9002-teams-config'

$ # create users an their teams and import them
$ create-domjudge-import-files  "$TEST_DATA_DIR/teams-part$COURSEPART_NUMBER.csv" $COURSEPART_NUMBER
$ # import teams (hack: using json@teams.yaml still allows import of yaml)
$ https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/teams" json@teams.yaml
$ # import accounts
$ https -a "$ADMINUSER:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@accounts.yaml

$ # note: we skip generating and sending emails to the test students for this second part

$ # let students submit data
$ batch-submit -a "$ACCOUNTS_YAML_FILE" -c testcourse-part-9002 -p fltcmp -f 'part9001-student%s@mailinator.com' $TEST_DATA_DIR/fltcmp/submissions.zip

# fetch and process the data
$ fetch  --auth "${ADMINUSER}:${PASSWORD}" --url "$DOMJUDGE_SERVER" \
      --contest testcourse-part-9002 --problem fltcmp --files --last-per-team
$ process
$ csv2xslt gradings.csv
$ csv2xslt gradings.details.csv
```
