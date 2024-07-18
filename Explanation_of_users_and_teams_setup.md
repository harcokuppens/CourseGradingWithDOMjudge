# Explanation of Users and Teams setup 

**Table of Contents**
<!--ts-->
* [Explanation of Users and Teams setup](#explanation-of-users-and-teams-setup)
   * [design choices](#design-choices)
   * [implementation](#implementation)
      * [user and team in DOMjudge](#user-and-team-in-domjudge)
      * [Inspect required information](#inspect-required-information)
      * [The teams.csv input file](#the-teamscsv-input-file)
      * [Extended teams_domjudge.csv](#extended-teams_domjudgecsv)
      * [teams.yaml and accounts.yaml](#teamsyaml-and-accountsyaml)
      * [mailing credentials to students](#mailing-credentials-to-students)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: harcok, at: wo jul 17 20:21:17 CEST 2024 -->

<!--te-->

In this document we describe our choosen design of how to setup teams and users in DOMjudge, and how an admin user of DOMjudge, by providing minimal information in a CSV file, can setup and mail around these credentials using automated scripts. The admin's work is made easy.

## Users and teams 

In DOMjudge you need both a user and a team because:

* a user without team cannot submit code
* a team without user cannot login into DOMjudge

DOMjudge also has the limitation that an user can only belong to one team. So it is not possible to assign a user to multiple teams. 

If you want to have different teams per course part, then either you have to make a new user account for each user for the new course part, or you have to update the user account it belongs to the new team and removing it from the old team. The user cannot see old work done under the old team anymore.  

That's why we decided to just make per team a single dummy account, which gets shared by the team members. For each course part we create new teams, and the new credentials are send to the new team members. A user then can still login to all the different teams it belonged to for the different course parts, meaning he can still see all work he has done in DOMjudge.

## Design choices

When using DOMjudge for grading in a course we use the following design choices to setup teams and users in DOMjudge:
      
   * students **work together in teams**
      
   * students in a specific team get a **shared login** to that team. By having one user per team, we effectively only have teams to which we can login. We simplify things by not caring about the separate users. (KISS principle). 
      
     We assume students cooperate fairly in a team. We do not want to use DOMjudge to check which student did the most work by counting submissions per student in a team. If there are problems with cooperation in a team, a student should discuss this with the teacher.

   * if logged in it should be clear which users are logged in by **displaying** their **names** and their **team id**.
      
   * all students get **per email the credentials of the team** they are in. Students in the same team get the same team credentials send.     


## User and teams setup 


### user and team in DOMjudge

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

   1. the first column 'names' is  the name of the team. We use as team name a string containing a comma separated set of student names.
   2. the second column 'emails' we set with a string containing a comma separated set of emails, one per student.

Example:

    $ cat teams.csv
    "names","emails"
    "Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
    "Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
    ....
          
   You can offcourse also make teams with only one student with only a single name and email in the columns.           
   
   Often you can export a list of students from some source, eg. brightspace,  and automatically generate the `teams.csv` file with a script. If you then want to combine students in teams you can edit this file manually to group them then in teams.

This is the minimal input required, all other fields like loginname,password and team-category can be generated and added to this `teams.csv`.
Once all data is generate we can use it to create import files for teams and accounts in DOMjudge, and to generate emails to send the team member their credentials. 

When generating a team's login we can use something like "teamXpartY" so that its clear from the login name to which part of the course the team belongs.

The scripts doing this work are not explained in this document. The aim of this document is to describe the design of how setting teams in DOMjudging can be made easy using scripts.

## Conclusion

The `teams.csv` file explained above is the only information an end user has to supply to create users and teams in DOMjudge. Using information in this file we can use scripts to generate accounts and teams in DOMjudge and inform students of the credentials with generated emails. 

