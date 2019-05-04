# DCgit
Scripts to be used by students and instructors to manage assignments through GitHub.

Typical Instructor Usage:
  * Create an organization for the course.
  * Fork this repository into the organization to create a new repository for the specific course.
  * Update the configuration file in your fork to reflect the relevant information for the specific course.
  * Add repositories to the organization for each assignment.
    * See the assignment template repositories provided.
  * The scripts in the InstructorScripts repository can be used to pull/push all student work on a given assignment for grading purposes.

Typical Student Usage:
  * Create a GitHub account.
  * Use git to clone the course specific fork of this repository to their local machine (they do not need to fork it)
    * If they work on multiple machines they will need to do this on each one.
  * Run __DCgitSetup__ once to complete the configuration
    * If they work on multiple machines they will need to do this on each one.
  * Run __DCgitBegin__ once at the start of each assignment to get the assignment starter materials.
  * Run __DCgitPull__ at the start of each work session to pull the latest work from their GitHub to their local machine.
  * Work on the assignment.
  * Run __DCgitPush__ at the end of each work session to push the latest work from the local machine to their GitHub.

Below are the details on each of the files in repository.

* __DCgitSetup__ _CourseID_
  * Run once for each course on each machine that the student uses to setup the common configuration parameters.

  * Parameters:
    * None

  * Behavior:
    * Updates student specific information within the configuration file.
    * Performs a basic git configuration.

* __DCgitBegin__ _AssignmentID_
  * Run once at the beginning of an assignment to produce an editable version of the assignment that is available to the student.

  * Parameters:
    * _AssignmentID_ : The identifier of the assignment repository in the course organization on GitHub.

  * Error Conditions:
    * If the configuration file has not been updated the script terminates with a suggestion to be sure that _DCgitSetup_ is run prior to use of the other scripts.
    * If the repository does not exist in the course organization the script terminates with a suggestion to check the _AssignmentID_.
    * If the repository already exists in the student's GitHub then the script terminates indicating that the assignment has already been begun and that _DCgitPull_ may be the intended action.
    * If current working directory on the local machine is has the same name as _AssignmentID_ the script terminates indicating that the assignment has already been begun and that _DCgitPull_ may be the intended action.
    * If the current working directory has a sub-directory with the same name as _AssignmentID_ the script terminates indicating that the assignment has already been begun and that changing into the directory and using _DCgitPull_ may be the intended action.

  * Behavior:
    * The student is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * The repository is copied from the course organization into the student's GitHub as a private repository.
      * _NOTE:_ The repository is copied rather than being forked because it will be private.
    * The instructor for the course is added as a collaborator on the private repository.
    * The repository for the assignment is cloned to the student's local machine.

* __DCgitPartnerBegin__ _AssignmentID_ _PartnerGitHubID_
  * Run by the student that did not execute __DCgitBegin__ to be given access to the partner's private repository so that it can be worked on collaboratively.  A stub repository is also created in the student's GitHub to which the instructor will push the final graded work so that both partners own a copy.

  * Parameters:
    * _AssignmentID_ : The identifier of the assignment repository in the partner's GitHub.
    * _PartnerGitHubID_ : The GitHub username of the partner for the assignment.

  * Error Conditions:
    * If the configuration file has not been updated the script terminates with a suggestion to be sure that _DCgitSetup_ is run prior to use of the other scripts.
    * If the assignment exists in the student's GitHub the script terminates with a message indicating that the partner that did not run _DCgitBegin_ should run this script. It also suggests that if both have run _DCgitBegin_ that one will need to run _DCgitExpunge_  and then rerun this script.
    * If the _PartnerGitHubID_ does not exist on GitHub then the script terminates with a suggestion to check the _PartnerGitHubID_.

  * Behavior
    * The student is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * The partner is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * The student is added as a collaborator on the partner's existing private repository.
      * If the _AssignmentID_ does not exist in the partner's GitHub the script terminates with a message to check the _AssignmentID_, the _PartnerGitHubID_ and to ensure that the partner has run _DCgitBegin_.
    * The invitation to collaborate on the partner's repository will be accepted.
    * The repository will be cloned to the student's local machine.
    * A bare repository for _AssignmentID_ will be created in the student's GitHub so that the instructor can later push the graded work to it.
    * The instructor is added as a collaborator on the bare repository.

