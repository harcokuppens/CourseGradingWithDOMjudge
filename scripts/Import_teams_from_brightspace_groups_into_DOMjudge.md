# Imports teams from Brightspace groups into DOMjudge

Brightspace is the University's Learning Management System (LMS). A Learning Management System (LMS)
is an online management system used for teaching and learning.

In Brightspace within a course you can make groups of students. With a trick you can export these
groups, to use them to create teams in DOMjudge.

## Export groups in Brightspace to CSV file

In Brightspace you can export the groups in a course by following the next steps:

     Administration -> Grades -> click on "Export"
        -> for "Export Grade Items For" select "Groups"
        -> for "Groups:"   select "Practical 1"  groups category (not indented in select box)
        -> then press on "Apply" (otherwise it won't change it!)
        -> for "Key Field" select "both"
        -> "Sort by": keep to "default"
        -> under "Grade Values" deselect all
        -> at "User Details"  select all

                User Details:
                Last Name
                First Name
                Email

                Group Membership

        -> at 'Choose Grades to Export'
           deselect all

        => at bottom 'press' Export to CSV
        => then press 'Download'


The exported file is a CSV file with at least the following fields:

- Username
- Last Name
- First Name
- Email
- name of groups category; in example above we had 'Practical 1' as groups category

The groups category column in the exported CSV file contains the group to which the student belongs:
'Group X' where X is the number of the group.

# Convert Brightpace's exported CSV file to the `teams.csv` CSV file import format we use for DOMjudge.

To convert Brightpace's exported CSV file to our `teams.csv` format we wrote the following script:

    $ brightspace2teams -h
    usage: brightspace2teams [-h] BRIGHTSPACE_CSV_FILE GROUPS_CATEGORY TEAMS_CSV_FILE

    from DOMjudge import files accounts.yaml and teams.yaml we generate credential emails in a local mail/ directory

    positional arguments:
    BRIGHTSPACE_CSV_FILE  CSV file with user data exported from DOMjudge (includes Group Membership)
    GROUPS_CATEGORY       the groups category used when exporting the exported CSV file
    TEAMS_CSV_FILE        the teams CSV output file used for DOMjudge

    options:
    -h, --help            show this help message and exit

An example how to use this script:

    brightspace2teams brightspace.csv 'Practical 1' teams.csv

where 'Practical 1' is the groups category in Brightspace which teams we exported.

The `teams.csv` can the be used with our other DOMjudge scripts to generate DOMjudge import files
for users and teams, and to generate emails to send the students their user credentials in DOMjugde.
