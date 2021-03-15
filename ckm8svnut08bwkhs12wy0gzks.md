## Deploying a Node / Express Backend to Heroku

A quick guide to assist in getting our group project deployed to each member's individual Heroku apps. This guide is step 1; to deploy your React frontend to Heroku,  [checkout my part 2 post](https://benhammond.tech/deploying-our-react-team-project-to-heroku) . 

### These were the issues causing failed deploys:

- the .git repo and the package.json need to both be on the floor of your project. I copied all the files out of the inner folder using ```cp -r /gigboard-Api/. /```, and then deleted the inner folder. Command courtesy of [this Stack Overflow](https://stackoverflow.com/questions/20192070/how-to-move-all-files-including-hidden-files-into-parent-directory-via)
- the PORT needed to be assigned dynamically: Instead of ```PORT = 3001```, we needed to use ```const PORT = process.env.PORT || 5000``` which lets Heroku choose the correct port, and our local machine to use 5000. I chose 5000 here because that seems to be where ```heroku local web``` (an alternative to running ```nodemon```) is looking by default.
- the ```Index.js``` model needed to be lowercase ```index.js``` to be able to reference it elsewhere by using ```../Models/```      It doesn't seem to recognize the uppercase ```Index``` as being the default file in that folder. I needed to fix using the weird capitalization trick, naming it a unique 3rd weird name (I used ```XXXindex.js```), committing, and then changing back to the proper capitalization (```index.js```) and committing. Read more about this bug and how to fix it in [this blog post](https://benhammond.tech/dont-change-the-capitalization-of-your-filenames)
- we needed to merge develop into main. I did that on GitHub, but that will need to be done again every time we want the new changes reflected on the deployed web app. 

### To deploy the backend to your Heroku

_first in browser_

- visit [heroku.com/apps](https://dashboard.heroku.com/apps) and login if needed
- click dropdown "new" and then "create new app"
- choose an available name that no one else on heroku has used; i used "ben-gigboard" and then click "create app"
- click "deploy" option in the nav bar
- mostly follow the *Deploy using Heroku Git* section, but I'll spell out the changes below.

_then in VSCode_

- open your ```package.json``` and scroll until you find the *scripts* entry:
```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
},
```

I am not yet implementing any tests, so I just replaced the test key:value line with a line that will point Node to our main file ```server.js```. 
```
"scripts": {
    "start": "node server.js"
},
```

If you wanted to include multiple lines you could, just be sure each key:value pair is separated by a comma. *Important*: since this is a *.json* file, it seems you can NOT have a trailing comma, as you might have had in a regular JavaScript object. 
- In the same way, be sure to keep that comma after the scripts object closing squirrely brace: ```}``` if there are more entries in your ```package.json```, (there probably are). 
- Also important: do not place any comments  inside of these .json objects, you'll have a bad time.

_lastly in your command line_

- navigate to your backend folder, and make sure you're on your main branch.
- ```git pull```
- ```npm i``` to make sure all your node modules are installed
- ```heroku login```, press any button and then log in with the browser that pops up, then close that browser window
- do a local test run with ```heroku local web``` and then visit the localhost that it starts running on in your browser; in this case it chose *localhost:5000*. This is very similar to just running ```nodemon```. Don't worry about that circular dependency warning; apparently it's not an issue. 
![Screen Shot 2021-03-14 at 12.17.24 AM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615706287965/5bPkc9Kvk.png)

- ```heroku git:remote -a ben-gigboard``` and replace ```ben-gigboard``` with whatever the exact name of your Heroku app is
- ```git add .```
- ```git commit -am "make it work please"```
- ```git push heroku main```
- cross your fingers and wait until you hopefully see ```remote: Verifying deploy... done```
- type ```heroku open``` to launch that sweet backend in your browser, and you should see "hello world" displayed
