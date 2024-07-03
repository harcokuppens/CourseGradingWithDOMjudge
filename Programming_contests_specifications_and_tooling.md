

# Overview programming contests, specifications and tooling

**Table of Contents**
<!--ts-->
* [Overview programming contests, specifications and tooling](Programming_contests_specifications_and_tooling.md#overview-programming-contests-specifications-and-tooling)
   * [The ICPC](Programming_contests_specifications_and_tooling.md#the-icpc)
      * [Examples of ICPC contests](Programming_contests_specifications_and_tooling.md#examples-of-icpc-contests)
   * [ICPC Specifications](Programming_contests_specifications_and_tooling.md#icpc-specifications)
   * [Contest Control Systems   (following ICPC specs)](Programming_contests_specifications_and_tooling.md#contest-control-systems---following-icpc-specs)
      * [Kattis](Programming_contests_specifications_and_tooling.md#kattis)
      * [DOMjudge](Programming_contests_specifications_and_tooling.md#domjudge)
   * [Tools to make problem/contest data](Programming_contests_specifications_and_tooling.md#tools-to-make-problemcontest-data)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: harcok, at: wo jul  3 12:02:44 CEST 2024 -->

<!--te-->

<br>

## The ICPC


The International Collegiate Programming Contest, known as the ICPC, is an 
annual multi-tiered competitive programming competition among the universities of the world.
   
References:

* https://icpc.global 
* https://en.wikipedia.org/wiki/International_Collegiate_Programming_Contest




### Examples of ICPC contests
 
 
 The DOMjudge tool has been used at several ICPC contests.<Br>
 Source https://www.domjudge.org/about :
        
        DOMjudge has been used in many live contests, a selection:

          * ICPC Sub-regionals: BAPC (since 2004), GCPC (since 2010), UKIEPC (since 2015)
                                  `-> Benelux: http://www.bapc.eu/  -> https://2023.bapc.eu/ contains examples of problems and their solutions
          * ICPC Regionals: NWERC (since 2007), SWERC (since 2008), SER USA (since 2010), SOCAL (since
            2013), RMRC (since 2014), South Pacific Regional
          * ICPC Championship: NAC (in 2023)
          * ICPC World Finals (since 2012, and since 2018 as main system)

 
## ICPC Specifications
 
* Contest Control System (CCS) Specifications<br>https://ccs-specs.icpc.io
      -  https://ccs-specs.icpc.io/draft/ccs_system_requirements<br>
          This document specifies requirements for operation, verification and validation of Programming Contest Control System
      - https://ccs-specs.icpc.io/draft/contest_api  (**REST API**)<br>
           This page describes an API for accessing information provided by a Contest Control System (CCS) or Contest Data Server.
      - https://ccs-specs.icpc.io/draft/contest_package <br>This page describes the format of a contest package. It describes how to store the information available through the Contest API on disk.
              
* Problem format: <br>https://icpc.io/problem-package-format/
    - https://icpc.io/problem-package-format/spec/2023-07-draft.html or ttps://www.kattis.com/problem-package-format/spec/2023-07-draft.html 
    - https://icpc.io/problem-package-format/examples/directory_structure.html      
   

## Contest Control Systems   (following ICPC specs)
  
I found two Contest Control Systems DOMjudge and Kattis.
  
  - both are automated judge systems to run programming contests. 
  - Kattis seems to develop the latest specification for the problem-package-format (https://icpc.io/problem-package-format/)
    Kattis says Development happens in the GitHub repository:
               https://github.com/Kattis/problem-package-format
    
    
### Kattis

 https://www.kattis.com/universities is a commercial tool


### DOMjudge
   
https://domjudge.org is an open source tool
               
        https://www.domjudge.org/about

             DOMjudge is an automated judge system to run programming contests. It has a mechanism to submit problem solutions,
             have them judged fully automatically and provides (web)interfaces for teams, the jury and the general public.
                                                                                 
             DOMjudge is primarily focused to be used in programming contests like the ICPC programming contests, where teams
             are on-site and there is a fixed problem set and time frame. It can however also be adapted to other contexts.

             The system scales well: distributed judging is easy and it has a modular system for plugging in languages or
             compilers. The team interface is kept simple and efficient, while the administrator's view has many features,
             including rejudging, clarifications, detailed submission/judging information. There's a REST API to extend
             DOMjudge in any direction you need.

             It is free and open source software, making it easy to adapt it to your needs.


         REST api's linked from DOMjudge jury interface:  
           DOMjudge API
              http://localhost:12345/api/doc      
           ICPC contest API 
             https://ccs-specs.icpc.io/2021-11/contest_api 
    
            
## Tools to make problem/contest data
  
  * https://github.com/Kattis/problemtools
      -> linked from  https://github.com/Kattis/problem-package-format
 
 
        verifyproblem

         Verifyproblem will do something like the following:

           * check that the problem statement can be compiled (from LaTeX)
           * check that there's at least one input format validator and one accepted submission
           * run input format validators on all inputs
           * run all submissions (accepted and otherwise) on the inputs and check that the results match the expectation
           * run any output validators if they exist on output produced by the submissions
           * compute time limits based on a time multiplier times the slowest accepted submission
        
 
  * https://github.com/RagnarGrootKoerkamp/BAPCtools/
     docs: https://github.com/RagnarGrootKoerkamp/BAPCtools/tree/master/doc
     
        https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/abstract.md 
           BAPCtools - software for streamlining problem development

            Over the years, BAPCtools has grown from a collection of scripts used in the BAPC (Benelux Algorithm Programming
            Contest) jury to a full blown command line application for problem development. It targets the CLICS problem
            package format as used by DOMjudge and Kattis, and is now also used by the NWERC jury. Good user experience and
            convenience of use, including an intuitive command line interface and clear output, are explicit goals.

            In this talk, I will develop a new problem from scratch and show some of BAPCtools' features, like:

              * automatically judging submissions to a problem,
              * a C++ library for input/output validation,
              * a new specification for generating test cases reproducibly,
              * fuzz testing submissions.

      Note:  BACPtools adds 'answer_validator' to problem, which is not in the official https://icpc.io/problem-package-format/ spec!   
      See: https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/validation.md 
         
