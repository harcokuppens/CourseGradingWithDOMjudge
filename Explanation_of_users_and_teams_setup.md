# Explanation of Users and Teams setup

**Table of Contents**

<!--ts-->
* [Explanation of Users and Teams setup](#explanation-of-users-and-teams-setup)
   * [Users and teams](#users-and-teams)
   * [Design choices](#design-choices)
   * [User and teams setup](#user-and-teams-setup)
      * [User and team in DOMjudge](#user-and-team-in-domjudge)
      * [Inspect required information](#inspect-required-information)
      * [The teams.csv input file](#the-teamscsv-input-file)
   * [Conclusion](#conclusion)
<!--te-->

In this document we describe our choosen design of how to setup teams and users in DOMjudge, and how
an admin user of DOMjudge, by providing minimal information in a CSV file, can setup and mail around
these credentials using automated scripts. The admin's work is made easy.

## Users and teams

In DOMjudge you need both a user and a team because:

- a user without team cannot submit code
- a team without user cannot login into DOMjudge

DOMjudge also has the limitation that an user can only belong to one team. So it is not possible to
assign a user to multiple teams. Probably the reason for this is that in a programming contest a
user always belongs to a single team, and then when a user submits a solution it is immediately
clear to which team the submission belongs.

If you want to have different teams per course part, then either you have to make a new user account
for each user for the new course part, or you have to update the user account it belongs to the new
team and removing it from the old team. The user then cannot see old work done under the old team
anymore, because he is removed from that team.

That's why we decided to just make a new user account for each user for the new course part. The
user can then still see als its work in the all course parts just by using the different credentials
it got per course part.

## Design choices

When using DOMjudge for grading in a course we use the following design choices to setup teams and
users in DOMjudge:

- students **work together in teams**
- students in a specific team each get its **own login** to that team. An admin then can see in
  which student did which submissions in a team.

- if an user is logged in it should be clear to which team the user belongs by **displaying** the
  **team ID** and the **team's member names**.
- all students get **per email their user credentials of the team** they are in.
- each student gets **per course part a new login in a new team**  

## User and teams setup

### User and team in DOMjudge

To meet above design choices we define the user and team fields as follows:

      for User
      +  ID:                 "u-1-005"                => the ID of 5th user in part 1 of the course
      +  Username:           "part1-<email addres of user>"   => used as login name
         Password:           .....
         Full name:          "full name of user"
         Email:              "email addres of user"
         TeamId:             "t-1-003"                => the team the user belongs to (can only be one team)

      for Team
      +   ID:                "t-1-003"                => the ID of 3th team in part 1 of the course
          Name:              "part1-team003"          => the team 3th team  in part 1 of the course
      +*  Display Name:      "part1-team003: <one or multiple full names of team members>"
          Category:          "part1-teams-config"

An user logs in with its `Username` and `Password` and then only sees its team's `Display Name`
(marked by `*`).

An admin sees both the user's `Id` and `Username`, and its team's `Id` and `Display name` when he
views a submission of an user (marked by `+`).

All teams in the first part of the course get the same `Category` value `part1-teams-config`. This
category is used in two ways:

1. for this specific category we set the `visible` field to false causing all teams unable to see
   each others submissions.

2. we allow that course part's contest only be accessible by teams with that category.

We use the user's email address to define also its `Username` because an email address is unique for
an user. We prefix it with `partX-` so that we have a separate user account per course part X. Most
student accounts in most education institutes also have a reasonable email address containing the
user's full name. This helps an admin a lot, because When he looks at a submission he only sees the
user's `Username` which then already reflects the user's full name.

The user's and team's IDs are external IDs we give ourself to the team! We configured DOMjudge to
allow external IDs for configuration data.

Using internal IDs was inconvenient for creating users and teams. Because for creating a user we
needed the the team ID it belong to.  
If using internal IDs in DOMjudge we then first needed to create the teams. Then after creating the
teams we had to get the internal IDs of the teams from DOMjugde before we could create the users
belonging to those teams. With external IDs we can choose our own team IDs and can we directly
create the users belonging to them.

### Inspect required information

If we look at all the information for setting up teams and users we see that some fields like IDs
can be just generated, and other fields like `Username` can be derived from other information. After
looking carefully we constructed a list of the minimal information required:

Full name of user Email addres of user Which users belong to the same team

This leads us to a simple input file we base our scripts on: `teams.csv`.

### The `teams.csv` input file

Create a `csv` file, where each line represents a team, with the two columns:

1.  the first column 'names' is the name of the team. We use as team name a string containing a
    comma separated set of student names.
2.  the second column 'emails' we set with a string containing a comma separated set of emails, one
    per student.

You can offcourse also make teams with only one student with only a single name and email in the
columns.

Example:

    $ cat teams.csv
    "names","emails"
    "Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
    "Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
    ....

Often you can export groups of students from some source, eg.
[Brightspace](Import_teams_from_brightspace_groups_into_DOMjudge.md), and automatically generate the
`teams.csv` file with a script. Sometimes we want the same numbering scheme of groups as in an
external system such as Brightspace, so that teamX in DOMjudge matches groupX in Brightspace. To
make this possible we allow also a `teamname` column in our `teams.csv` file:

Example:

      $ cat teams.csv
      "teamname","names", "emails"
      "group1","Piet Venema, Jan Jansen", "pietvenema@gmail.com,janjansen@gmail.com"
      "group2","Henk Hier, Piet Praat", "henkhier@gmail.com, pietpraat@gmail.com"
      ....

The `teams.csv` file contains the minimal information required to create users and teams in
DOMjudge. We use scripts to generate from this `teams.csv` file a `users.yaml` and `teams.yaml` to
import users and teams into DOMjudge, and to generate emails which sends users their credentials and
team info. The scripts only need the `teams.csv` and the course part's number as input. The course
part number is required because as we can see above its used in the user's `Id` and `Username`, and
the team's `Id`, `Name`, and `Display name`, because as explained above for each coursepart we
generate a different user account for each user.

Using the scripts doing this work are not explained in this document. The aim of this document is to
describe the design of how setting teams in DOMjudging can be made easy using scripts.

## Conclusion

The `teams.csv` file explained above is the only information an end user has to supply to create
users and teams in DOMjudge. Using information in this file we can use scripts to generate accounts
and teams in DOMjudge and inform students of their credentials and team with generated emails.
