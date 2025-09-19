# Scripts

## Overview scripts

Info scripts

- [`domjudge-language-versions`](#domjudge-language-versions) - get language versions
  of official judgehost docker image
- [`domjudge-extra-language-versions`](#domjudge-extra-language-versions) - get
  language versions of my judgehost docker image with extra languages

Administrative scripts

- [`create-domjudge-admins`](#create-domjudge-admins) - from a admins.csv file create
  the DOMjudge import file admin-accounts.yaml for users with 'admin' role, and
  generate credential emails in a local mail/ directory to inform the admins
- [`brightspace2teams`](#brightspace2teams) - import a CSV file with user data
  exported from DOMjudge into a teams.csv file. The user data specifies per user its
  group.
- [`create-domjudge-import-files`](#create-domjudge-import-files) - from a teams.csv
  file create DOMjudge import files accounts.yaml and teams.yaml
- [`create-domjudge-credentials-mails`](#create-domjudge-credentials-mails) - from
  DOMjudge import files accounts.yaml and teams.yaml we generate credential emails in
  a local mail/ directory
- [`send-emails`](#send-emails) - send out all emails setup in a local/ mail
  directory

Students submit

- [`submit`](#submit) - Submit a solution for a problem.

Teacher gets results

- [`fetch`](#fetch) - Fetch students final solutions for a problem in a contest into
  a data file.
- [`process`](#process) - Process data file with final solutions for a problem in a
  DOMjudge contest into a table in a csv file
- [`csv2xslt`](#csv2xslt) - converts a csv file to a nice excel file containing a
  table

## Usage descriptions of the scripts

### domjudge-language-versions

```console
$ domjudge-language-versions -h
usage: domjudge-language-versions [-h] DOMJUDGE_VERSION

description: checking language versions on docker image domjudge/judgehost:DOMJUDGE_VERSION

```

### domjudge-extra-language-versions

```console
$ domjudge-extra-language-versions -h
usage: domjudge-extra-language-versions [-h] DOMJUDGE_VERSION

description: checking language versions on docker image harcokuppens/judgehost-extra-languages:DOMJUDGE_VERSION

```

### create-domjudge-admins

```console
$ create-domjudge-admins -h
usage: create-domjudge-admins [-h]
                              ADMINS_CSV_FILE MAIL_FOLDER FROM_EMAILADDRESS
                              FROM_FULL_NAME

from a admins.csv file create the DOMjudge import file admin-accounts.yaml for
users with 'admin' role, and generate credential emails in a local mail/
directory to inform the admins

positional arguments:
  ADMINS_CSV_FILE    CSV file with amin data for importing admins into
                     DOMjudge
  MAIL_FOLDER        folder in which the mails are generated. This folder is
                     created for you, and should not already exist.
  FROM_EMAILADDRESS  Email address used to send the generated emails.
  FROM_FULL_NAME     Full name of person which sends the generated emails.
                     (person of FROM_EMAILADDRESS)

options:
  -h, --help         show this help message and exit
```

### brightspace2teams

```console
$ brightspace2teams -h
usage: brightspace2teams [-h]
                         BRIGHTSPACE_CSV_FILE GROUPS_CATEGORY TEAMS_CSV_FILE

Import a CSV file with user data exported from DOMjudge into a teams.csv file.
The user data specifies per user its group.

positional arguments:
  BRIGHTSPACE_CSV_FILE  CSV file with user data exported from DOMjudge
                        (includes Group Membership)
  GROUPS_CATEGORY       the groups category used when exporting the exported
                        CSV file from Brightspace
  TEAMS_CSV_FILE        the teams CSV output file used for DOMjudge

options:
  -h, --help            show this help message and exit
```

### create-domjudge-import-files

```console
$ create-domjudge-import-files -h
usage: create-domjudge-import-files [-h] TEAMS_CSV_FILE COURSEPART_NUMBER

from a teams.csv file create DOMjudge import files accounts.yaml and
teams.yaml

positional arguments:
  TEAMS_CSV_FILE     CSV file with ("teamname",)"names","emails" header. The
                     "teamname" field is optional.
  COURSEPART_NUMBER  Integer specifying the course part for which we generate
                     teams and users.

options:
  -h, --help         show this help message and exit
```

### create-domjudge-credentials-mails

```console
$ create-domjudge-credentials-mails -h
usage: create-domjudge-credentials-mails [-h]
                                         ACCOUNTS_YAML_FILE TEAMS_YAML_FILE
                                         MAIL_FOLDER FROM_EMAILADDRESS
                                         FROM_FULL_NAME

from DOMjudge import files accounts.yaml and teams.yaml we generate credential
emails in a local mail/ directory

positional arguments:
  ACCOUNTS_YAML_FILE  YAML file with user data for importing users into
                      DOMjudge
  TEAMS_YAML_FILE     YAML file with team data for importing teams into
                      DOMjudge
  MAIL_FOLDER         folder in which the mails are generated. This folder is
                      created for you, and should not already exist.
  FROM_EMAILADDRESS   Email address used to send the generated emails.
  FROM_FULL_NAME      Full name of person which sends the generated emails.
                      (person of FROM_EMAILADDRESS)

options:
  -h, --help          show this help message and exit
```

### send-emails

```console
$ send-emails  -h

USAGE:
  send-emails FROM_EMAIL DIR_CONTAINING_EMAILS [SLEEP_BETWEEN_SENDING_EMAILS]

PARAMS:
  FROM_EMAIL: email address used as sender
  DIR_CONTAINING_EMAILS: the .eml files in this directory get send
  SLEEP_BETWEEN_SENDING_EMAILS: sleep between sending a previous and next email.
                                By default this value is 6 in seconds.

DESCRIPTION:
  The send-emails script allows you to first setup a folder containing emails
  in .eml files, after which you can send them out automatically with the
  send-emails script. If each email has a single recepient, then it is a
  good convention to set the basename of the email file to the recepients
  email address.

  Some mail servers do not allow more then X emails to be send per minute.
  To prevent you email account to be blocked, we sleep between sending emails.
  We set the default to limit sending a maximum of 10 emails per minute.
  This is a safe limit, but note that to send 100 emails this will take 10 minutes.
```

### submit

```console
$ submit -h
usage: submit [--version] [-h] [-c CONTEST] [-p PROBLEM] [-l LANGUAGE]
              [-e ENTRY_POINT] [-v [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]] [-q]
              [-y] [-u URL] [-a AUTH]
              [filename ...]

Submit a solution for a problem.

positional arguments:
  filename              filename(s) to submit

options:
  --version             output version information and exit
  -h, --help            display this help and exit
  -c CONTEST, --contest CONTEST
                        submit for contest with ID or short name CONTEST.
                            Defaults to the value of the
                            environment variable 'SUBMITCONTEST'.
                            Mandatory when more than one contest is active.
  -p PROBLEM, --problem PROBLEM
                        submit for problem with ID or label PROBLEM
  -l LANGUAGE, --language LANGUAGE
                        submit in language with ID LANGUAGE
  -e ENTRY_POINT, --entry_point ENTRY_POINT
                        set an explicit entry_point, e.g. the java main class
  -v [{DEBUG,INFO,WARNING,ERROR,CRITICAL}], --verbose [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]
                        increase verbosity
  -q, --quiet           suppress warning/info messages
  -y, --assume-yes      suppress user input and assume yes
  -u URL, --url URL     submit to server with base address URL
                            (should not be necessary for normal use)
  -a AUTH, --auth AUTH  user:password value to authenticate with basic auth
```

### fetch

```console
$ fetch -h
usage: fetch [--version] [-h] [-c CONTEST] [-p PROBLEM]
             [-v [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]] [-q] [-l] [-f]
             [-u URL] [-o OUTPUTFILE] [-a AUTH]

Fetch students final solutions for a problem in a contest into a data file.

options:
  --version             output version information and exit
  -h, --help            display this help and exit
  -c CONTEST, --contest CONTEST
                        fetch from contest with ID or short name CONTEST.
                            Defaults to the value of the
                            environment variable 'SUBMITCONTEST'.
                            Mandatory when more than one contest is active.
  -p PROBLEM, --problem PROBLEM
                        fetch from problem with ID or label PROBLEM
  -v [{DEBUG,INFO,WARNING,ERROR,CRITICAL}], --verbose [{DEBUG,INFO,WARNING,ERROR,CRITICAL}]
                        increase verbosity
  -q, --quiet           suppress warning/info messages
  -l, --last-per-team   only fetch the last submission per team
  -f, --files           also fetch the files belonging to a team submission as a zipfile
  -u URL, --url URL     submit to server with base address URL
                            (should not be necessary for normal use)
  -o OUTPUTFILE, --outputfile OUTPUTFILE
                        output file where fetched data is stored. Default: contest_problem_data.pod
  -a AUTH, --auth AUTH  user:password value to authenticate with basic auth
```

### process

```console
$ process -h
usage: process [--version] [-h] [--skip SKIP] [datafile]

Process data file with final solutions for a problem in a DOMjudge contest into a table in a csv file

positional arguments:
  datafile     datafile we got from fetch command. Default: contest_problem_data.pod

options:
  --version    output version information and exit
  -h, --help   display this help and exit
  --skip SKIP  number of public samples we have to skip, because we only evaluated on secret samples
```

### csv2xslt

```console
$ csv2xslt -h
usage: csv2xslt FILE.csv
csv2xslt converts a csv file to a nice excel file containing a table
```
