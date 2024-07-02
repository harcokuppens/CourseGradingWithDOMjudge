# Explanation of Users and Teams setup 


## design choices

When using DOMjudge for grading in a course we use the following design choices to setup teams and users in DOMjudge:
      
   * students **work together in teams**
      
   * students in a specific team get a **shared login** to that team. By having one user per team, we effectively only have teams to which we can login. We simplify things by not caring about the separate users. (KISS principle).
      
     We assume students cooperate fairly in a team. We do not want to use DOMjudge to check which student did the most work by counting submissions per student in a team.
        If therr are problems with cooperation in a team, a student should discuss this with the teacher.

   * if logged in it should be clear which users are logged in by **displaying** their **names** and their **team id**.
      
   * all students get **per email the credentials of the team** they are in. Students in the same team get the same team credentials send.     


## implementation


### user and team in DOMjudge

In DOMjudge you need both a user and a team because:

* a user without team cannot submit code
* a team without user cannot login into DOMjudge


We create per team only one user, so the user account and its team account are basicly the same.
So we configure them also with the same data:

      for User
       * Username:           "teamX"    => used as login name            
                                  `-> X is group id for our own administration! 
         Full name:          "one/multiple full names of team members"     
         Password:           .....
         
         
      for Team 
          Name:              "teamX"                                     
       *  Display Name:      "one/multiple full names of team members"
   
  The fields with `*` are shown on top of the submission page. 
  So when viewing a submission we see at top:
     
      team: one/multiple full names of team members       user: team-X     
                |                                                 `-> "Username" is shown here, not Full name
                `-> Team's "Display Name" is shown here

 Information about the team is very clearly displayed because we see both "full names of team members" and its "team id"!
 
 
The team id  is an external id we give ourself to the team!  We configured DOMjudge to allow external id's for configuration data.  

Using internal id's was inconvenient for creating users and teams. Because for creating a user we needed the the team id it belong to.  If using internal id's in DOMjudge we then first needed to create the teams. Then after creating the teams we had to get the internal id's of the teams from DOMjugde before we could create the users belonging to those teams. With external id's we can choose our own team id's and can we directly create the users belonging to them.

### Inspect required information

So for each user/team pair in DOMjudge we need to following information:

1. Username(user)/Name(team):  loginname set with value "teamX", where X is integer specifing team number
2. Password(user): password for login
3. Full name(user)/Display name(team):	 written out name of single student/multiples students in team

The first two fields can be generated, but the third field we have to supply. However in our design choices we also require to email the credentials to  each team member. So we need also per team the email addresses of its team members. This leads us to a simple input file we base our scripts on: `teams.csv`.

### The `teams.csv` input file


Create a `csv` file with two columns:

   1. the first column is  the name of the team. We use as team name a string containing a comma separated set of student names.
   2. the second column we set with a string containing a comma separated set of emails, one per student.

Example:

    $ cat teams.csv
    "Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
    "Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
    ....
          
   You can offcourse also make teams with only one student with only a single name and email in the columns.           
   
   Often you can export a list of students from some source, eg. brightspace,  and automatically generate the `teams.csv` file with a script. If you then want to combine students in teams you can edit this file manually to group them then in teams.


###  Extended `teams_domjudge.csv`
Using `teams.cvs` file as input we can generate the extra information that DOMjudge needs:


    generate_domjudge_data.py teams.csv TEAM_CONFIGURATION_NUMBER  > "teams_domjudge.csv"
   
The  `TEAM_CONFIGURATION_NUMBER` is used to generate a team category which is unique. For the first part of the course we would use 1, for the second part of the course 2 etc...

Then we generated a file  "accounts.domjudge.csv" containing also the fields
  
 * loginname
 * password
 * team category

This file we use a input for generating users and account for DOMjudge, and emails we will send to the students to give them the login credentials to DOMjudge.

### teams.yaml and accounts.yaml

When we run the script:

    create_domjudge_import_files.py  "teams_domjudge.csv" 

we generate two files `teams.yaml` and `users.yaml` which we can import to DOMjudge to create the teams and users in DOMjudge. We can do the import manually 
via the "Import / export" page linked on the "DOMjudge Jury interface". But we can also do it using the [REST API](https://www.domjudge.org/docs/manual/main/develop.html#api) using the [httpie tool](https://httpie.io):

    DOMJUDGE_SERVER="http://localhost:12345"    
    PASSWORD="secret" 
    # admin password can be read from ./data/passwords/admin.pw 
    # see https://github.com/harcokuppens/DOMjudgeDockerCompose/
    
    # import teams (hack: using json@teams.yaml to allow still import yaml)
    https -a "admin:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/teams" json@teams.yaml

    # import accounts
    https -a "admin:$PASSWORD" --check-status -b -f POST "$DOMJUDGE_SERVER/api/v4/users/accounts" yaml@accounts.yaml


### mailing credentials to students

When we run the script:

    create_domjudge_credentials_mails.py  "teams_domjudge.csv". "mail/" 

we generate in the `mail/` subfolder the mails to be send to the students.
We do not directly send the emails, because we want to be able to inspect the emails before sending.

With the following script we  can send out the emails:

    send_emails.bash  "thesender@gmail.com"  "mail/"


The send_emails.bash script:

          #!/bin/bash

          from_email="$1"
          
          for mailfile in mails/*
          do 
              printf "\n\n"
              to_email=$(basename $mailfile)
              printf "sending $mailfile to $to_email\n"
              printf -- "--------------------------\n"
              # prefer sendmail, because then also subject and recipient in mailfile
              sendmail -t -i   -f $from_email  < $mailfile
              sleep 1 # not to overflow mail server
          done
