# Create problem for the course with BACPtools

<!--ts-->
<!-- prettier-ignore -->
   * [Overview](#overview)
   * [Create a new problem](#create-a-new-problem)
   * [Edit your problem](#edit-your-problem)
   * [Generate samples for your problem](#generate-samples-for-your-problem)
   * [Test your problem](#test-your-problem)
   * [Create a zip file](#create-a-zip-file)
<!--te-->

## Overview

We use [BACPtools](https://github.com/RagnarGrootKoerkamp/BAPCtools) to develop a
problem to be used in DOMjudge.

In DOMJudge we will import this problem twice:

1. One version **for grading** containing both the public and secret samples. The
   judging is set to be done **none-lazy**, meaning all samples are evaluated. This
   folder is the leading problem folder.
2. One version **for practicing** which is a copy where we removed all secret samples
   in the 'data/secret/' folder. The judging is set to be done **lazy**.

So when developing the problem folder we will first focus on the **grading** leading
problem folder. Later we will strip this to create the **practicing** problem.

## Create a new problem

Use BACPtools to create a problem:

      bt new_problem  grading_mynewproblem

which after several questions generates a new problem in the new subfolder
`grading_mynewproblem/` with the problem name `grading_mynewproblem` set in
`grading_mynewproblem/problem.yaml`.

It is **important** to prefix your problem name with `grading_` because this folder
will be the leading problem folder which we will use for grading. For practicing we
make a copy from which we strip the secret samples.

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

where the `problem.yaml` file contains the metadata about the project, and the
`.gitkeep` files are only added to allow you to push an empty folder to git.  
BACPtools generates the problem as described by
[the official icpc standard for the problem format](https://icpc.io/problem-package-format/spec/legacy-icpc.html).
BAPCtools extends the standard by adding a `.timelimit` file and supporting also an
answer validator. By default it doesn't add an output validator because the default
diff-based output validator suffices for most cases. See 'Edit your problem' below
for more details.

BACPtools generates a file `.timelimit` containing an integer specifying the
limelimit of the problem in seconds (default:1). The official problem package format
does not support this file, but also does not specify how to specify a timelimit.
[DOMjudge supports specifying a timelimit](https://www.domjudge.org/docs/manual/main/problem-format.html)
in its own specific `domjudge-problem.ini` file. BACPtools does also support this
file, but has deprecated it. DOMjudge does also support the `.timelimit` file when
importing a problem zipfile. Because we use BACPtools to create a problem we will use
the `.timelimit` file! We won't use a `domjudge-problem.ini` file, only a
`problem.yaml` file.

## Edit your problem

When creating a new problem it is always nice to have a good example to look at. I
recommend to look at the example at the BAPCtools
[different problem example](https://github.com/RagnarGrootKoerkamp/BAPCtools/tree/master/test/problems/different/).
The different problem provides the input and output samples statically. For an
example where a generator is used look at the BAPCtools
[identity problem example](https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/test/problems/identity/).

Steps to follow to define your new problem:

- add `problem_statement/problem.en.tex` to describe your problem in English. When
  exporting the problem as a zipfile it will compile this `tex` file to the file
  `problem.pdf` at the root of the zipfile.
- in `submissions/accepted/` folder put good solutions to problem
- add a generator,<br> A generator generates test samples for a problem. In the
  generator config some inputs are choosen, and using a solution the answers are
  calculated. The generator then delivers these inputs with there answers in .in
  files and .ans files in the `data/` folder. See documentation:
  https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/generators.md . An
  example generator you can find at
  https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/test/problems/identity/generators/generators.yaml
- optionally add an input and answer validator, <br> These validators validate
  respectively the `.in` or `.ans` file for every test case, typically for syntax,
  format, and range. They are a safety check on you sample files format and syntax.
  See documentation:
  https://github.com/RagnarGrootKoerkamp/BAPCtools/blob/master/doc/validation.md . A
  good example problem with an input and answer valicator you can find at
  https://github.com/RagnarGrootKoerkamp/BAPCtools/tree/master/test/problems/different/

  **Note:** The official problem package format does not specify an answer validator.
  The answer validator is a BACPtools specific extension.

- use the default output validator,<br> The output validator checks whether for a
  given input the output of a solution is correct. <br> In most cases we do not need
  an specific output validator and can we use the default output validator. The
  [default output validator](https://icpc.io/problem-package-format/spec/2023-07-draft.html#default-output-validator-specification)
  is essentially a beefed-up diff on a file with the expect output(`.ans` file), and
  a file containing the submission's output. In its default mode, it tokenizes the
  files to compare and compares them token by token.

## Generate samples for your problem

Generate samples for your problem with:

    bt generate

The generator generates input and answer files, validates them with an input and
answer validator.

## Test your problem

Test your problem with:

    bt run  --no-generate  --verbose  --overview accepted

We apply each solution in the `submissions/accepted/` folder on the generated input
files, and validates its output is the same as the generated answer, finally
displaying an overview in single line as shown in the DOMjudge website for a
submission.

If your accepted submission does not pass within the timelimit then increase its
value in the `.timelimit` file. Otherwise if it passes to easily you can decrease it.
In this way you can tune your problem.

Let us explain the command in more detail:

- The `accepted` argument makes `bt` only validate solutions in the
  `submissions/accepted/` folder.
- The `--no-generate` option makes `bt` to skip the `generate` step, which is not
  needed because we already generated our sample data in the previous step.
- The `--verbose` option makes `bt` to display a line per test case, and causes it to
  not stop if a test case fails, but evaluates them all: greedy or none-lazy
  evaluation.
- The `--overview` option make `bt` to display a per solution a single line result
  where the line contains per testcase a letter displaying its evaluation result.
  Lowercase letters are used for the public samples, and uppercase letters for secret
  test cases. This is almost the same line as shown in the DOMjudge website. In the
  website icons are used instead of letters, and the public and private samples are
  separated with a bar.

**Note:** in above command we only test the `accepted` solutions. We can also test
how the not accepted solutions in the other subfolders of the `submissions/` fail as
we would expect.

## Create a zip file

If everything is fine can create a zipfile of the problem with

      bt zip -f

The `-f` option makes `bt zip` skip validation of input and answers. This is already
done in the previous steps.

Now you can import this problem zipfile into DOMjudge. For details how we exactly do
this see next section.

On import DOMjudge creates a problem:

- with "name" from `problem.yaml`
- with "problem text" set to `problem.pdf`
- and `.in `and `.ans` files from the data folder (both `answers/` and `secrets/`)

All other data in the zipfile is not imported into the DOMjudge problem.

**IMPORTANT**:

- Always keep the local BACPtools problem folder, because using that we can easily
  change and test the problem before exporting to DOMjudge. This folder is the
  leading folder in this process!

- Add the submission/deadline date, before which a final solution of the problem must
  be submitted, in the problem description in `problem.pdf` contains.

- The **name** of the problem is on import to DOMjudge set to value of the `name`
  field in `problem.yaml`. The basename of the zipfile is used as the problem
  **shortname**. See https://www.domjudge.org/snapshot/manual/problem-format.html).
  The command `bt zip -f` creates a zipfile with its basename set to directory name
  of the problem. Using the directory name and the basename of a zipfile is a very
  implicit way of configuring the shortname of the problem. I rather would have
  preferred that there was a special shortname field in the `problem.yaml` config
  file. <br> **KISS**: To prevent any confusion I advise to always set the (long)name
  and shortname of the problem the same. That is use the same name for the problem's
  directory, and its derivated zipfile [setting the problem's shortname], as for the
  name field in the `problem.yaml` config file [setting the problem's (long)name].

- It is advised to not import the problem to a contest directly. The `submissions/`
  folder is never imported to the problem. But, the `submissions/` folder in the
  zipfile is handled specially if the user account doing the import has **admin
  role** , **belongs to a team** and **imports the problem to a contest directly**.
  Then the `submissions/` folder will be submitted to the newly created problem to be
  judged. This can be convenient to directly evaluate that everything also works the
  same on the DOMjudge server as on your local machine. However as we describe below
  in the 'Test problem in DOMjudge' section I prefer to do the testing separately in
  a separate test contest, because then testing submissions are then not included in
  the student contests, so we then later do not have to filter out test submissions
  when processing the student results.
