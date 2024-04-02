# Profiling Node App: Detect the memory uses of node app | Use of -inspect | Heapdump | Heap Snapshot (Ref: https://medium.com/@svsh227/profiling-nodejs-application-detect-the-memory-uses-of-node-app-use-of-inspect-d3f6a723c513 )


Today, We’ll learn how we can know our node app better, and how to detect how much memory our app is consuming.
So let’s follow the below steps.

Step 1: Let’s create a basic node.js application, create two files with the name app.js and package.json:

package.json

{
"name": "heap-snapshot",
"version": "1.0.0",
"private": true,
"scripts": {
       "start": "node --inspect app.js"
  },
"author": "Shubham Verma",
"license": "",
"dependencies": {
                 "express": "^4.14.1",
                 "fast-levenshtein": "^2.0.6"
     }
}
app.js:

const express = require('express');
const console = require('console');
const levenshtein = require('fast-levenshtein');
//When set to 100 it should be the only visible operation
const HOW_OBVIOUS_THE_FLAME_GRAPH_SHOULD_BE_ON_SCALE_1_TO_100 = 10;
const someFakeModule = (function someFakeModule () {
return {
 calculateStringDistance (a, b) {
 //Here's where heavy sunchronous computation happens
 return levenshtein.get(a, b, {
    useCollator: true
  })
 }
 }
})()
const app = express();
app.get('/', (req, res) => {
   res.send(`
             <h2>Take a look at the network tab in devtools</h2>
             <script>
                  function loops(func) {
                        return func().then(_ => setTimeout(loops,        20, func))
                 }
                 loops(_ => fetch('api/tick'))
            </script>
           `)
 });
app.get('/api/tick', (req, res) => {
                   Promise.resolve('asynchronous flow will make our stacktrace more realistic'.repeat(HOW_OBVIOUS_THE_FLAME_GRAPH_SHOULD_BE_ON_SCALE_1_TO_100))
.then(text => {
                 const randomText = Math.random().toString(32).repeat(HOW_OBVIOUS_THE_FLAME_GRAPH_SHOULD_BE_ON_SCALE_1_TO_100)
                 return someFakeModule.calculateStringDistance(text, randomText)
  })
   .then(result => res.end(`result: ${result}, ${arr.length}`))
 });
app.get('/api/end', () => process.exit())
app.listen(8080, () => {
   console.log(`go to http://localhost:8080/ to generate traffic`)
})
Step 2: Go to the app location and run below command

npm install

Terminal snapshots for npm install
Step 3: After the successful installation, run bellow command:

node --inspect app.js

Terminal snapshots for node — inspect app.js
Step 4: Be careful at this step, after running the `node — inspect app.js` command you can see an URL start with ‘ws’, just like in above snapshot ‘ws://127.0.0.1:9229/a1c6b690-d8ef-4da6–9a50–23118d400640’.

Now what you need to do is, you need to make an URL like this:

devtools://devtools/bundled/js_app.html?experiments=true&v8only=true&ws=127.0.0.1:9229/a1c6b690-d8ef-4da6–9a50–23118d400640

Step 4: Now, open the above URL in the browser, make sure URL is correct, otherwise, you will get the following error:


Error snapshots
( Suggestion: make your URL into browser’s address bar )

If your URL is correct you can see below the page in your browser:


Snapshot of correct URL in the browser
Step 5: Click on the link ‘http://localhost:8080/’, it will open a tab with URL ‘http://localhost:8080/’ ( you can directly open the URL `http://localhost:8080/` into browser )

Step 6: After clicking/opening URL `http://localhost:8080/` you can see the below page, also open network tab in dev tools.


You can see there are many requests are hitting by the browser.
if not then check all the steps again ( you are doing wrong somewhere )
If yes then move to the next step.

Step 7:Now open the dev tool tab ( which is previously opened in step 4 ) Click on “Memory”, Select “heap snapshot” under the “select profiling type” and click on the below button “Take snapshot”.


Step 8:After clicking the “Take snapshot”, you can see there is a snapshot taken under the “profiles-> HEAP SNAPSHOT”. Now click on that “snapshot 1".



Step 9:After clicking on that “Snapshot 1”, You can see that each and every object created for this app.
* You can also see the num of that variable which is created to run this app.


* You can see how much memory is being used for this app.


You can see in the above snapshot, the app is consuming total of 8.3 MB memory.

* You can see the “Shallow size” of that kind of variables.


* You can see the “Retained Size” of that kind of variables.


You can take multiple snapshots and compare each and everything by clicking on the left button as shown in the below snapshot:


The best use of it:

You can take snapshots before hitting an API and after hitting “N” API, Now you can compare the differences between. You can check where you need to improve.
In the next blog, I will tell you how you can detect the memory leaks in your node app and how you can get the execution time of each function in your node app, also I will tell you how you can create the “flame chart” for your node app.

Congratulations… You are becoming an expert in node.js.
Thanks for reading…
Keep reading… Keep getting…


# Deep Dive
https://medium.com/performance-engineering-for-the-ordinary-barbie/intro-to-memory-profiling-chrome-devtools-memory-tab-explained-5a99d3ba85d2 