* __DCgitPull__ [ ForceLocal | ForceRemote | Merge ]
  * Run within the assignment repository at the beginning of each work session to pull the most recent version of the assignment down from GitHub to the student's local machine.

  * Parameters:
    * [ ForceLocal | ForceRemote | Merge ] : Force merge conflicts to be resolved in favor of the local version, the version on the student's GitHub, or to launch a merge tool that will allow the conflicts to be resolved.

  * Error Conditions:
    * If the configuration file has not been updated the script terminates with a suggestion to be sure that _DCgitSetup_ is run prior to use of the other scripts.
    * If not run within an assignment repository the script terminates and suggests changing into an assignment directory and rerunning.
    * If the origin repository does not exist on GitHub the script terminates, explaining that the assignment has been removed from GitHub and that help should be sought to resolve the issue.

  * Behavior:
    * The student is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * Pull the contents of the repository from the appropriate remote.
      * If there are merge conflicts the script will terminate with a message suggesting the use of one of the _[ ForceLocal | ForceRemote | Merge ]_ flags.

* __DCgitPush__ [ ForceLocal ]
  * Run within the assignment repository at the end of each work session to push the current version of the assignment from the student's local machine up to the origin on GitHub.

  * Parameters:
    * [ ForceLocal ] : Forces the repository on GitHub to look identical to the version on the local machine.

  * Error Conditions:
    * If the configuration file has not been updated the script terminates with a suggestion to be sure that _DCgitSetup_ is run prior to use of the other scripts.
    * If not run within an assignment repository the script terminates and suggests changing into an assignment directory and rerunning.
    * If the remote repository does not exist on GitHub the script fails, explaining that the assignment has been removed from GitHub and that help should be sought to resolve the issue.

  * Behavior:
    * The student is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * All of the changes to the local repository are committed using the date/time and local username as the commit message.
    * The local repository is pushed from the local machine to the origin on GitHub.
      * If there are merge conflicts the script terminates and a suggestion is made that either a _DCgitPull_ be done to resolve the conflict or that the [ ForceLocal ] flag be used.

* __DCgitExpunge__ _AssignmentID_
  * Run from within an assignment repository to remove an assignment completely from the local machine and the student's GitHub.  This is typically used when something goes horribly wrong and they want to start over from scratch.

  * Parameters:
    * _AssignmentID_ : The identifier of the repository to be expunged.

  * Error Conditions:
    * If the configuration file has not been updated the script terminates with a suggestion to be sure that _DCgitSetup_ is run prior to use of the other scripts.
    * If not run within an assignment repository that agrees with the provided _AssignmentID_ the script terminates and suggests changing into an assignment directory and rerunning.

  * Behavior:
    * The student is asked to type the _AssignmentID_ to confirm that it should be expunged, or anything else to terminate the operation.
    * The student is prompted for their GitHub password.
      * If the password is not valid the script terminates with a message saying the password was incorrect and to try again.
    * The repository on the student's GitHub is removed.
      * _NOTE:_ This will not remove any repositories from a partner's GitHub (either bare or with the graded results).
    * The local repository is removed.

_NOTE_: These scripts have been produced for the specific use cases involved in our course.  They are not designed to handle every use case. Nor are they designed to prohibit sufficiently motivated and unethical students from working around the basic restrictions that they impose.  The code has been released under a Creative Commons License (see below) so please feel free to adapt to your purposes. Pull requests that handle additional use cases or improve the robustness of the scripts in a sufficiently general way that does not impact our specific use cases are welcome and will be considered on a case-by-case basis.

___
![Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png "Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License")
###### All textual materials used in Dickinson College COMP 130 are licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-nc/4.0/)

###### All executable code used in Dickinson College COMP 130 is licenced under the [GNU General Public Licence Version 3](https://www.gnu.org/licenses/gpl.txt)
