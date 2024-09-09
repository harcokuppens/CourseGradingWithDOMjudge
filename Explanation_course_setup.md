# Explanation of How to Use DOMjudge for a Course

**Table of Contents**
<!--ts-->
* [Explanation of How to Use DOMjudge for a Course](#explanation-of-how-to-use-domjudge-for-a-course)
   * [Requirements](#requirements)
   * [Implementation of requirements](#implementation-of-requirements)
      * [Point 1: students should not be able to see each other results](#point-1-students-should-not-be-able-to-see-each-other-results)
      * [Point 2: submit for practicing and grading separately](#point-2-submit-for-practicing-and-grading-separately)
      * [Point 3: support different teams configurations](#point-3-support-different-teams-configurations)
      * [Point 4: easy fetch grading results](#point-4-easy-fetch-grading-results)
      * [Point 5:  stop submissions to a problem when the deadline expires](#point-5--stop-submissions-to-a-problem-when-the-deadline-expires)
      * [Point 6: the DOMjudge system should be easy installable and upgradable](#point-6-the-domjudge-system-should-be-easy-installable-and-upgradable)
      * [Point 7: data should be easily being backed up for long term storage](#point-7-data-should-be-easily-being-backed-up-for-long-term-storage)
<!--te-->


## Requirements
Using DOMjudge for course has some different requirements:

 1. students should not be able to see each other results
    this means no scoreboard
 2. students need a way to submit trial versions of their solution for practicing, 
    and a final solution for grading
 3. students can work together in teams, where during the course the teams can change 
 4. teachers should be able to stop submissions to a problem when the deadline expires
 5. teachers should be able to easily fetch the final solutions with grading from 
    DOMjudge 
 6. the DOMjudge system should be easy installable and upgradable,
    because teachers do not have much time for system administration.
 7. data should be easily being backed up for long term storage
 
Below we will tackle these requirements point by point.
 
## Implementation of requirements
 
### Point 1: students should not be able to see each other results

We can tackle by assigning students to teams with an invisible team category. 
DOMjudge supports team categories.  Each team is assigned a specific team category. 
All teams having the same category inherit the properties of this team category.  
By setting the 'visible' property of a team category to "off" we can make the 
teams invisible on the public team scoreboard. So we effectively disable the
scoreboard for all teams in this category, making the teams invisible for each other.
A team can only see its own results, and not that of any other team.

### Point 2: submit for practicing and grading separately

This point can be easily be arranged by providing a problem in two variants to the contest.
The first variant is a practicing one, where in the problem we only have samples of
inputs and their corresponding outputs. The students can see and download these samples. 
With these  samples the students can evaluate their submitted code for practicing purpose.
The problem is lazy evaluated, meaning that on the first sample it fails it stops. All remaining
samples won't be run, this to prevent overloading the domjudge server. Students of course still
can download the samples and test their submission locally on all of them.
 
The second variant is for grading. The problem from the first variant is extended with secret inputs 
and their corresponding outputs which are not visible by the students, but only by the domjudge admin.
When students have a final result they can submit it to this problem for grading. 
This problem will be judge none-lazily meaning that all samples will be run whether they fail or not.
Looking at the evaluation of the samples the student can still see if he submitted the right code. 
If something went wrong he can resubmit. Only the latest submission will be used for grading.
The grading will be done on the secret samples which will also be automatically evaluated, but their
evaluation is only visible for the domjudge admin. Hence the public samples are for the students to
verify that they submitted the right solution, and the secret samples are for the teacher to grade
the student's solution.

### Point 3: support different teams configurations 


In DOMjudge we implement this by dividing the course in different parts, where each part is only accessible by a specific teams configuration:
 
 1.  divide the course in parts by creating a separate *contest* per part
 2.  make each *contest* only accessible by specific *team category* representing a teams configuration
 3.  we implement a specific *team category*  by assigning only the teams in a specific teams configuration to this  *team category*  

Thus, a team can only access the contest that its *team category* can access. Additionally, by setting the *invisible* property on the *team category*, teams cannot see each other.

### Point 4: easy fetch grading results

We use a fetch script to automatically fetch the data from DOMjudge. Then we use another script to process the data to present the results nicely in an excel stylesheet.

### Point 5:  stop submissions to a problem when the deadline expires
  
Each problem has two instances a `practice` problem, and a `grading` problem. We disable submitting to the `grading` problem when its deadline expires. In DOMjudge disable submitting to a problem causes also that the problem becomes invisible for the teams, making any new submissions to it impossible. The students cannot see `grading` problem anymore, however they can still see the `practice` problem containing there results.  In this way after the deadline the `grading` problems disappears, so that students **cannot submit for grading anymore**. But all contests and its `practice` problems are always kept visible for the students,  and therefore they **keep having an overview of the work they did**. 

If all problems in a course part, represented by a contest, are graded then we can also disable submitting to contest as a whole. Then also submissions to the `practice` problems in the contest become disabled.

Finally if the whole course is ended, we can disable all the contests in the course, making everything disabled. From then on a team sees no contests at all anymore. This is a quick method to disable the course, and still allow us to activate parts of it if really necessary.

For more details see [How to handle ending time of a problem](How_to_handle_ending_time_of_a_problem.md).


### Point 6: the DOMjudge system should be easy installable and upgradable

  A new installation can be easily be done using by unning DOMjudge in docker by following this quickstart.
    https://github.com/harcokuppens/DOMjudgeDockerCompose/blob/main/README.md#quick-start


### Point 7: data should be easily being backed up for long term storage

At the end off the course, if all resits are done,  we do a combined **backup**, **reset**, and **update** of DOMjudge:

   1. `docker compose down`
   2. `sudo cp -a . $BACKUPFOLDER`
   3. `sudo rm -r ./data`
   4. Optionally update to a newer domjudge version by updating `DOMJUDGE_VERSION/MARIADB_VERSION` in `.env` file,
      which can be also done by `git pull` if the versions in the github repo are updated.
   6. `docker compose up -d`

DOMjudge is now ready for next years course, and the new admin password can be read from: `./data/passwords/admin.pw`.



