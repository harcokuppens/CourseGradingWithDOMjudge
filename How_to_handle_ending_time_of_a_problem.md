

# How to handle ending time of a problem?

**Table of Contents**
<!--ts-->
* [How to handle ending time of a problem?](#how-to-handle-ending-time-of-a-problem)
   * [Domjudge settings controlling ending time of a problem](#domjudge-settings-controlling-ending-time-of-a-problem)
      * [Per contest](#per-contest)
      * [Per problem](#per-problem)
   * [Brainstorm: how to use these settings to apply and ending time for a problem](#brainstorm-how-to-use-these-settings-to-apply-and-ending-time-for-a-problem)
      * [Method 1: create a contest per problem to have the end time of the contest apply for the problem](#method-1-create-a-contest-per-problem-to-have-the-end-time-of-the-contest-apply-for-the-problem)
      * [Method 2: manually enforce end time per problem](#method-2-manually-enforce-end-time-per-problem)
   * [Conclusion](#conclusion)
<!--te-->


## Domjudge settings controlling ending time of a problem 

DOMjudge allows some settings per contest, and some per problem.

### Per contest

 
  From the contest edit form in DOMjudge we find the
  following documentation: 

        Start time
          Absolute time when the contest starts.

        End time
          Time when the contest ends. 
  
          Submissions made after this time will be accepted and judged 
          but shown (to teams and public) as 'too-late' and 
          not counted towards the score.
   

        Activate time
          Time when the contest becomes visible for teams. 
          Must be in the past to enable submission of jury submissions.

        Deactivate time
          Time when the contest and scoreboard are hidden again. Usually a few hours/days after the contest ends.
          

        Enable contest
          When disabled, the contest is hidden from teams (even when active) and judging is disabled. 
          Disabling is a quick way to remove access to it without changing any other settings.

        Allow submit
          When disabled, users cannot submit to the contest and a warning will be displayed.
   


So we can conclude from this:
 
 * **activate/deactivate**: is about **WHEN** the contest is  **VISIBILE**.
   
 * **start/end time**: is about **WHEN** teams **MAY SUBMIT** to contest
      
 * **Allow submit**:  just **DISABLES SUBMITTING DIRECTLY** with a warning shown to the teams that submission is disabled.
                    
 * **Enable contest**:  just **HIDES CONTEST DIRECTLY**

    
The main problem with disabling per contest is that it disables directly for ALL problems!!

A more fine grained approach is disable per problem instead of contest.
   
### Per problem 

We can edit settings per problem in DOMjudge in the edit form of the contest  at problem listing at the bottom of the edit form.

Per problem we have two options:

 * **disable submissions** to the problem.<br> DOMjudge then also makes the problem **invisible** for all teams.
 
 * **disable judging** of submissions to the problem. <br>Teams can still submit to a problem, but the submission status stays 'pending'. <br>If you look as admin to the submissions it says 'Queued' and under the submissions details it says 'Judging is not ready yet!'  and 'Run not started/finished yet.'


Disable judging to a problem is confusing for the students, because they still can submit and 
the submission is displayed as 'Pending'. 
When disabling submissions to a problem, the students just cannot submit anymore. Much clearer!

Reference documentation from:
  https://www.domjudge.org/docs/manual/8.2/problem-format.html
     
            allow_submit - whether to allow submissions to this problem, 
                           disabling this also makes the problem invisible to teams and public
            
            allow_judge - whether to allow judging of this problem



        
 
## Brainstorm: how to use these settings to apply and ending time for a problem

### Method 1: create a contest per problem to have the end time of the contest apply for the problem

If you really want to control **end time** per problem in DOMjudge,  we could make a separate contest per problem.
However then teams can still submit after the endtime of the contest, which still gets judge in background. 
The team sees 'to late', but for download script you need to discard all submits after endtime. 
There are some advantages and disadvantages of this approach.

* **Advantage:** we have an end time per problem in DOMjudge with countdown timer.
* **Disadvantage:** for each problem you need to setup a contest, which is extra work. It is also confusing that you still can submit, even if you get result to late!<br>
  You can disable submit manually to fix this, but this is extra manual work which you wanted to prevent by using an endtime. Note, that you should not deactivate with time, but then it becomes hidden!

Above approach gives a nice ending time per problem, but it also has practical disadvantages.     
     
### Method 2: manually enforce end time per problem

Another simpler approach is that we just don't care about handling end times in DOMjudge. We can just communicate an endtime to the students. And if time ended for a problem, we then just disable submitting to "grading" version of the problem, after which we download results. The "grading" version of the problem becomes invisible, but the "practicing" version is still there.
       
We could even disable submission to both "grading" and "practicing" version of a problem. Students then cannot look back. However it is nicer if students can see overview of their own scores of the practicing problems, so it is better choice to only disallow submitting to  "grading" version of the problem, so you can still have an overview of the practicing results to all problems.
       
By disabling submitting per problem we can still have several problems in a contest per course part.
We then only need a different contest in the course if we change the arrangement of students over teams.
We then have clear parts in DOMjudge how students cooperated.
    
## Conclusion
       
Above brainstorm leads us to take a simple approach:
     
 1. Don't use explicit endtime for problem in system, but just communicate an endtime  for a problem to the students.
  If the endtime of problem is passed the teacher ends the problem manually by: 

    1. disable judging of grading version of problem. 
        This makes the grading problem invisible for teams, and they cannot submit to it anymore!
        The teams can still see the none-grading 'practicing' problem.  Note that they still can submit to the 'practicing' problem, but they probably won't.
    2. use download script to download results from grading script.
      
     
 2. If a contest is done, we can disable submitting to the contest: the practicing problems stay visible, but you cannot submit to it anymore. This prevents people submitting to old problems, but let them still see them. This allows students to look back.     
        
 3. If course is done, you can disable all contests: then all contests are hidden. Teams can login but do not see anything anymore. Course is over, students do not need to look back anymore.
       

Every year start DOMjudge fresh! Don't use a system with old teams and old contests, because this
could only lead to unnecessary conflicts and confusion. 
