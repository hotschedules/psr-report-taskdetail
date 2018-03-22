# psr-jenkins-setup


This is the template repo for all psr jenkins jobs. It describes the steps
required to set up a psr test to be initiated using a Jenkins job instead 
of the Flood.io UI.

###How to Jenkinize<sup>TM</sup> a PSR Test

**SET UP A NEW PSR REPO (skip to the next section if you already have a repo)**

1. Fork this repo **(do not clone)** and name the fork after your PSR test 
e.g. "psr-platform" or "psr-authz". In this example we will call the fork 
"psr-yourtest".

2. Clone down psr-yourtest to your machine.

3. Edit the package.json file. It contains properties that your Jenkins job 
needs. Necessary changes are commented in the package.json file

4. Edit scripts/jenkins.sh. 
   1. Edit the list of files you want to upload to Flood.io 
e.g. your .jmx file and your psrCSV.txt file. 
   2. Edit the "flood[project]" value to select the Flood project where your 
 test should be stored (e.g. Platform or Authz). 
   3. Edit "flood[name]" to name the test in Flood. Flood automatically appends
 an incremented integer to the end of the name, like "psr-authz 1", "psr-authz 2"
 etc. after each run.
   4. Everything else in the jenkins.sh file is parameterized or by necessity 
hard coded. Parameters will be provided by your Jenkins job. Look for the 
"flood_files" lines like this:

              `-F "flood_files[]=@yourJMXfile.jmx" \`

5. Commit and push your changes to your psr-yourtest repository.

6. Navigate to your Github repo in a browser. Click on the "Settings" tab
near the right side of the page. In the left hand menu, click on "Collaborators
and teams". In the "Teams" section, click on the "Add a team: Select team"
button and choose "platform-qa" to give the rest of the team access to the
repo. Then choose "platform-buildusers" to give Jenkins access too.

**SET UP AN EXISTING PSR REPO**

1. Alternatively if you have an existing repo, add scripts/jenkins.sh to your
repo.
2. Edit jenkins.sh file as described above.
3. Edit the package.json file as described above. 
4. You may need to npm init your repo if you do not already have a package.json 
file. 
5. Do not forget to npm install the dependencies listed in the psr-jenkins-setup 
package.json (e.g. superagent). It is required for your Jenkins job to run successfully.

**SET UP A JENKINS JOB**

1. In a browser, navigate to the Jenkins website at [https://build.rbcplatform.com/](https://build.rbcplatform.com/)

2. Near the upper left hand corner of the page click the "New Item" link.

3. At the top of the page, name your Jenkins job e.g. psr-yourtest.

4. At the bottom of the page, use the "Copy from" field to copy the psr-jenkins-setup 
job and then click "OK".

5. On the new page, change the info in the description field as needed.

6. Below that you will see a checked box labeled "This project is parameterized". 
   1. FLOOD - password parameter - that contains the Flood API token. This
 will probably never change.
   2. Threads - string parameter - enter the number of threads required by your
 PSR test.
   3. Rampup - string parameter - enter the test rampup time in SECONDS.
   4. Duration - string parameter - enter the test duration in SECONDS.
   5. Nodes - string parameter - enter the number of Flood nodes needed for
 your PSR test.
   6. Stop_grid_after - string parameter - enter the grid time to run your Flood
 nodes in MINUTES.

7. In the "Source Code Management" section change the Repository URL to point
to your Github repo.

8. In the "Build Environment" the NodeJS Installation should already be set.

9. In the "Build" section you should see an Execute Shell step that will run
the jenkins.sh file, install npm dependencies, and npm run jenkinsJS will
run the test/jenkins.js code that your package.json is pointing to.

10. In the "Post-build Actions" section you should see 2 steps. 
    1. The Slack Notifications step notifies in the PSR and SF-Maintenance Slack 
 channels when a PSR test is starting. Click on the "Advanced.." button to 
 edit the custom message that displays when your test runs.
    2. The Editable Email Notifications step is configured to send an email to
 the PSR and SF-Maintenance Slack channels notifying on completion of your 
 PSR test. Edit the message as necessary.

11. Click "Save" in the lower left corner of the page.

12. To test your job, click "Build with Parameters" in the left hand menu.
You should see the 6 parameter fields discussed in step 6. Verify that the
values are correct, then click "Build". 

13. On the left side of the page, click on the flashing ball to view the job
run.

14. Monitor the Console Output for error messages.

15. In a browser, navigate to Flood.io and check the project you assigned
your test to. Click "Select Last Flood". This should take you to your test,
which probably has not begun yet. Check the Flood grids page to see if your
grid/node(s) are starting up.

16. Once the test has started, go back to Jenkins and you should see a link
to your Flood test in the Console Output.

17. Check the PSR and SF-Maintenance channels to verify that the test start
notifications are there.

18. Once the test has finished on Flood, check the PSR and SF-Maintenance 
channels to confirm that the test finish notifications are there.
